# sell

> sell app

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).



### 项目展示： 

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml13824\wps1.jpg)![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml13824\wps2.jpg)![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml13824\wps3.jpg)![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml13824\wps4.jpg)![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml13824\wps5.jpg) 



### 过程


1. components 替换 ./components
    在build\webpack.base.conf.js
    resolve:{
    alias: {
        'components': resolve('/src/components'),
    }
    }

2. 修改.eslintrc.js rules规则
    always：强制遵守 （默认值）
    never：不允许
    ignore：可无视也可遵守 （只能用于单独的对象）
    0：不检测

3. 路由设置
    在 src\router\index.js -> routes
    高亮解决方案
    a, .router-link-active
          color : red
    b,linkActiveClass: 'active' // .router-link-active -> active

4. 1像素border的实现
    在 src\common\stylus
    添加 mixin.styl 通过border-lpx带参$color,及伪类&: after 实现
    兼容问题
    在 src\common\stylus\base.styl
      基于1.5x 2.0x dpi的窗口做适配
    出口文件：index.styl

App.vue
  display: block; // 使a标签可以撑满整个div


header组件的设计
  在 App.vue 中通过 vue-resource 异步请求seller数据, 在其子组件header中 通过props 获取父组件中的seller数据

  嵌套子组件: details
  抽象公共组件: bar, star

  坑：v-if="seller.supports"
    因为要用到seller.supports[], 而seller是通过异步请求过来的, 可能在html渲染时还没到, 导致出现"TypeError: Cannot read property '0' of undefined"
  组成：
    额外引入了details组件, 并制作了star组件
  注意:
    v-for从2.x开始需要指定key key必须唯一,如果没有唯一的key,可以使用索引idx


goods组件的设计
  结构
    分为左侧menuWrapper和右侧goodsWrapper以及底部组件shopcart

  下划线依然使用的是mixin的border-1px

  Scroll的实现
    引用了"better-scroll": "^1.15.2"
    vue1.x中使用v-ref or v-el:x 关联DOM x节点 然后使用this.$els.x 调用, 先已合并为了ref:x 并通过this.$refs 调用

    better-scroll的坑 
      better-scroll实现原理是监听了一些 touchstart,touchend事件
      他会prevent default 阻止掉默认的
      解决：
        在 new BScroll(this.this.$refs.menuWrapper, {
            click: true // 派发点击事件
          });
      坑：某些PC端或android的touch没有被阻止,导致两次点击
      解决：
          if (!event._constructed) {
            return
          } // 原生浏览器上没有这个属性, 则使用bscroll派发的点击时间时 return掉

  问题：
    在created中调用了操作DOM的方法
      必须包函在$nextTick的回调函数中
      官网解读:
        Vue 异步执行 DOM 更新。只要观察到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作上非常重要。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作。Vue 在内部尝试对异步队列使用原生的 Promise.then 和MessageChannel，如果执行环境不支持，会采用 setTimeout(fn, 0)代替
        例如，当你设置vm.someData = 'new value'，该组件不会立即重新渲染。当刷新队列时，组件会在事件循环队列清空时的下一个“tick”更新。多数情况我们不需要关心这个过程，但是如果你想在 DOM 状态更新后做点什么，这就可能会有些棘手。虽然 Vue.js 通常鼓励开发人员沿着“数据驱动”的方式思考，避免直接接触 DOM，但是有时我们确实要这么做。为了在数据变化之后等待 Vue 完成更新 DOM ，可以在数据变化之后立即使用Vue.nextTick(callback) 。这样回调函数在 DOM 更新完成后就会调用。

  shopcart组件的设计
    使用flex布局 左侧1 右侧固定105px

    在 src/components/shopcart/shopcart.vue 遇到异常: [Vue warn]: Error in render: "TypeError: Cannot read property 'forEach' of undefined"
      明明已经指定了selectFoods为Array类型, 却无法使用数组的forEach?
      解决方案1：
        在forEach外包裹一次判断
          if (this.selectFoods) {
            this.selectFoods.forEach((food) => {
              total += food.price * food.count
            })
          }
      解决方案2：
        在props中写明default 返回 []
          selectFoods: {
            type: Array,
            default() {
              return []
            }
          },
    
    注意：count不是foods原有属性, 而是拟造的
    
    box-shadow
    
      /* x偏移量 | y偏移量 | 阴影模糊半径 | 阴影扩散半径 | 阴影颜色 */
      box-shadow: 2px 2px 2px 1px rgba(0, 0, 0, 0.2);
    
      /* 任意数量的阴影，以逗号分隔 */
      box-shadow: 3px 3px red, -1em 0 0.4em olive;
    
    v-show="food.count>0" 未执行问题
      对vue观测对象中不存在的字段进行操作是无效的
      vue的default.property是监控不到改字段的
      即使 if (!this.food.count) this.food.count = 1
      通过js渲染到html,能够在控制台console.log(this.food.count);
    
      解决方案:
        引入 import Vue from 'vue'
        并使用其api set(object, key, value)进行注册
        if (!this.food.count) Vue.set(this.food, 'count', 1)
    
      原理：
        是在VueConstructor<Vue>中构造一个新的字段然后进行监听

  核心难点：组件通讯
    详细可见./study.text

- [x] 已实现：
    添加goods到购物车的小球动画

