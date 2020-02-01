vue面试第十三题：watch和computed的区别以及怎么选用?
答：var vm = new Vue({
el: '#demo',
data: {
firstName: 'Foo',
lastName: 'Bar',
fullName: 'Foo Bar'
},
watch: {
//监听了firstName这个对象，一旦值发生了改变，就会触发方法体
firstName: function (val) {
this.fullName = val + ' ' + this.lastName
},
//监听了firstName这个对象，一旦值发生了改变，就会触发方法体
lastName: function (val) {
this.fullName = this.firstName + ' ' + val
}
}
})
computed 作用 监听值，也可以计算值
比如：C值 = A值 加 B值，
监听 C值， 如果 A值 或 B值发生了改变，C值就会被改变，或者说是计算，C值不用再data中定义，直接在computed中定义就可以使用了
上代码：
<template>     
<div>         
<input type="text" v-model="firstName">         
<p>{{firstName}}</p>         
<input type="text" v-model="lastName">        
<p>{{lastName}}</p>         
<p>{{fullName}}</p>     
</div> 
</template> 
<script>     
// 定义全局过滤器     
export default {         
data () {             
// data中指定两个参数             
return {                 
firstName: 123,                 
lastName: 456,             
}         
},         
computed: {             
// 定义并监听fullName，             
// fullName的值 引用了 firstName 和 lastName             
// fullName的引用发生了改变，就会触发方法体             
fullName: function () {                 
return +this.firstName + +this.lastName             
}         
},           
mounted () {             
this.init();         
}     
} 
</script>
