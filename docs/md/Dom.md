join 数组转字符串
Dom核心
querySelector  getElementById  获取元素属性
class .
id    #
innerText  不识别HTML标签 	去除空格和换行 
innerHtml  可识别HTML标签 	保留空格和换行 
预解析: 变量和函数提升 先运行变量和函数定义
元素节点 1	属性节点 2	文本节点 3
父子节点	parentNode	children	children[0]	children[元素.length - 1]
兄弟节点	nextElementSibling	previousElementSibling
创建节点	createElement	
添加节点	appendChild		insertBefore
删除节点	removeChild		
复制节点	cloneNode 
括号为空或为false浅拷贝 只复制本身节点 不克隆子节点 
括号为true为深拷贝复制节点本身鸡所有子节点





Dom事件流	
addEventListener 
true 捕获阶段 从外到内
false或省略 冒泡阶段 从内到外
this	指向事件函数调用者
e.target 指向点击的对象
区别：
this		谁绑定事件就指向谁	
e.target	点击哪个元素就返回那个元素
const ul = document.querySelector('ul')
ul.addEventListener('click',function (e) {
    console.log(this)       //  给ul绑定对象this指向ul
    console.log(e.target)   //  点击li    e.target就指向li
}
事件委托	给父亲添加侦听器，利用事件冒泡影响每一个子节点

