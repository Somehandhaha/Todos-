Todos备忘录项目详解

vue.js   本人 刚刚发布 · 编辑
赞  |   0收藏  |  0
0 次浏览
今天我们要做的是个备忘录项目模仿一下Todos这是Todos的官方网址http://todomvc.com/examples/v...

操作流程
新建文件夹和文件名称

自定义文件夹和文件名称，我们用它来装我们今天的项目

打开命令行 依次输入：

npm init -y // 初始化项目

npm i -S underscore vue todomvc-app-css //下载这三个文件，因为我们后期要用到

如图这样

在index.html里边输入Vue的基本样式和引入css、html：

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" href="./node_modules/todomvc-app-css/index.css">
</head>
<body>
        <section class="todoapp">
                <header class="header">
                    <h1>todos</h1>
                    <input class="new-todo" placeholder="What needs to be done?" autofocus="" >
                </header>
                <section class="main" v-show="showList">
                    <input class="toggle-all" id="toggle-all" type="checkbox">
                    <label for="toggle-all">Mark all as complete</label>
                    <ul class="todo-list">
                        <li>
                            <div class="view">
                                <input class="toggle" type="checkbox" >
                                <label></label>
                                <button class="destroy" ></button>
                            </div>
                            <input class="edit">
                        </li>
                    </ul>
                </section>
                <footer class="footer" v-show="showList">
                    <span class="todo-count">
                        <strong></strong></span>
                    <ul class="filters">
                        <li>
                            <a class="selected" href="#/">All</a>
                        </li>
                        <li>
                            <a href="#/active" >Active</a>
                        </li>
                        <li>
                            <a href="#/completed" >Completed</a>
                        </li>
                    </ul>
        
                    <button class="clear-completed" >Clear completed</button>
        
                </footer>
            </section>
    <script src="./../../vue.js"></script>
    <script>
        var app = new Vue({
            el:".todoapp"，
             data:{
                
            }
        })
    </script>
</body>
</html>
在data中加入写上一个数组，用来渲染到页面，数组里边又是一个对象，因为我们的一条数据里会有不同的数据。

 data:{
        todoList:[
            {
                text:"Vue很难",  //这个是任务的名称
                checkbox:false  // checkbox的false表示未完成，true表示已完成
            },
            {
                text:"不难，只要用心", 
                checkbox:false
            }
        ]
    }
在li里遍历一下数据并绑定，在label中加入数据

 <li v-for="(todo,index) in todoList">
    <div class="view">
        <input class="toggle" type="checkbox">
        <label>{{todo.text}}</label>
        <button class="destroy"></button>
    </div>
    <input class="edit">
</li>
给li元素动态绑定class,completed样式的值，根据todo.checked, 如果todo.checked为 true则有completed样式。

<li v-for="(todo,index) in todoList" :class="{completed: todo.checked}">
给checkbox加上v-model，值为todo.checked, checked属性会自动和todo.checked关联

<input class="toggle" type="checkbox" v-model="todo.checked"/>

这样我们就可以看到点击复选框的效果了。

在data中加入newTodo

 data: {
    newTodo:"",
    todoList: [{
            text: "Vue很难", //这个是任务的名称
            checked: false // checkbox的false表示未完成，true表示已完成 
        },
        {
            text: "不难，只要用心",
            checked: false
        }
    ]
}
删除默认数据，因为我们要用动态去添加，给我们的输入框动态绑定数据，并添加事件来提交内容。

<input class="new-todo" placeholder="你接下来需要完成什么?" autofocus="" v-model="newTodo" @keyup.enter.trim="addTodo" />
紧接着我们写绑定事件的逻辑，新建模块methods

methods: {
    addTodo() {
        this.newTodo = this.newTodo.trim(); //这句是去除空格 
        // 判断输入内容是否为空，为空不提交  
        if (this.newTodo.length < 1) {
            return;
        }
        //把新数据添加到数组当中,默认为未完成
        this.todoList.unshift({
            text: this.newTodo,
            checked:false
        })
        this.newTodo = ""
    }
}
这样我们就能动态的添加数据了，然后我们来做删除数据功能
绑定删除事件，因为我们要用到underscore，所以引入

<button class="destroy" @click="deleteTodo(todo)"></button>

<script src="./node_modules/underscore/underscore-min.js" charset="utf-8"></script>
添加业务逻辑在methods中添加

deleteTodo(todo){

this.todoList = _.without(this.todoList, todo)
}

我们看到官网的页面是没有数据底部是消失的，所以我们来完善一下
添加计算属性

 computed:{
        showList(){
            return this.todoList.length > 0
        }
    }
