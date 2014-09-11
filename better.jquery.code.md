#开发中如何高效的使用jQuery
--------------

##缓存变量(var Caching)
DOM遍历是昂贵的，所以尽量将重用元素缓存

```javascript
//bad
h = $('#element').height();
$('#element').css('height', h-20);

//good
$element = $('#element');
h = $element.height();
$element.css('height', h-20);
```

##避免全局变量(avoid Globals)
jQuery 与 javascript 一样，一般来说，最好确保你的变量在函数作用域内

```javascript
// bad
$element = $('#element');
h = $element.height();
$element.css('height', h-20);

// good
var $element = $('#element');
var h = $element.height();
$element.css('height', h-20);
```

##使用匈牙利命名法(use Hungarian Notation)
在变量前加 $ 前缀，便于识别出 jQuery 对象

```javascript
// bad
var first = $('#first');
var second = $('#second');
var value = $first.val();

// good - 在 jQuery 对象前加 $ 前缀
var $first = $('#first');
var $second = $('#second'),
var value = $first.val();
```

##使用 var 链（单 var 模式）(use var Chaining)
将多条 var 语句合并为一条语句，我建议将未赋值的变量放到后面
```javascript
var
  $first = $('#first'),
  $second = $('#second'),
  value = $first.val(),
  k = 3,
  cookiestring = 'SOMECOOKIESPLEASE',
  i,
  j,
  myArray = {};
```

##请使用 'on'
在新版 jQuery 中，更短的 on("click") 用来取代类似 click() 这样的函数。 在之前的版本中 on() 就是 bind()。 自从 jQuery 1.7 版本后，on() 附加事件处理程序的首选方法。 然而，出于一致性考虑，你可以简单的全部使用 on() 方法
```javascript
// bad
$first.click(function(){
    $first.css('border','1px solid red');
    $first.css('color','blue');
});

$first.hover(function(){
    $first.css('border','1px solid red');
})

// good
$first.on('click',function(){
    $first.css('border','1px solid red');
    $first.css('color','blue');
})

$first.on('hover',function(){
    $first.css('border','1px solid red');
})
```

## 精简 javascript(condense Javascript)
一般来说，最好尽可能合并函数
```javascript
// bad
$first.click(function(){
    $first.css('border','1px solid red');
    $first.css('color','blue');
});

// good
$first.on('click',function(){
    $first.css({
        'border':'1px solid red',
        'color':'blue'
    });
});
```
##链式操作(use Chaining)
jQuery 实现方法的链式操作是非常容易的
```javascript
// bad
$second.html(value);
$second.on('click', function(){
    alert('hello everybody');
});
$second.fadeIn('slow');
$second.animate({height:'120px'}, 500);

// good
$second.html(value);
$second.on('click', function(){
    alert('hello everybody');
}).fadeIn('slow').animate({height:'120px'}, 500);
```
##维持代码的可读性(keep your code Readable)
伴随着精简代码和使用链式的同时，可能带来代码的难以阅读。 添加缩紧和换行能起到很好的效果
```javascript
// bad
$second.html(value);
$second.on('click',function(){
    alert('hello everybody');
}).fadeIn('slow').animate({height:'120px'},500);

// good
$second.html(value);
$second
    .on('click',function(){ alert('hello everybody');})
    .fadeIn('slow')
    .animate({height:'120px'},500);
```
##选择短路求值(prefer Short-circuiting)
短路求值是一个从左到右求值的表达式，用 &&（逻辑与）或 ||（逻辑或）操作符
```javascript
// bad
function initVar($myVar) {
    if(!$myVar) {
        $myVar = $('#selector');
    }
}

// good
function initVar($myVar) {
    $myVar = $myVar || $('#selector');
}
```

##选择捷径(prefer Shortcuts)
精简代码的其中一种方式是利用编码捷径
```javascript
// bad
if(collection.length > 0){..}

// good
if(collection.length){..}

```

##繁重的操作中分离元素(detach elements when doing heavy Manipulations)
如果你打算对 DOM 元素做大量操作（连续设置多个属性或 css 样式），建议首先分离元素然后在添加
```javascript
// bad
var
    $container = $("#container"),
    $containerLi = $("#container li"),
    $element = null;

$element = $containerLi.first();
//... 许多复杂的操作

// good
    $container = $("#container"),
    $containerLi = $container.find("li"),
    $element = null;

$element = $containerLi.first().detach();
//... 许多复杂的操作

$container.append($element);
```
## 熟记技巧
对于适用jQuery中的经验少，一定要查看文档，可能会有一个更好或更快的方法
```javascript
//bad
$('#id').data(key.value);

//good
$.data('#id', key, value);
```
##使用父元素缓存来查询子元素
DOM遍历是昂贵的操作，典型做法是缓存父元素并在选择子元素重用这些缓存元素
```javascript
//bad
var
    $container = $('#container'),
    $containerLi = $('#container li'),
    $containerLiSpan = $('#container li span');
//good
var
    $container = $('#container '),
    $containerLi = $container.find('li'),
    $containerLiSpan= $containerLi.find('span');
```
##避免通用选择符
将通用选择符放到后代选择符中，性能非常糟糕
```javascript
// bad
$('.container > *');

// good
$('.container').children();
```
##避免隐式通用选择符
通用选择符有时是隐式的，不容易发现
```javascript
// bad
$('.someclass :radio');

// good
$('.someclass input:radio');
```
##优化选择符
例如，ID 选择符应该是唯一的，所以没有必要添加额外的选择符
```javascript
//bad
$('div#id');
$('div#id a.myLink');

//good
$('#id'');
$('#id .mylink');
```
##避免多个ID选择符
在此强调，ID选择符应该是唯一的，不需要添加额外的选择符，更不需要多个后代ID选择符
```javascript
//bad
$('#outer #inner');

//good
$('#inner');
```

##坚持最新版本
新版本通常更好：更轻量级、更高效。

##摒弃弃用方法
关注每个新版本的废气方法

```javascript
// bad - live 已经废弃
$('#stuff').live('click', function() {
  console.log('hooray');
});

// good
$('#stuff').on('click', function() {
  console.log('hooray');
});
// 注：此处可能不当，应为live能实现实时绑定，delegate或许更合适

```
##利用CDN
google的CDN能保证选择离用户最近的缓存并迅速响应

##必要时组合jQuery和javascript原生代码
jQuery就是javascript，这意味着用jQuery能做的事，同样可以用原生代码来做，原生代码的可读性和可维护性可能不如jQuery，而且代码更长，但也意味着更高效（通常更接近底层代码可读性越差，性能越高）

##Final Considerations
jQuery并非不可或缺，仅是一种选择
jQuery is not a requirement，but a choice

来源：[Writing Better jQuery Code](http://modernweb.com/2013/11/25/writing-better-jquery-code/)