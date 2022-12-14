[TOC]
### 简介
tv-focusable 是适用于在 TV 端进行网页开发时管理焦点移动的框架，以简洁的 Api 让前端网页开发就像 android 开发一样自动管理焦点。

### 安装
```
npm i vue-tv-focusable
```

### [案例展示](https://slailcp.github.io/focusable/example/index.html)
[案例展示](https://slailcp.github.io/focusable/example/index.html)


**效果示例**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201016100758392.gif#pic_center)

### 如何使用
**vue2.x  main.js中添加如下代码**
```js
import Vue from "vue";
import App from "./App.vue";

/*+++++++++添加下面两行+++++++++*/
import focusable from 'vue-tv-focusable';
Vue.use(focusable);
/*+++++++++添加结束+++++++++*/

Vue.config.productionTip = false;
new Vue({
    render: h => h(App)
}).$mount("#app");

```
**vue3.x**
```js
// main.js中添加如下代码
import { createApp } from 'vue'
import App from './App.vue'

import focusable from 'vue-tv-focusable'
createApp(App).use(focusable).mount('#app')


//setup()中如何获取$tv对象
import { getCurrentInstance } from 'vue'
setup() {
    const { proxy, ctx } = getCurrentInstance(); 
    // proxy.$tv.xxx
    proxy.$tv.focusClassName = 'focus123';
    proxy.$tv.requestFocus(proxy.$tv.getElementByPath('//div[@class="demo"]/span[18]'));
    ...
}
```

#### 1.设置元素可获取焦点
```html
<div v-focusable>可获取焦点的元素</div>
<div>不可获取焦点的元素</div>

// 1.1.0版本及其以上支持
<div v-focusable="true">可获取焦点的元素</div>
<div v-focusable="false">不可获取焦点的元素</div>
```
跟 android 的很相似了吧~
给非 android 开发的同学普及一下，在进行 android 的 TV 端开发时，系统是会自动给 focusable=true 的元素分发焦点的，例如：
```xml
    <TextView
        android:id="@+id/text1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:focusable="true" />
    <TextView
        android:id="@+id/text2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:focusable="true" />
```
假设这两个元素是左右排列的，当焦点在前面一个 text1 上时，按遥控器的右键，焦点会自动跑到 text2 上。
现在在网页标签上设置 v-focusable 就相当于 android 上设置了 android:focusable="true"，当用户按遥控器时，该库会像 android 那样自动把焦点移动到下一个焦点上，无需开发者处理。

#### 2.设置焦点的样式
聚焦的元素会被加上一个 `class="focus"` (你可以自己定义 className，默认为 focus)
现在你应该知道要做什么了，给 focus 设置个独特的样式就好了。
例如这样：
```js
    <div class="demo">
    <div class="item" v-focusable v-for="index in 10" :key="index">
        {{ index }}
    </div>
    </div>
...
.focus {
    transform: scale(1.1);
    border: 2px solid red;
    box-shadow: 0 0 20px red;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201013104506375.gif#pic_center)

好了，这样就设置完了，现在你按方向键时焦点就会自动移动到下一个目标元素上了，很简洁吧~

#### 3.指定焦点移到到某一个元素上
上面的演示中最开始时第一个元素获取了焦点，实际上你在使用时，需要你自己来指定让哪个元素聚焦，这个由用户自己来指定似乎更加合适，你可以用下面的方法来指定焦点移动到某个元素上（如果不想要滚动的动画，则添加一个参数 默认为true需要动画，false则是不需要动画）：
```js
this.$tv.requestFocus(Element,bool); // Element;必填 bool非必填。默认为true
```
例如：
```js
<div class="item" ref="myDiv">s</div>
...
export default class Example1 extends Vue {
    created() {
    this.$nextTick(() => {
        // @ts-ignore
        this.$tv.requestFocus(this.$refs.myDiv)
    })
    }
}
```


至于如何获取 Element 就不再展开了，你可以用上面示例中 vue 提供的 $refs 方式，也可以用 xpath 等很多方式：
```js
// 让第2个div 聚焦
this.$tv.requestFocus(this.$tv.getElementByPath('//div[@class="demo"]/div[2]'));
```
一般 TV 端的页面在刚加载完成时，产品都会要求让某一个元素聚焦的，这时你就用上这条 api 了。
**注意：** 进入页面时想让某个元素聚焦，要在 nextTick 回调中进行，否则会因页面未加载完全而导致焦点移动并不如你所愿。

#####  XPath获取DOM—getElementByPath(string)
获取 Element的,vue-tv-focusable提供了xPath的方式获取
```js
<body>
    <div class="content">
        <span class="item1">1</span>
        <span class="item2">2</span>
        <span class="item3">3</span>
    </div>
</body>
this.$tv.getElementByPath('//div[@class="content"]/span[2]');
this.$tv.getElementByPath(this.$tv.readXPath(document.querySelector('.item2')));
//结果: <span class="item2">2</span>
```
##### 根据DOM反向获取XPth路径  —readXPath(el)
vue-tv-focusable根据DOM反向获取XPth路径
```js
<body>
    <div class="content">
        <span class="item1">1</span>
        <span class="item2">2</span>
        <span class="item3">3</span>
    </div>
</body>
this.$tv.readXPath(document.querySelector('.item2')) 
this.$tv.readXPath(this.$tv.getElementByPath('//div[@class="content"]/span[2]');) 
//结果: "/html/body/div[1]/span[2]"
```

#### 4.自定义焦点移动
框架有自己的一套找焦点算法，但有时产品要求你在某个元素上按遥控器右方向键时，让屏幕左下角的某个该死的元素聚焦，那你就要根据产品需求文档来自定义焦点移动了。
可以使用 @up，@right，@down，@left 这几个属性来指定按遥控器对应方向键时调用的方法，然后自己在方法里通过 requestFocus 让目标元素聚焦，如果你在自己的方法里调用了 requestFocus 方法，该库将不再执行默认的焦点移动操作，否则依然帮你执行默认的焦点移动。
示例：
```js
<div class="focus-item" v-focusable v-for="index in 1000" :key="index" @down="down(index)">
...
    down(index: number) {
    if (index === 2) { //在第2个div上按下方向键时，焦点直接移动到第30个div上
        this.$tv.requestFocus(this.$tv.getElementByPath('//div[@class="wrapper"]/div[30]'));
    }
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020101310444651.gif#pic_center)
#### 5.表单控件，按确定键进行填写,
```js
// formAutofocus：默认true
    this.$tv.formAutofocus=false；// 不可以输入
    this.$tv.formAutofocus=true；// 可以输入 
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/6221fa03b653411f9d5a91e37bd2aa6f.gif)



好啦，到这里应该能满足基本所有的 TV 焦点需求了。





你还在看？？？
好吧，我猜你可能还有一些别的需求~
### 其它
#### 1.提一下初始化
您可以设置全局设置或单页设置
```js
    // 初始化配置
    // 全局配置可在mian.js中，也可以放在APP.vue中
    const vm = new Vue();
    // 1.init初始化（init的所有配置项可以在最后面的表格中查看）
    vm.$tv.init({
    focusClassName: "on-focus", // 聚焦元素的className （默认focus）
        KEYS: {
        KEY_LEFT: [37, 21], 
        KEY_UP: [38, 19], 
        KEY_RIGHT: [39, 22], 
        KEY_DOWN: [40, 20]，
        KEY_ENTER: [40, 20]
        }, // 自定义键值
        longPressTime: 800, // 长按响应时间（单位：毫秒），默认500ms
        distanceToCenter: false // 是焦点始终在可是范围的中间部分， 默认false
    });


// 2.独立配置初始化
    vm.$tv.KEYS=  {
        KEY_LEFT: [37, 21], 
        KEY_UP: [38, 19], 
        KEY_RIGHT: [39, 22], 
        KEY_DOWN: [40, 20]，
        KEY_ENTER: [40, 20]
    };  
    vm.$tv.longPressTime= 800;  
```
警告：为了不影响全局配置，如果对页面进行了一些配置，请记住在销毁页面时重置它
```js
created(){
    this.$tv.findFocusType  = 0;
}
destroyed() {
    this.$tv.resetFindFocusType(); //this.$tv.findFocusType  = 1;
}
```
如果你有定制一些参数的需求，可以通过初始化设置一下这些参数，否则无须初始化。
下面是参数的一些说明：
##### 1.自定义焦点 class（focusClassName）
你接手了别的组的项目，他们已经把 `focus` 这个词作为普通 className 给用了，而且很多地方都用到了，你一定不愿意把他们的代码里的 focus 改成 focus1,focus2 什么的，以使你可以正常使用该库的 focus 这个名字作为焦点样式。
那就通过初始化里的 focusClassName 属性重新定义一下该库的焦点样式名吧。

##### 2.自定义键值（KEYS-配置上下左右，获取失去焦点，enter事件，长按事件）
不同厂家的遥控器键值可能不一样，有些并未按照 android 键值来实现，而且产品可能要求同时支持遥控器和键盘操作，这时你就需要指定上下左右方向键值了，例如上面配置的 KEY_LEFT: [37, 21] 就是支持键盘上的左箭头键和遥控器左方向键。

##### 3.自定义找焦点方式（findFocusType，initDis）
findFocusType=1，即默认找最近焦点方式，用默认方式时实际上用不到 initDis 参数
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201013104411514.gif#pic_center)

findFocusType=0，initDis = 49
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201013104423300.gif#pic_center)

initDis 为何是 49？只要下一个 div 的横向中心线和当前焦点元素的中心线距离大于 initDis 值，就认为它和当前焦点不在同一水平线上，现在 div2 的横向中心线比 div1 高了 50px，所以如果你不想让用户在 div1 上按右方向键时焦点跑到 div2 上，就可以设置找焦点方式为直线模式，并通过 initDis 来控制偏差范围。
##### 4.边缘距离（offsetDistance）
到达边缘的时候,给焦点和边缘一个距离.
```js
this.$tv.offsetDistance = 50 // 到达边缘的时候距离边缘50像素
this.$tv.offsetDistance = 250 // 到达边缘的时候距离边缘250像素
```
如图:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110110738158.gif#pic_center)



#### 2.局部滚动（setScrollEl/resetScrollEl）   
TV 端网页大多是整个页面滚动，滚动条在浏览器窗口上，如果你的滚动内容是在整个页面的局部某一块，需要指定一下滚动容器。
**setScrollEl(Element): 设置滚动的el
**resetScrollEl()： 重置el，即滚动浏览器的滚动条
举个栗子
```js
<div class="demo">
        <div class="wrapper">
        <div class="focus-item" v-focusable v-for="index in 300" :key="index">
            {{ index }}
        </div>
        </div>
</div>
<script>
    ...
    created() {
        this.$nextTick(() => {
            this.$tv.scrollEl = this.$tv.getElementByPath('//div[@class="demo"]')
            //this.$tv.setScrollEl(this.$tv.getElementByPath('//div[@class="demo"]'));
        })
    }
    destroyed() {
        // To avoid affecting the global configuration, reset the page when destroying it
        this.$tv.resetScrollEl();
    }
    ...
</script> 
<style>
    .demo {
    background:#ccc; margin: 0 auto; width: 400px; height: 400px; 
    position: relative; padding: 20px; overflow: hidden;
    .wrapper { width: 800px; }
    .focus-item {...}
    }
</style>
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201013104345818.gif#pic_center)


#### 3.监听焦点状态
@onFocus：获得焦点
@onBlur：失去焦点
```js
<div class="focus-item" v-focusable v-for="index in 300" :key="index" @down="down(index)"
                @onFocus="focus(index,$event)" @onBlur="blur(index)">
            {{ index }}
        </div>
...
    focus(index: number, event: any) {
    console.log(event.detail.el); //当前元素
    console.log('focus:' + index);
    }

    blur(index: number) {
    console.log('blur:' + index);
    }
```
不需要获取当前元素就不用传 $event 了。

#### 4.长按
@longPress
```js
<div class="focus-item" v-focusable @longPress="longPress()">11</div>
...
    longPress() {
    console.log('longPress'); // 长按超过500ms自动打印出来哦，可通过$tv.init.longPressTime配置时间哦
    }
```
#### 5.重置单个配置/重置
```js
// 重置成插件默认值，reset+属性名()，例如
this.$tv.resetScrollEl();
this.$tv.resetFocusClassName();
this.$tv.resetFindFocusType();
this.$tv.resetInitDis();
this.$tv.resetKEYS();
this.$tv.resetLimitingEl();
...

// 独立配置init里面的每一项，$tv.属性名=xx, 如下：
this.$tv.focusClassName = XX;
this.$tv.KEYS = {
        KEY_LEFT: [XX,XX,XX],
        KEY_UP: [XX],
        KEY_RIGHT: [XX, XX],
        KEY_DOWN: [XX],
        KEY_ENTER:[XX]
    };
this.$tv.offsetDistance = XX;
this.$tv.longPressTime = XX;
...
```

#### 6.清除所有配置并恢复到插件的默认配置
```js
this.$tv.reset()
```
#### 7.所有可配置项
初始化配置init


init(...)

| name| 描述 | 默认值  | 是否必须 |
|--|---|-|--|
| ~~focusableClassName~~ |~~可聚焦的classname,(设置了focusable的时候不需要设置此项)~~  |~~空字符串~~  | no |
| focusClassName | 指定焦点元素类名。默认为“焦点” "focus" | focus | no |
| initDis  | 差值| 20| no |
| findFocusType  | 移动类型，1：就近查找  0：直线 查找  | 1 | no |
| KEYS  | 上下左右的键值  | {         KEY_LEFT: [37, 21],         KEY_UP: [38, 19],         KEY_RIGHT: [39, 22],         KEY_DOWN: [40, 20],         KEY_ENTER:[13, 23]     } | no |
| offsetDistance | 边缘距离 | 50 | no |
| longPressTime | 长按响应时间 | 500 | no |
| distanceToCenter| 焦点是否在滚动区域保持剧中显示  | false | no |
| limitingEl| 整个界面，只有limitingEl内的焦点可以聚焦| null| no |
| formAutofocus| 表单控件是否可以输入或者编辑  |true |  |


独立设置每一项

| name | 描述 | 使用  | 
|-|-|---|
| ~~focusableClassName~~ |~~可聚焦的classname,(设置了focusable的时候不需要设置此项)~~  | ~~$tv.focusableClassName= XX;~~  | 
| focusClassName  | 指定焦点元素类名。默认为“焦点” “focus”  | this.$tv.focusClassName = XX; | 
| itemAttrname |可聚焦的属性名  | this.tv.itemAttrname= XX; <br/>```获取所有可聚焦的dom属性 console.log($tv.itemAttrname)```| 
| findFocusType  | 移动类型，1：就近查找 0：直线 查找 | this.$tv.findFocusType = XX; | 
| initDis  | 差值  | this.$tv.initDis = XX; | 
| KEYS  | 上下左右的键值  | this.$tv.KEYS = {...}; | 
| offsetDistance  | 边缘距离 | this.$tv.offsetDistance = XX; | 
| longPressTime  | 长按响应时间  | this.$tv.longPressTime = XX; | 
| scrollEl  | 设置可以滚动的el  | this.$tv.scrollEl = XX; | 
| limitingEl  | 整个界面，只有limitingEl内的焦点可以聚焦  | this.$tv.limitingEl = XX; | 
| distanceToCenter  | 焦点是否在滚动区域保持剧中显示  | this.$tv.distanceToCenter = XX; | 
| formAutofocus| 表单控件是否可以输入或者编辑  | $tv.formAutofocus= XX; | 
| smoothTime| 动画时间  | $tv.smoothTime= 200; | 
| spacingTime| 动画流畅度(数值越小动画越流畅,但是越耗性能) ,当smoothTime设置为40时,spacingTime=20和spacingTime=1的效果是不一样的 | $tv.spacingTime = 20| 
| scrollSpeedX|当按键按下不抬起时水平方向上多少毫秒滚动一次，值越大越节省能，默认0，单位毫秒 | $tv.scrollSpeedX = 200| 
| scrollSpeedY|当按键按下不抬起时垂直方向上多少毫秒滚动一次，值越大越节省能，默认0，单位毫秒 | $tv.scrollSpeedY = 200| 
| scrollSpeed|当按键按下不抬起时水平/垂直多少毫秒滚动一次，值越大越节省能，默认0，单位毫秒 | $tv.scrollSpeed = 200| 



scrollEl

| name | 描述 | 类型  | 参数  | 参数是否必填 |
|-|-|---|--|------|
|  $tv.setScrollEl(el) /   $tv.scrollEl = el  | 设置滚动的el| Function /el| Element /无| 是  |
| $tv.resetScrollEl | 重置成浏览器滚动 | Function | 没有参数| --|

reset

| name  | 描述  | 类型| 参数 |
|-|---|----|-|
| ~~$tv.resetFocusableClassName()~~ | ~~重置 focusableClassName~~ | Function| 无 |
| $tv.resetFocusClassName()  | 重置 focusClassName| Function  | 无  |
| $tv.resetInitDis()| 重置 initDis | Function  | 无 |
| $tv.resetFindFocusType()| 重置 findFocusType | Function  | 无 |
| $tv.resetKEYS()| 重置 KEYS | Function  | 无 |
| $tv.resetOffsetDistance()| 重置 offsetDistance| Function  | 无 |
| $tv.resetLongPressTime()| 重置 longPressTime| Function  | 无 |
| $tv.resetDistanceToCenter()| 重置 distanceToCenter| Function  | 无 |
| $tv.resetScrollEl()| 重置 scrollEl | Function  | 无 |
| $tv.resetLimitingEl()| 重置 limitingEl | Function  | 无 |
| $tv.resetFormAutofocus()| 重置 formAutofocus| Function  | 无 |
| $tv.reset() | r重置 所有  | Function  | 无 |


other

| name | 描述  | 类型 | 参数 | 参数是否必填 |
|----|-|---|---|------|
| requestFocus  | 直接调指定的el | Function | Element:跳转的el, bool:是否启用动画 | Element:是,bool:否 |
| getElementByPath | 使用xPath获取el | Function | str| 是  |
| readXPath| 根据DOM反向获取XPth路径 | Function | el| 是  |

事件

| name | 描述  |
|----|-|
| up  | 上移 (可配置KEY_UP修改键值)  | 
| right | 右移  (可配置KEY_RIGHT修改键值)  |
| down | 下移 (可配置KEY_DOWN修改键值)|
| left | 左移 (可配置KEY_LEFT修改键值)|
| onFocus | 获取焦点  |
| onBlur | 失去焦点  |
| longPress | 长按 (可配置KEY_ENTER修改键值)， $tv.init.longPressTime可配置长按的时间，默认500ms  |
| click | 按enter时可触发click(可配置KEY_ENTER修改键值)|


## [案例源码请戳这里](https://github.com/slailcp/vue-tv-focusable-example)


# 常见问题 
## vite 报错 define is not defined
1.0.8以及以上版本兼容vite

## 如何控制滚动速度
当遥控器按键一直按着不松手，默认请情况下会滚动很快，想要控制滚动速度，这个时候可以设置scrollSpeed来控制滚动速度，scrollSpeed是以毫秒为单位，越大滚动越慢
```js
$tv.scrollSpeedX = 200; // 不设置的话，默认为0
$tv.scrollSpeedY = 500; // 不设置的话，默认为0

当水平和垂直方向速度一样的时候，可以这么设置
$tv.scrollSpeed = 200;
```
**注：当页面上的容器非常多的时候（几万个），长按上下左右键的时候会卡顿，这个时候将scrollSpeed 设置 合适的值会解决卡文问题；**

## 如何阻止焦点移动
当遥控器按下上下左右键的时候，事件会有一个event.target参数.这个event.target就是自身的意思。可以调用$tv.requestFocus(event.target);方法，只能固定在自身
```js
<div v-focusable class="div_item" @down="testDown($event)" >a</div>
<div v-focusable class="div_item" @down="testDown($event)" >b</div>
testDown(event){ // 按下键盘阻止焦点移动，各事件同理
    proxy.$tv.requestFocus(event.target); 
};
```
## 监听返回键问题
可以看下这篇文章：[移动端网页监听返回键](https://blog.csdn.net/Aislli/article/details/81178562)

## uniapp开发相关问题
uni-app 在非 h5 端开发时，会去掉浏览器自带的一些对象,所以需要先将项目打包成h5，然后再将h5转成apk才行，否则会报错，参考链接如下：                     
[在uni-app里使用tv-focusable打包apk时报错如何解决](https://blog.csdn.net/sllailcp/article/details/119939974)
[h5打包成apk](https://blog.csdn.net/sllailcp/article/details/118397743)

## 返回上一页，记录上一个页面已经聚焦的容器
可以使用$tv.readXPath(focusableEl);获取当前聚焦的el的xpath路径,如下：
```js
// 跳转页面的时候调用下面的方法保存当前焦点。
var xpathstr = this.$tv.readXPath(document.querySelector("[focused]"));
sessionStorage.setItem('xpath',xpathstr)

// 结果是类似  '//*[@id="app"]/div[2]/div[1]/div[2]/div[1]/div[31]'  的字符串，返回到上一个页面，可以使用requestFocus获取焦点
 this.$tv.requestFocus(this.$tv.getElementByPath(sessionStorage.getItem('xpath')))
```
**此处只是提供一个思路，如果你有更好的解决方案可以忽略；**

## keepalive 返回缓存的页面,调用requestFocus 无效 解决方案
先去掉页面上已经聚焦的focused,然后再调用requestFocus; 如下(**此处只是提供一个思路，如果你有更好的解决方案可以忽略；**):
```js
  beforeRouteLeave(to, from, next) {
    if(!document.querySelector("[focused]")) return;
    sessionStorage.setItem(
      "focusXpath",
      this.$tv.readXPath(document.querySelector("[focused]"))
    );

    // 去掉焦点,如果requestFocus(el)的这个el上存在焦点,则不会再次触发requestFocus
    document.querySelector("[focused]").removeAttribute("focused");
    next();
  },
  activated() {
    this.$nextTick(() => {
      this.$tv.distanceToCenter = true;
      this.$tv.scrollEl = document.querySelector(".wraper");

      // 设置焦点
      const focusXpath = sessionStorage.getItem("focusXpath");
      if (focusXpath && this.$tv.getElementByPath(focusXpath)) {
        this.$tv.requestFocus(this.$tv.getElementByPath(focusXpath),false); 
        sessionStorage.removeItem("focusXpath")
      }
    });
  },
```

## 如果你还有问题，可加入群聊交流讨论
Q群 377202993  备注: focusable  


# 版本升级日志
2022-07-28 1.1.1更新日志：
1.修复resetLimiting方法无效问题（focusable-core@1.1.2）
2.增加v-focusable指令动态控制 eg:v-focusable="bool" (vue-tv-focusable@1.1.1)
3.修改focusableClassname,对应的标签同步更新focusable属性 （focusable-core@1.1.2）

2022-02-14 1.0.8更新日志：
兼容vite

2021-12-18 1.0.4更新日志：
当一直按键不抬起时，控制每一次焦点的获取时间，从而达到控制滚屏速度

 2021-10-28 1.0.3更新日志：
1、禁止隐藏元素聚焦；
2、开放滚动动画执行时间自定义，默认 200ms；
3、开放滚动动画循环的次数，默认 20次(注意:数值越小越耗性能)； 
4、优化其它不影响使用的小逻辑；

2021-09-27 1.0.2更新日志：
修复 vue3.x 延时加载元素的情况下，自定义事件不触发的问题；

2021-09-10 1.0.1更新日志：
1、防止和三方 UI 库类命名冲突，导出自定义 itemAttrname；
2、优化聚焦逻辑，禁止 svg 元素聚焦；
3、禁止焦点嵌套情况，父子元素同时设置可聚焦时只允许父元素聚焦；

2021-07-24 1.0.0更新日志：
1、优化对 angular 的支持；
2、稳定版本。

2021-07-24 0.0.6更新日志：
1、增加 formAutofocus 配置指定表单是否可执行原生聚焦（输入状态）；
2、开放 focusableClassName 的配置（原则上是开放给react用的），如果不明白这个是做什么用的，说明你不需要用到这个；

2021-07-16 0.0.5更新日志：
增加 readXPath 接口，便于读取目标元素的 xpath 路径，可用于需要序列化标签存储的场景；

2021-04-09 0.0.4更新日志：
1、增加 init 属性的独立配置项，可单独设置某个属性值；
2、增加 limitingEl 逻辑，可限制页面上只有某个容器内的元素可聚焦；

2020-12-24 0.0.3更新日志：
1、增加滚动 center 模式，通过 distanceToCenter 控制；
2、优化聚焦算法；

2020-12-11 0.0.2更新日志：
增加 TV 端长按事件的支持；

2020-12-09  0.0.1更新日志：
第一次发版.



