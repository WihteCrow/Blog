# 前端性能优化

1. 使用documentFragments或innerHTML取代复杂的元素注入

DOM操作在浏览器上是昂贵的。现在假设我们页面中有一个ul元素，调用AJAX获取JSON列表，然后使用JavaScript更新元素内容。通常，程序员会这么写：

```JavaScript
var list = document.querySelector('ul');
ajaxResult.items.forEach(function(item) {
    // 创建<li>元素
    var li = document.createElement('li');
    li.innerHTML = item.text; // <li>元素常规操作，例如添加class，更改属性attribute，添加事件监听等
    // 迅速将<li>元素注入父级<ul>中
    list.apppendChild(li);
});
```
上面的代码，没循环意思就要想dom节点插入li，这样处理是非常慢的。
优化思路，在内存中创建好dom，最后只在页面上插一次就ok了

```JavaScript
var frag = document.createDocumentFragment();
ajaxResult.items.forEach(function(item) {
    // 创建<li>元素
    var li = document.createElement('li');
    li.innerHTML = item.text; // <li>元素常规操作，例如添加class，更改属性attribute，添加事件监听等
    // 迅速将<li>元素注入父级<ul>中
    frag.apppendChild(li);
});

// 最后将所有的列表对象通过DocumentFragment集中注入DOM
document.querySelector('ul').appendChild(frag);
```
或者

```JavaScript
var frag = document.createDocumentFragment();
ajaxResult.items.forEach(function(item) {
    // 创建<li>元素
    var li = document.createElement('li');
    li.innerHTML = item.text; // <li>元素常规操作
    // 例如添加class，更改属性attribute，添加事件监听，添加子节点等
    // 将<li>元素添加到碎片中
    frag.appendChild(li);
});

// 最后将所有的列表对象通过DocumentFragment集中注入DOM
document.querySelector('ul').appendChild(frag);
```
