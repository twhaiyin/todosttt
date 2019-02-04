<template>
<!-- 1创建数据 -->
    <!-- 2数据渲染 -->
    <!-- 3数据操控 需要在app.module.ts中加入import { FormsModule } from '@angular/forms';-->
    <!-- 4添加任务 -->
    <!-- 5全选 -->
    <!-- 6显示未完成的数量 -->
    <!-- 7删除全部已完成的任务 -->
    <!-- 8任务为0时，footr隐藏 -->
    <!-- 9编辑任务
          9.1双击获取数据
          9.2创建空数组editTodoLi，判断时使用
          9.2获取数据后，覆盖到空数组里
          9.3editTodoLi发生变化后，进行判断可以改变样式
          9.4编辑框获取焦点
          9.5失去焦点或按enter键时确认，按esc键时取消
    -->
    <!-- 10点击all，Active,completed时发生变化 
          10.1环境变量filterText
          10.2创建可变更的数组filterTodos,在computed里面,当filterText发生变化是，也随之发生变化
          10.3创建事件当锚点发生变化时，获取锚点值，并赋值到filterText中
          10.4把filterTodos渲染到页面中
          10.5更改按钮样式
    -->
    <!-- 11利用localStroage保存数据 -->
  <div class="hello">
    <section class="todoapp">
		<header class="header">
			<h1>todos</h1>
			<input class="new-todo" placeholder="What needs to be done?" autofocus @keydown.enter="addTodo">
		</header>
		<!-- This section should be hidden by default and shown when there are todos -->
		<section class="main">
			<input id="toggle-all" class="toggle-all" type="checkbox" v-model="todoAll">
			<label for="toggle-all">Mark all as complete</label>
			<ul class="todo-list">
				<!-- These are here just to show the structure of the list items -->
				<!-- List items should get the class `editing` when editing and `completed` when marked as completed -->
				<!-- <li class="completed">
					<div class="view">
						<input class="toggle" type="checkbox" checked v-model="itme.completed">
						<label>Taste JavaScript</label>
						<button class="destroy"></button>
					</div>
					<input class="edit" value="Create a TodoMVC template">
				</li> -->
				<li v-for="(item,index) in filterTodoList" :key="index" :class="{completed:item.todo,editing:editTodoLi === item}">
					<div class="view">
						<input class="toggle" type="checkbox" v-model="item.todo">
						<label @dblclick="getTodo(item)">{{item.title}}</label>
						<button class="destroy" @click="deleteTodo(index)"></button>
					</div>
					<input 
          class="edit"
          :value="item.title"
          v-todofocus="editTodoLi === item"
          @blur="editTod($event,item)"
          @keydown.enter="editTod($event,item)"
          @keydown.esc="editTodoLi = null"
          >
				</li>
			</ul>
		</section>
			<!-- This footer should hidden by default and shown when there are todos -->
		<footer class="footer" v-if="todolist.length">
			<!-- This should be `0 items left` by default -->
			<span class="todo-count"><strong>{{num}}</strong> item left</span>
			<!-- Remove this if you don't implement routing -->
			<ul class="filters">
				<li>
					<a :class="{selected:filterTodoLi === ''}" href="#/">All</a>
				</li>
				<li>
					<a :class="{selected:filterTodoLi === 'active'}" href="#/active">Active</a>
				</li>
				<li>
					<a :class="{selected:filterTodoLi === 'completed'}" href="#/completed">Completed</a>
				</li>
			</ul>
			<!-- Hidden if no completed items are left ↓ -->
			<button class="clear-completed" @click="deleteAllCompleted()">Clear completed</button>
		</footer>
		</section>
  </div>
</template>

<script>
import 'todomvc-app-css/index.css'
import 'todomvc-common/base.css'
export default {
  name: 'HelloWorld',
  data () {
    return {
      todolist:JSON.parse(window.localStorage.getItem('mytodo3') || '[]'),
      editTodoLi: null,
      filterTodoLi:''
    }
  },
  methods:{
    deleteTodo (i) {
      this.todolist.splice(i,1)
    },
    deleteAllCompleted () {
      this.todolist = this.todolist.filter(item => !item.todo)
    },
    addTodo (e) {
      const list = this.todolist
      if(!e.target.value){
        return alert('请输入内容')
      }
      list.push({
        id:list.length ? list[list.length-1].id+1 : 1,
        title:e.target.value,
        todo:false
      })
      e.target.value = ''
    },
    getTodo (item) {
      this.editTodoLi = item
    },
    editTod (e,item) {
      item.title = e.target.value
      this.editTodoLi = null
    }
  },
  computed:{
    todoAll:{
      get () {
        return this.todolist.every((item) => item.todo)
      },
      set () {
        const checked = this.todoAll
        this.todolist.forEach((item) => {
          item.todo = !checked
        })
      }
    },
    num () {
      return this.todolist.filter((item) => !item.todo).length
    },
    filterTodoList () {
      if(this.filterTodoLi === ''){
        return this.todolist
      } else if (this.filterTodoLi === 'active') {
        return this.todolist.filter(item => !item.todo)
      } else if (this.filterTodoLi === 'completed') {
        return this.todolist.filter(item => item.todo)
      }
    }
  },
  directives:{
   'todofocus': function (el,binding) {
      if(binding.value){
        el.focus()
      }
    }
  },
  watch:{
    todolist:{
      handler: function (val, oldVal) {
        window.localStorage.setItem('mytodo3',JSON.stringify(val))
      },
      deep: true
    }
  },
  created () {
    window.onhashchange = () => {
      this.filterTodoLi = window.location.hash.substr(2)
      
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
