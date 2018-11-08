---
title : backbone.js入门
date : 2017-10-31 10:33
tags : js backbone思想
---
backbone.js入门
===
此篇文章的目的是让没有接触过backbone.js，或者没有接触过类似前端思想的人能够理解backbone.js核心思想。在此基础上，通过一些实例，了解backbone.js在前端的角色及自己动手设计和编写一些简单的前端代码。

如下一句话是backbone.js官网（中文）的原话：

> Backbone.js为复杂WEB应用程序提供模型(model)、集合(collection)、视图(view)的结构。其中模型用于绑定键值数据和自定义事件；集合附有可枚举函数的丰富API； 视图可以声明事件处理函数，并通过RESRful JSON接口连接到应用程序。

从这段话中我们可以看到backbone.js为我们提供了一种面向对象的思想。前端有model、collection、view三种类型。那么，是不是说前端所有的东西都可以通过某种方法封装成这三种类型呢？大家都知道前端所涉及的东西有dom、js方法、js对象、css样式等，是否真如我们预测的一样呢？下面我们先通过几个例子来解开backbone.js的面纱。

创建一个model对象
```
//js 原生的方式
var obj = new Object();
obj.name = 'zangbx';
console.log(obj.name);
//backbone.js 方式
var model = new Backbone.Model();
model.set("name","zangbx");
console.log(model.get("name"));
```
从上面的对比中，我们可以看到创建一个backbone.js的对象，和一个普通的js对象没有太大区别，只是从上面的操作上，可以预见，backbone.js给我们提供了一些额外的方法。比如上面的set、get方法等。
其实，确实backbone.js给Model赋予了一些方法，下面来自官网（如果不想看这些方法可以跳过）：
```
extend
constructor / initialize
get
set
escape
has
unset
clear
id
idAttribute
cid
attributes
changed
defaults
toJSON
save
destroy
validate
等等
```
当然，你可以对上面的这些方法进行重写，也可以添加新的方法。
这里来看几个例子：
```
var User = new Backbone.Model.extend({
	defaults:{
		username:"",
		password:"",
		age : 0,
		gender : 'M'
	}
});
var zangbx = new User();
zangbx.set("name","zangbx");
zangbx.set("password","123456");
console.log(zangbx);
```
至此，我们发现backbone.js是有点意思的，有点java风格的操作了。这样我们可以将extend对比成java的extends，一种继承的方式。defaults里面可以定义一些成员变量。
让我们继续，是不是backbone.js也有类似于java的构造函数呢？有
```
var User = Backbone.Model.extend({
	defaults:{
		name : "",
		password : "",
		age : 0,
		gender : 'M'
	},
	initialize:function(name,age,gender){
		this.set("name",name);
		this.set("age",age);
		this.set("gender",gender) ;
	}
});
var zangbx = new User('zangbx',18,'F');
console.log(JSON.stringify(zangbx));
```
好吧，这样我就可以像创建java对象一样创建js对象了。
如果这样，那么backbone.js中collection类型，是不是也和java中的集合一样呢？
我们尝试一下：
```
var zhangsan = new User('zhangsan',18,'M');
var wangwu = new User('wangwu',18,'M');
var users = new Backbone.Collection();
users.add(zhangsan);
users.add(wangwu);
console.log(JSON.stringify(users));
```
真的可以。到现在为止，一切还都可以理解。有了backbone.js确实让我们比较容易编写面向对象的代码了。当然backbone.js也对collection内置了一些方法，如下：
```
extend
model
constructor / initialize
models
toJSON
sync
add
remove
reset
set
get
push
pop
length
comparator
sort
等等
```
我们继续。现在我们了解了model，collection，那么view有是个什么东西呢？
搜索了一下，有人说view是backbone.js的核心。我也赞同这一说法，为什么呢？现在数据有了（model、collection），那这些数据展现给用户，必须要用到view了吧，所以view可以说是战斗在最前线的存在。
再看几个例子：
```
<html>
	<body>
		<div id="formDivID"></div>
	</body>
	<!--引入backbone.js需要的js包-->
	<script type="text/javascript" src="https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js" ></script>
	<script type="text/javascript" src="https://cdn.bootcss.com/underscore.js/1.8.3/underscore-min.js"></script>
	<script type="text/javascript" src="https://cdn.bootcss.com/backbone.js/1.3.3/backbone.js"></script>
	<!--定义了一个模板-->
	<script type="text/template" id="form-template">
		<form>
			name:<input type="text" ></input>
			age:<input type="text"></input>
		</form>
	</script>
	<!--写js代码-->
	<script type="text/javascript" >
		var FormView = Backbone.View.extend({
			el:'#formDivID',
			template:_.template($("#form-template").html()),
			render : function(){
				this.$el.html(this.template);
			}
		});
		
		var formView = new FormView();
		formView.render();
	</script>
</html>
```
这段代码中有一个表单模板，一个view扩展对象。可以看到表单模板其实就是一个html的超文本（也可以是一个.html的文件）。在view扩展对象中，render方法用户渲染页面，其实里面不用模板用其他方式也能实现页面渲染的作用，比如：
```
this.$el.html("this is not a form,just a text!");
```
既然是模板，那么模板引擎肯定允许传入参数，所以，我试一下：
```
<script type="text/template" id="form-template">
	<form>
		name:<input type="text" value="<%= name %>" ></input>
		age:<input type="text" value="<%= age %>" ></input>
	</form>
</script>

<script type="text/javascript" >
	var FormView = Backbone.View.extend({
		el:'#formDivID',
		template:_.template($("#form-template").html()),
		render : function(){
			this.$el.html(this.template(this.model.toJSON()));
		}
	});
	var zangbx = new User('zangbx',18,'F'); 
	var formView = new FormView({model:zangbx});
	formView.render();
</script>
```
这样调整之后，我们发现，表单中的input有了默认值啦，是不是不难理解。此处用到的模板引擎是backbone默认的，比较简单。模板需要的参数为JSON数据，模板常见操作为<%= value %>(用于赋值)，<% javascript code %>(用于写javascript代码)，完了，是不是超级简单。
我们再写个例子看看呢：
```
<html>
	<body>
		<div id="tableDivID"></div>
	</body>
	<!--引入backbone.js需要的js包-->
	<script type="text/javascript" src="https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js" ></script>
	<script type="text/javascript" src="https://cdn.bootcss.com/underscore.js/1.8.3/underscore-min.js"></script>
	<script type="text/javascript" src="https://cdn.bootcss.com/backbone.js/1.3.3/backbone.js"></script>
	<!--定义了一个模板-->
	<script type="text/template" id="table-template">
		<table border="1">
			<tr>
				<th>序号</th>
				<th>名称</th>
				<th>年龄</th>
				<th>性别</th>
			</tr>
			<% 
			if(users.length>0){
				for(var i=0;i<users.length;i++){
					user = users.at(i);
			%>
				<tr>
					<td><%= (i+1) %></td>
					<td><%=user.get('name') %></td>
					<td><%=user.get('age') %></td>
					<td><%=user.get('gender') %></td>
				</tr>
			<%
				}
			}
			%>

		</table>
	</script>
	<!--写js代码-->
	<script type="text/javascript" >
		var User = Backbone.Model.extend({
			defaults:{
				name : "",
				password : "",
				age : 0,
				gender : 'M'
			},
			initialize:function(name,age,gender){
				this.set("name",name);
				this.set("age",age);
				this.set("gender",gender) ;
			}
		});
		
		var TableView = Backbone.View.extend({
			el:'#tableDivID',
			template : _.template($("#table-template").html()),
			render : function(){
				this.$el.html(this.template);
			}
		});
		var zhangsan = new User('zhangsan',18,'M');
		var wangwu = new User('wangwu',18,'M');
		var users = new Backbone.Collection();
		users.add(zhangsan);
		users.add(wangwu);

		var tableView = new TableView({users:users});
		tableView.render();
	</script>
</html>
```

