---
title: "WordPress的插件机制"
date: 2016-08-07
categories: 
  - "设计模式"
tags: 
  - "hook"
  - "plugins"
  - "wordpress"
  - "插件机制"
  - "钩子"
---

[WordPress的插件机制](https://codex.wordpress.org/Plugin_API)展现了强大扩展能力，它将用户模板定制、插件开发等从核心处理流程中剥离，也让开发者在核心之外有机会修改核心处理流程中的任何一个环节。

#### 基本概念

WordPress插件机制的核心是钩子（Hooks），每一次处理请求都会顺序执行一系列的钩子，每个钩子由唯一的标签（Tag）标识，每个钩子上能够注册数量不限的函数，在钩子执行中，函数按照注册时的优先级先后依次执行，每个钩子可以重复被执行。

举例来讲，当服务器接到首页的请求时，第一个钩子_muplugins\_loaded_会在加载完[自动激活（Must-Use）](https://www.sitepoint.com/wordpress-mu-plugins/)插件后被执行，第二个钩子_registered\_taxonomy_会在系统初始化默认的分类系统（create\_initial\_taxonomies）中执行。在响应信息完成前，有[几十个钩子](https://codex.wordpress.org/Plugin_API/Action_Reference)被依次执行。[](/wp-content/uploads/2016/08/default_actions_sequence.png)

[![default\_actions\_sequence](/wp-content/uploads/2016/08/default_actions_sequence-1024x399.png)](/wp-content/uploads/2016/08/default_actions_sequence.png)

如果钩子上没有注册函数，那么执行钩子时并没有实际动作。比如_registered\_taxonomy_是在每个分类系统注册结束时被执行，当初始化分类系统时，4.5.3版本默认注册了5个分类系统：category、post\_tag、nav\_menu、link\_category、post\_format，每个分类系统注册结束后_registered\_taxonomy_都被执行一次，但由于WordPress核心并没有在这个钩子上注册过函数，所以没有实际的动作。

如果钩子上注册了函数，那么执行钩子时，按照优先级从高到低顺序执行，优先级一样的，先注册的先执行。优先级用非负整数表示，数字越小优先级越高。4.5.3版本在加载主题模板前会执行_template\_redirect_来处理若干URL重定向的问题。系统默认注册了6个函数，其中\_wp\_admin\_bar\_init优先级为0，wp\_old\_slug\_redirect、redirect\_canonical优先级为10，rest\_output\_link\_header、wp\_shortlink\_header优先级为11，wp\_redirect\_admin\_locations优先级为1000. 假如用户在Must-Use插件中注册了setup\_custom\_redirect到_muplugins\_loaded_，而函数的内容是注册一个用户custom\_redirect函数到_template\_redirect_，那么系统在执行_muplugins\_loaded_时，会执行setup\_custom\_redirect函数，从而注册custom\_redirect到_template\_redirect_。注册custom\_redirect时，没有指定优先级，默认是10，这样就与wp\_old\_slug\_redirect和redirect\_canonical有着相同的优先级。由于执行_muplugins\_loaded_是在default-filters.php（系统默认的两个优先级为10的函数都在这里注册）执行之后，所以custom\_redirect执行时候也后执行。

#### 编程接口

钩子的类型有两种，过滤钩子（Filter）和动作钩子（Action），所以编程接口（API）也有两组。这两组编程接口定义在wp-includes/plugin.php中，函数签名如下：

```
function add_filter( $tag, $function_to_add, $priority = 10, $accepted_args = 1 );
```

add\_filter和add\_action用于函数注册，apply\_filters、apply\_filters\_ref\_array、do\_action、do\_action\_ref\_array用于钩子执行，remove\_filters、remove\_all\_filters、remove\_action、remove\_all\_actions用于函数移除。函数注册和函数移除必须在钩子（要注册和要移除的钩子，非其它的钩子）执行前或者执行中才能生效。在钩子执行中注册或者移除仅对低优先级的函数生效。

动作钩子是一类特殊的过滤钩子，两种钩子的标签和注册函数是共享的，都存储在**$wp\_filter**对象中，换句话说用add\_action注册的函数，用apply\_filters执行在某些情况下也是可以的。事实上，动作钩子的add\_action方法直接调用了add\_filter方法。

过滤钩子顾名思义，钩子上注册的函数要起过滤的作用。具体来说，会对某个对象的内容，可能是字符串、整数或者复杂对象做一定的修改后再返回修改后的对象。所以一系列的钩子连在一起，对这个对象形成了层层的过滤，直到钩子执行结束。过滤钩子主要的特点是，前一个函数的处理结果可以传给下一个函数。动作钩子相较而言，无法接收前一个函数的返回值。对比两个函数的参数，也可以发现apply\_filters的第二个参数$value是必选参数，这个参数也就是要过滤的对象，这个参数在第二个函数执行时，会传入第一个函数的返回值，而后依此类推。do\_action的第二个参数$args不是必选，即使有返回值也不会再向下传递。类似地，apply\_filters\_ref\_array的返回值会由$args\[0\]继续向后传递。值得注意的是，如果要用apply\_filters代替do\_action，第二个参数在第二个函数之后就无法正常获取，因此前面提到的某些情况，即是第二个参数无意义的时候。当然最好不要用apply\_filters去代替do\_action。

has\_filter和has\_action用于检查某个钩子是否存在，填写第二个参数可以检查某个钩子上是否注册了某个函数。

current\_filter和current\_action用于获取当前执行函数所在的钩子的名称，而doing\_filter和doing\_action用于获取当前所有在执行的钩子的名称。前后两组的区别是当前执行函数只会有一个（单线程），对应的钩子也只有一个，而在执行的钩子可以有多个，因而钩子A的注册函数中还可能执行钩子B，而执行到B的注册函数是，A并没有执行完，只是暂时压入了栈中。**$wp\_current\_filter**对象即是一个堆栈，用于存放执行中的钩子，而堆顶是正在执行函数的的钩子。doing\_filter的参数为null时，只检查$wp\_current\_filter栈中是否为空，不为空，返回真；参数为某个钩子时，去检查钩子是否在栈中，如果在，返回真。

did\_action用于检查某个钩子是否执行过，执行过的钩子都会在**$wp\_actions**中登记一次，而且登记顺序是符合执行顺序的。利用$wp\_actions可以在最后一个钩子_shutdown_执行时，顺序打印所有执行过的钩子，来获取[真实的场景中钩子的执行顺序](http://wordpress.stackexchange.com/questions/162862/how-to-get-wordpress-hook-run-sequence)。

```
function show_hooks_order() {
    print_r( $GLOBALS['wp_actions'] );
}
add_action( 'shutdown', 'show_hooks_order' 
```

了解有哪些钩子和钩子的作用是什么，除了直接查看源码外，Adam R Brown还汇总了历史上各个WordPress版本中[钩子词典](http://adambrown.info/p/wp_hooks)，也可以用作参考。

#### 结语

WordPress的钩子机制是一种扩展流程的通用思想，不论是否做网站开发，都应该学习这种机制，从而了解庞大系统为何能变得庞大还脉络清晰、适应性强大的缘由，进一步也会帮助自身在设计系统时有所借鉴。
