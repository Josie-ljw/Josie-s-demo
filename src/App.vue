<template>
  <div id="app" @click.stop="stop($event,index)">
    <h1 v-text="title"></h1>
    <div><input v-model="newItem" v-on:keyup.enter="addNew"></div>
    <div class="box-center">
      <ul class="box-list">
        <li v-for="item ,index in items" v-bind:class="{finished:item.isfinished}"  v-on:mouseover="moveBtn(item)" v-on:mouseout="leaveBtn(item)" @contextmenu.prevent="show($event,index)">
          <span v-on:click="toggleFinished(item, index)" v-bind:class="{bgYellow:item.isBgyellow,bgGreen:item.isBggreen,bgRed:item.isBgred}">{{item.label}}</span>
          <span class="list-btn" v-show="item.isShow">
          <!-- <button v-on:click="moveUp(index,item)">up</button> -->
          <!-- <button v-on:click="yellowBtn(index)">yellow</button> -->
          </span>
        </li>
      </ul>
    </div>
    <div id="right-menu" v-bind:class="{display:isBlock}" v-bind:style="{left:leftPx+'px',top:topPx+'px'}">
      <ul>
        <li v-on:click="moveUp()">上移</li>
        <li class="border-bottom" v-on:click="moveDown()">下移</li>
        <li class="border-bottom" v-on:click="deleteBtn(index)">删除</li>
        <li v-on:click="yellowBtn()"> 黄色</li>
        <li v-on:click="greenBtn()"> 绿色</li>
        <li v-on:click="redBtn()"> 红色</li>
      </ul>
    </div>
  </div>
</template>

<script>

  import Store from './store'

  export default {
    data: function() {
      return {
        title: 'A simple todo-list',
        items: Store.fetch(),
        newItem: '',
        isShow: false,
        isBlock: true,
        isBgyellow: false,
        isBggreen: false,
        isBgred: false,
        leftPx:0,
        topPx:0,
        index: null,
      }
    },
    watch: {
      items: {
        handler: function(items) {
          Store.save(items)
        },
        deep: true
      }
    },
    methods: {
      toggleFinished: function(item, index) {
        item.isfinished = !item.isfinished;
        this.index = index;
      },
      show:function($event,index) {
        $event.cancelBubble = true;
        this.isBlock = false;
        this.topPx = ($event.clientY);
        this.leftPx = ($event.clientX);
        this.index = index;
      },
      stop: function(event){
        this.isBlock = true;
      },
      moveBtn: function(item) {
        item.isShow = true;
      },
      leaveBtn: function(item) {
        item.isShow = false;
      },
      addNew: function() {
        //非空才可以添加
        if (this.newItem!='') {
          this.items.push({
            label: this.newItem,
            isfinished: false
          })
        this.newItem = '';
      },
      moveUp: function() {
        //在上一项插入该项
        // this.items.splice(index-1,0,(this.items[index]));
        // //删除后一项
        // this.items.splice(index+1,1);
        // item.isShow = false;
        // if (index === 0) {
        //   alert("到顶啦！");
        // }
        if (this.index === 0) {
          alert("到顶啦！");
        }
        if ((this.index > 0) && (this.index < this.index.length)) {
          //在上一项插入该项
          this.items.splice(this.index-1,0,(this.items[this.index]));
          //删除后一项
          this.items.splice(this.index+1,1);
        }
      },
      moveDown: function() {
        if (this.index == this.items.length-1) {
          alert("已经是最后一项啦！");
        }
        if ((this.index > 0) && (this.index < this.index.length)) {
          //在下一项插入该项
          // console.log(this.index+2);
          this.items.splice(this.index+2,0,(this.items[this.index]));
          //  删除前一项
          this.items.splice(this.index,1);
        }
      },
      deleteBtn: function(index) {
        this.items.splice(index,1);
      },
      yellowBtn: function(index) {
        let $thisList = this.items[this.index];
        $thisList.isBgred = false;
        $thisList.isBggreen = false;
        $thisList.isBgyellow = !$thisList.isBgyellow;
      },
      greenBtn: function(index) {
        let $thisList = this.items[this.index];
        $thisList.isBgred = false;
        $thisList.isBgyellow = false;
        $thisList.isBggreen = !$thisList.isBggreen;
      },
      redBtn: function(index) {
        let $thisList = this.items[this.index];
        $thisList.isBggreen = false;
        $thisList.isBgyellow = false;
        $thisList.isBgred = !$thisList.isBgred;
      },
    },
  }
}
  </script>

<style>
  html,body {
    height: 100%;
  }
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    height: 100%;
    margin-top: 60px;
  }
  .box-center {
    margin: 0 auto;
  }
  .box-list {
    width: auto;
    display: inline-block;
  }
  .box-list li a{
    text-decoration: none;
  }
  .box-list li {
    width: 350px;
    text-align: left;
    position: relative;
  }
  #right-menu {
    background: #dddddd;
    width: 100px;
    position: absolute;
    border: 1px solid #c1c1c1;
    border-radius: 4px;
    box-shadow: 0 0 6px #888888;
  }
  #right-menu li {
    list-style: none;
    color: #000;
  }
  #right-menu li:hover{
    background-color: rgb(15, 143, 255);
  }
  #right-menu ul {
    padding-left: 0;
    margin: 5px 0;
  }
  .bgYellow {
    background-color: #FFFF77;
  }
  .bgGreen {
    background-color: #99FF99;
  }
  .bgRed {
    background-color: #FF77FF;
  }
  .display {
    display: none;
  }
  .border-bottom {
    border-bottom: 2px solid #c5c5c5;
  }
  .list-btn {
    float: right;
    margin-left: 20px;
    position: absolute;
    right: 0;
  }
  .finished {
    text-decoration: line-through;
  }
</style>
