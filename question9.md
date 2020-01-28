vue面试第九题：谈谈你对vue组件之间通信的理解？
组件通信常用方式
props eventbus vuex 自定义事件
边界情况
$parent $children $root
$refs provide/inject
非prop特性 $attrs
$listeners
组件通信
props
父给子传值
         
// child
props: { msg: String }
// parent
<HelloWorld msg="Welcome to Your Vue.js App"/>
自定义事件
 子给父传值
  // child
this.$emit('add', good)
// parent
<Cart @add="cartAdd($event)"></Cart>
事件总线
任意两个组件之间传值常用事件总线 或 vuex的方式。
  // Bus:事件派发、监听和回调管理 class Bus {
  constructor(){
    this.callbacks = {}
  }
  $on(name, fn){
    this.callbacks[name] = this.callbacks[name] || []
    this.callbacks[name].push(fn)
  }
  $emit(name, args){
    if(this.callbacks[name]){
      this.callbacks[name].forEach(cb => cb(args))
    }
} }
// main.js
Vue.prototype.$bus = new Bus()
// child1
this.$bus.$on('foo', handle)
// child2
this.$bus.$emit('foo')
  实践中通常用Vue代替Bus，因为Vue已经实现了相应接口
vuex
创建唯一的全局数据管理者store，通过它管理数据并通知组件状态变更。 组件通信最佳实践，预习视频第12章
$parent/$root
兄弟组件之间通信可通过共同祖辈搭桥，$parent或$root。
  // brother1
this.$parent.$on('foo', handle)
// brother2
this.$parent.$emit('foo')

$children
父组件可以通过$children访问子组件实现父子通信。
注意:$children不能保证子元素顺序 $attrs/$listeners
包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 ( class 和 style 除外)。当一个组件没有 声明任何 prop 时，这里会包含所有父作用域的绑定 ( class 和 style 除外)，并且可以通过 v- bind="$attrs" 传入内部组件——在创建高级别的组件时非常有用。
文档
refs
获取子节点引用
provide/inject
能够实现祖先和后代之间传值
// parent
this.$children[0].xx = 'xxx'
 
 
// child:并未在props中声明foo <p>{{$attrs.foo}}</p>
// parent
<HelloWorld foo="foo"/>
  
// parent
<HelloWorld ref="hw"/>
mounted() {
    this.$refs.hw.xx = 'xxx'
}
// ancestor
provide() {
    return {foo: 'foo'}
}
// descendant
inject: ['foo']
