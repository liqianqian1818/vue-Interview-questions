vue训练营面试第二题：Vue组件data选项为什么必须是个函数而Vue的根实例则没有此限制？
答：组件的配置（options）和实例（instance）是需要分开的。最根本原因是js对于对象（以及数组等）是传引用的，因为如果直接写一个对象进去，那么当依此配置初始化了多个实例之后，这个对象必定是多个实例共享的。

组件（Component）定义方式
组件在定义的时候，可以全局（Vue.component()）或者局部注册
new Vue({
  // ...
  components: {
    // <my-component> 将只在父模板可用
    'my-component': Child
  }
})
两种方法并没有本质区别，都需要在data属性里传入对象。局部注册只是放在了new Vue的options处理部分，仍然是Vue.extend(definition)里判断。
下面以全局注册为例过一遍Vue源码。
前面说的报错位置在这里
strats.data // vue-template-compiler/build.js
...
if (typeof childVal !== 'function') {
    "development" !== 'production' && warn(
      'The "data" option should be a function ' +
      'that returns a per-instance value in component ' +
      'definitions.',
      vm
    )
    return parentVal
}
...
这个函数简单来说，是负责data字段内容处理的，不管是new Vue的参数里data还是组件初始化的data，都要经过这里。

简单起见，从这个位置往上倒（二声）到开头：

initGlobalAPI (Vue)
-function initAssetRegisters (Vue)
...
到这一步结束，按照_assetTypes（包括'component','directive','filter'）挂载方法。这里挂载了Vue.component的初始化方法，但还没调用。

经过一众其他内部操作。直到执行我们的代码（组件是从官方文档抄过来的）

Vue.component('button-counter', {
  data: function () {
    return {
      counter: 0
    }
  }
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
 
-Vue.extend(definition)
definition内容：

{
  data: function () {...},
  "template": "<button v-on:click=\"count++\">You clicked me {{ count }} times.</button>",
  "name": "button-counter"
}
写入组件的配置：
Sub.options = mergeOptions(Super.options,  definition)
-mergeField('data')
-strats.data
这一步就是前面报错的那一步，会判断data是否为函数，是则执行并挂载函数方法。否则返回父级属性。

vue-component-config

组件挂载结果
看起来3个_assetTypes加了s，是在代码里搞的

config._assetTypes.forEach(function (type) {
  strats[type + 's'] = mergeAssets
})
在实际使用时，才会初始化组件，即调用

function VueComponent (options) {
      this._init(options)
}
初始化Vue实例时的处理
new Vue的时候，也会调用mergeOptions，不同的是这时候传入了vm实例。这时在mergeField('data')里走了另外一条路线：

return function mergedInstanceDataFn () {
// ...
    var instanceData = typeof childVal === 'function'
        ? childVal.call(vm)
        : childVal
// ...
}
