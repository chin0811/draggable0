<script setup>
import { ref, reactive, watch } from "vue";
import draggable from "vuedraggable";
/*
draggable 对CSS样式没有什么要求万物皆可拖拽
:list="state.list"         //需要绑定的数组
ghost-class="ghost"        //被替换元素的样式
chosen-class="chosenClass" //选中元素的样式
animation="300"            //动画效果
@start="onStart"           //拖拽开始的事件
@end="onEnd"               //拖拽结束的事件
*/
const limitHeight = ref(6)
const columns = reactive(['list1','list2'])
const state = reactive({
  mainList: [
    { name: "程式1" ,height:1,id:1},
    { name: "程式2" ,height:2,id:2},
    { name: "程式3" ,height:3,id:3},
    { name: "程式4" ,height:1,id:4},
    { name: "程式5" ,height:2,id:5},
    { name: "程式6" ,height:3,id:6},
  ],
  list1: [
    { name: "_" ,height:1},
    { name: "_" ,height:1},
    { name: "_" ,height:1},
    { name: "_" ,height:1},
    { name: "_" ,height:1},
  ],
  list2: [
    { name: "_" ,height:1},
    { name: "_" ,height:1},
    { name: "_" ,height:1},
    { name: "_" ,height:1},
    { name: "_" ,height:1},
  ],
});
const onMouseEnter = (column) => {
    console.log('Mouse entered:', column);
    // Your logic for mouse enter event
  };
const onStart = (evt,num) =>{
  console.log(evt)
  if(state[num].length<6){
    state[num].push({name:'_',type:'temp'})
  }
}
const onEnd = (evt,num) =>{
}
const log = (evt,num) => {
  console.log(evt.added.newIndex)
  if (evt.added) {
    if(evt.added.newIndex==6){

    }
    if (state[num][evt.added.newIndex + 1].name === "_") {
      state[num].splice(evt.added.newIndex + 1, 1);
    } else {
      for (let i = evt.added.newIndex + 1; i < state[num].length; i++) {
        if (state[num][i].name === "_") {
          state[num].splice(i, 1);
          break; 
        }
      }
    }
  }else if (evt.removed){
    console.log(state[num][evt.removed.oldIndex])
    state[num].splice(evt.removed.oldIndex , 0, { name: "_" ,height:1});
  }else if (evt.moved){
    if(evt.moved.newIndex==5){
      state[num] = state[num].map(item => {
          if (item.type === 'temp') {
              item.type = 'act';
          }
          return item; 
      });
    }
  }
  state[num] = state[num].filter(item => item.type !== 'temp');
};
</script>

<template>
  <div class="fullBox">
    <!-- 主要的清單 -->
    <div class="mainListBox">
      <draggable
        :list="state.mainList"
        chosen-class="chosenClass"
        animation="300"
        @start="mainStart"
        @end="mainEnd"
        :group="{ name: 'formula', pull: 'clone', put: false }"
        @change="mainLog"
        item-key="name"
      >
        <template #item="{ element }">
          <div class="item">
            {{ element.name }}
          </div>
        </template>
      </draggable>
    </div>
    <!-- 右邊的每個coulumn -->
    <div v-for="(column, index) in columns" :key="index" :class="'column' + (index + 1)" @mouseover="onMouseEnter(column)">
      <draggable
        :list="state[column]"
        chosen-class="chosenClass"
        animation="300"
        @start="(event) => onStart(event, column)"
        @end="(event) => onEnd(event, column)"
        :group="{ name: 'formula'}"
        @change="(event) => log(event, column)"
        :item-key=index
        filter=".opp"
        @beforeEnd="onBeforeEnd"
      >
        <template #item="{ element,index }">
          <div class="item" :class="[element.name === '_' ? 'opp' : '', 'height-' + element.height]"  >
            {{ element.name }}
          </div>
        </template>
      </draggable>
    </div>
    <!-- 程式清單 -->
    <div class="list-box">
      <div v-for="column in columns" :key="column" class="list">
        <div v-for="item in state[column]" :key="item.id">
          <p v-if="item.name !== '_'">{{ item.name }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.fullBox{
  display: flex;
}
.mainListBox{
  height: 240px;
  width: 300px;
  color: black;
  background-color: aliceblue;
}
.column1{
  height: 240px;
  width: 50px;
  background-color: rgb(72, 118, 158);
  color: black;
  /* overflow: hidden; */
}
.column2{
  height: 240px;
  width: 50px;
  background-color: rgb(160, 207, 249);
  color: black;
  /* overflow: hidden; */
}
.opp{
  background-color: rgba(249, 249, 204, 0.661) !important;
  color: rgba(255, 255, 255, 0);
}

.item{
  background-color: rgb(150, 155, 155);
  margin-bottom: 10px;
  height: 30px;
}
.list-box{
  display: flex;
  flex-wrap: wrap;
  width: 300px;
  background-color: rgb(255, 205, 172);
  position: relative;
}

.list{
  background-color: brown;
  height: 30px;
  color: rgb(234, 233, 233);
  margin: 10px;
  display: flex;
  align-items: center;
}
.height-1 {
  height: 30px; /* 设置您想要的高度 */
  background-color: beige;
}

.height-2 {
  height: 70px; /* 设置您想要的高度 */
  background-color: blueviolet;
}

.height-3 {
  height: 110px; /* 设置您想要的高度 */
}
</style>