从上面的代码可以看到，在模板中的<% %> 内可以直接使用view里面定义的对象。
吼吼~~，到这里，我们发现，一个前端的页面是由多个view对象组成的啊，可以幻想一下，整个前端组件仅仅有几十个这样的view组件就能够实现几乎所有的界面展现啦。比如封装公共的formView，tableView,seletedView,treeView,textView,dateView等等，只要我们提供不同的数据（model），那么他们展现的数据就会不同啦。
是不是有种想试试的感觉，到这里，我们发现将数据展现已经不是问题了，那么，什么是问题呢？1，数据从哪里来啊，2，只有展现，怎么没有交互呢？

backbone.js为了与服务器端通信的方法，不过需要自己去重写。想一下现在我们完全可以通过jquery的ajax获取数据啦，我们可以将与服务器端交互的代码写到initialize:function中，这样在创建这个对象时，自动的向服务器请求，获取数据。
所以，还是来看看怎么交互的吧。
其实，backbone.js为了方便交互，提供了Events对象。中文官网如是说：

> Events 是一个可以融合到任何对象的模块, 给予 对象绑定和触发自定义事件的能力. Events 在绑定之前 不需要声明, 并且还可以传递参数.

下面看个例子：

```
var zangbx = new User('zangbx',18,'F'); 
		
_.extend(zangbx,Backbone.Events);
zangbx.on('change',function(){
	console.log("zangbx changed");
});
zangbx.on('event',function(msg){
	console.log("zangbx :"+msg);
});
zangbx.set("age",19);
zangbx.trigger('event','this is a event');
```

从上面可以看到，backbone.js不仅满足常用的event（change），还可以监听到自定义的event（event);再看看event与view结合使用的场景。
```
<script type="text/template" id="form-template">
	<form>
		name:<input name="username" type="text" value="<%= name %>" ></input>
		age:<input name="age" type="text" value="<%= age %>" ></input>
	</form>
</script>

var FormView = Backbone.View.extend({
	el:'#formDivID',
	events:{
		"change input[name='username']" : "checkUsername",
		"change input[name='age']" : "checkAge"
	},
	template:_.template($("#form-template").html()),
	render : function(){
		this.$el.html(this.template(this.model.toJSON()));
	},
	checkUsername : function(event){
		console.log($("input[name='username']").val());
	},
	checkAge : function(event){
		console.log($("input[name='age']").val());
	}
});
```
haha,通过events可以给view所对应的html中的dom元素绑定事件啦，这样，在操作dom中的元素时，后面的事件函数就会被触发，然后根据具体的业务作出相应的变化。

到此为止，整个入门也算结束了，当然，backbone.js没有这么简单，如果先深入了解可以去官网了解。

#### 补充 ####
require.js解决了前端一个比较棘手的问题————作用域全局命名污染问题。它的作用就是，定义一个模块，并显示的定义依赖关系，将依赖的以参数形式注入到模块中。这样，在这个模块中，你就可以为所欲为啦，你不允许，别人是管不了啦啦啦。具体语法如下：
```
define(['moduleA', 'moduleB', 'moduleC'], function (moduleA, moduleB, moduleC){
	// some code here
});
```
如果项目中用想用到jquery，那么就可以这样用
```
define(['jquery'],function($){
	$("#id").val();
});
```
当然，jquery需要在配置文件中进行配置，或者写绝对路径哦。














