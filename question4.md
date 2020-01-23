vue训练营面试第四题： 你怎么理解vue中的diff算法？
答：diff算法是一种优化手段，将前后两个模块进行差异化对比，修补（更新）差异的过程叫做patch(打补丁)
一、某些时候我们修改了页面中的某个数据，如果直接渲染到真实DOM中会引起整棵树的重绘，那么我们能不能只让我们改变过的数据映射到真实 DOM,做一个最少的重绘呢，
这就是diff算法要解决的事情。
virtual DOM是将真实的 DOM 的数据抽取出来，以对象的形式模拟树形结构，diff算法比较的也是virtual DOM
二、diff的比较概括起来就是对操作前后的dom树同一层的节点进行对比，一层一层对比，然后再插入真实的dom中，重新渲染
特点：
1、只会做同级比较，不做跨级比较
2、比较后几种情况
if (oldVnode === vnode)，他们的引用一致，可以认为没有变化。
if(oldVnode.text !== null && vnode.text !== null && oldVnode.text !== vnode.text)，文本节点的比较，需要修改，则会调用Node.textContent = vnode.text。
if( oldCh && ch && oldCh !== ch ), 两个节点都有子节点，而且它们不一样，这样我们会调用updateChildren函数比较子节点，这是diff的核心
else if (ch)，只有新的节点有子节点，调用createEle(vnode)，vnode.el已经引用了老的dom节点，createEle函数会在老dom节点上添加子节点。
else if (oldCh)，新节点没有子节点，老节点有子节点，直接删除老节点。
三、设置key和不设置key的区别：
不设key，newCh和oldCh只会进行头尾两端的相互比较，设key后，除了头尾两端的比较外，还会从用key生成的对象oldKeyToIdx中查找匹配的节点，
所以为节点设置key可以更高效的利用dom。key的作用主要是为了高效的更新虚拟DOM。另外vue中在使用相同标签名元素的过渡切换时，也会使用到key属性，
其目的也是为了让vue可以区分它们，否则vue只会替换其内部属性而不会触发过渡效果