添加v-show

<footer class="footer" v-show="showList">
官网是双击数据是可以进行编辑的
绑定数据

<input class="edit" type = "text" v-model = "todo.text">

在data里添加索引

editingIndex: -1

绑定双击事件

<label @dblclick="editTodo(index)">{{ todo.text }}</label>

在methods里添加

editTodo(index) {

// 设置一下当前正在编辑的索引
this.editingIndex = index;
}

加上class

<li:class="{completed: todo.checked, editing: index === editingIndex}"
v-for="(todo,index) in todoList">

自定义一个属性

// 注册一个全局自定义指令 v-focus
Vue.directive('focus', {
// 当绑定元素插入到 DOM 中。
inserted(el) {

// 聚焦元素
el.focus()
},
// 当绑定元素更新的时候
update(el) {

el.focus();
}
})

添自定义属性并绑定事件

<input class="edit" type="text" v-model="todo.text" v-focus="index === editingIndex" @blur="saveTodo(todo)" @keyup.enter="saveTodo(todo)"/>

写事件

saveTodo(todo) {

this.editingIndex = -1
if (todo.text.trim().length < 1) {
  this.deleteTodo(todo)
}
}

未完成的个数
填写逻辑在计算属性中

activeCount() {

return this.todoList.filter(item => {
  return !item.checked
}).length;
}

渲染数据

<span class="todo-count">

<strong>{{activeCount}}</strong>
</span>

当刷新页面我们希望保留数据，这就需要我们的存储数据了
新建一个store.js并引入

在store.js中填写
var STORAGE_KEY = 'todoList'
window.todoStorage = {

fetch() {
try {
  return JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
} catch(error) {
  return [];
}
},
save(todoList) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todoList));
}
}

修改初始化数据

在data里添加

todoList: todoStorage.fetch()

进行监听，添加监听模块

watch: {
todoList: {

deep: true,
handler: todoStorage.save
}
}

全部完成功能的实现（选中和全选功能）
添加计算属性

// 是否所有任务都完成
allDone: {

get() {
  // 未完成的数量为0表示全部完成,全部完成返回true
  return this.activeCount === 0;
},
set(value) {
  this.todoList.forEach(todo => {
    todo.checked = value
  });
}
}

绑定数据

<input class="toggle-all" id="toggle-all" type="checkbox" v-model="allDone" />

页面切换（all、active、Completed）
实例一个对象来实现过滤逻辑

var filters = {
all: function (todos) {

return todos;
},
active: function (todos) {

return todos.filter(function (todo) {
  return !todo.checked;
});
},
completed: function (todos) {

return todos.filter(function (todo) {
  return todo.checked;
});
}
};

在data里添加一个属性visibility 来表示我们要显示所有，还是显示未完成，或已完成

visibility: 'all'

修改一下未完成的数量这个计算属性，使用上面的filters对象去过滤

computed: {
...
// 未完成的任务数量
activeCount() {

return filters.active(this.todoList).length;
},
}

添加任务过滤的计算属性：

computed: {
...
// 过滤任务列表
filteredTodoList: function () {

return filters[this.visibility](this.todoList);
}
}

在DOM当中添加点击事件，点击的时候修改visiblity属性即可

<li><a:class="{selected:visibility==='all'}"href="#/"@click="visibility='all'">all
</li>
<li>
<a
:class="{selected: visibility === 'active'}"
href="#/active"
@click="visibility = 'active'"

active
</li>
<li>
<a
:class="{selected: visibility === 'completed'}"
href="#/completed"
@click="visibility = 'completed'">completed
</li>
列表渲染的循环语句修改：
<li
:class="{completed: todo.checked, editing: index === editingIndex}"
v-for="(todo,index) in filteredTodoList" :key="'todo-'+index"

添加一个变量，得到hash值：

var visibility = location.hash.substr(location.hash.indexOf('/')+1);
visibility = visibility === '' ? 'all' : visibility

设置visibility属性的值为当前的这个变量：

data: {
visibility: visibility,
...
}

点击清空已完成功能：
添加一个已完成的任务数量计算属性：

computed: {
...
// 已完成的任务数量
completedCount() {

return filters.completed(this.todoList).length;
}
}

添加一个清空已完成的方法：

methods: {
...
// 清空已完成的任务列表
clearCompleted() {

this.todoList = filters.active(this.todoList)
}
}

DOM元素绑定事件，以及v-show:

<button class="clear-completed" @click="clearCompleted" v-show="completedCount > 0">清空已完成</button>
