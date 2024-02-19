<script setup>
import { ref, reactive, watch, computed } from "vue";
import draggable from "vuedraggable";

// https://www.itxst.com/vue-draggable/vnqb7fey.html
const limitHeight = ref(4)
const columns = reactive(['list1','list2','list3'])
const state = reactive({
  mainList: [
    { name: "皮卡丘" ,height:1,id:1},
    { name: "妙蛙種子" ,height:1,id:2},
    { name: "小火龍" ,height:2,id:3},
    { name: "傑尼龜" ,height:3,id:4},
    { name: "波波" ,height:2,id:5},
    { name: "綠毛蟲" ,height:1,id:6},
  ],
  list1: [
  ],
  list2: [
  ],
  list3: [
  ],
});

let startHeight = 0
const onStart = (evt,num) =>{
  console.log(evt)
  startHeight = evt.target.__draggable_component__.context.element.height
  console.log('start')
  if(state[num].length<limitHeight){
    state[num].push({name:'_',type:'temp'})
  }
}
const onEnd = (evt,num) =>{
  console.log('end')

}
const log = (evt,num) => {
  console.log('log')
  if (evt.added) {
    let itemHeight=evt.added.element.height
    for(let i =0; i<itemHeight;i++){
      console.log('test1')
      if (state[num][evt.added.newIndex + 1].name === "_") {
        console.log('test2')
        state[num].splice(evt.added.newIndex + 1, 1);
      }else if(state[num][evt.added.newIndex - 1].name === "_"){
        console.log('test3')
        state[num].splice(evt.added.newIndex - 1, 1);
      }else {
        console.log('test')
        for (let i = evt.added.newIndex + 1; i < state[num].length; i++) {
          if (state[num][i].name === "_") {
            state[num].splice(i, 1);
          }
        }
      }
    }
  }else if (evt.removed){
    let itemHeight=evt.removed.element.height
    for(let i =0; i<itemHeight;i++){
      state[num].splice(evt.removed.oldIndex , 0, { name: "_" ,height:1});
    }
  }else if (evt.moved){
    if(evt.moved.newIndex==3){
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
const onMove = (evt,num) => {
  console.log('move')
  // console.log(num)
  // console.log(evt.relatedContext.list)
  // console.log(evt.from.__draggable_component__.list)
  // console.log(evt.relatedContext.list==evt.from.__draggable_component__.list)
  let sameList = evt.relatedContext.list==evt.from.__draggable_component__.list
  let toList = evt.to.__draggable_component__.list
  let totalHeight = 0;

  for (let i = 0; i < toList.length; i++) {
    if (toList[i].name !== "_") {
      totalHeight += toList[i].height;
    }
  }
  if(startHeight> limitHeight.value-totalHeight&&!sameList){
    return false
  }
}
const onAdd = (evt,num) => {
  console.log('add')
}
const mainStart = (evt,num) => {
  console.log('mainStart')
  startHeight = evt.target.__draggable_component__.context.element.height
}
const mainMove = (evt,num) => {
  console.log('mainMove')
  console.log(evt)
  let toList = evt.to.__draggable_component__.list
  let totalHeight = 0;

  for (let i = 0; i < toList.length; i++) {
    if (toList[i].name !== "_") {
      totalHeight += toList[i].height;
    }
  }
  if(startHeight> limitHeight.value-totalHeight&&!sameList){
    return false
  }
}
// const onClone = (evt,num) => {
//   console.log('clone')
// }
const onMouseEnter = (column) => {
  // console.log('Mouse entered column:', column);
};
const deletItem = (element,index,column) => {
  // console.log(element,index,column)
  let itemHeight=element.height
  // console.log(itemHeight)
  state[column][index]= { name: "_" ,height:1};
  for(let i =1; i<itemHeight;i++){
    state[column].splice(index , 0, { name: "_" ,height:1});
  }
}

const totalHeightList1 = computed(() => {
  let height = state.list1.reduce((acc, item) => acc + item.height, 0);
  // console.log(height)
  return height
});

</script>

<template>
  <div class="fullBox">
    <!-- 主要的清單 -->
    <div class="mainListBox">
      <draggable
        :list="state.mainList"
        chosen-class="chosenClass"
        dragClass="dragClass"
        animation="300"
        @start="mainStart"
        @end="mainEnd"
        :group="{ name: 'pokemon', pull: 'clone', put: false }"
        @change="mainLog"
        item-key="name"
        :sort="false"
        :move="(event) => mainMove(event)"
      >
        <template #item="{ element }">
          <div class="mainItem">
            {{ element.name }}
          </div>
        </template>
      </draggable>
    </div>
    <!-- 右邊的每個coulumn -->
    <div v-for="(column, index) in columns" :key="index" :class="'column' + (index + 1)"  @mouseenter="onMouseEnter(column)">
      <draggable
      dragClass="dragClass"
        :list="state[column]"
        chosen-class="chosenClass"
        animation="300"
        @start="(event) => onStart(event, column)"
        @end="(event) => onEnd(event, column)"
        @add="(event) => onAdd(event, column)"
        @clone="(event) => onClone(event, column)"
        :group="{ name: 'pokemon'}"
        @change="(event) => log(event, column)"
        :item-key=index
        filter=".disable"
        @beforeEnd="onBeforeEnd"
        :move="(event) => onMove(event, column)"
      >
        <template #item="{ element,index }">
          <div class="item" :class="[element.name === '_' ? 'disable' : '', 'height-' + element.height]"  >
            {{ element.name }}
          </div>
        </template>
      </draggable>
    </div>
    <!-- 程式清單 -->
    <div class="list-box">
      <div v-for="column in columns" :key="column" class="list">
        <div v-for="(item,index) in state[column]" :key="item.id" >
          <div v-if="item.name !== '_'" class="item">
            <span class="name">{{ item.name }}</span>
            <span v-for="(arr,heightIndex) in item.height">
              q[{{ index + heightIndex }}]
              <span v-if="item.height-heightIndex==1">;</span>
              <span v-else>,</span>
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
// .dragClass{
//   color: #d32525 !important;
//   font-size: 55px;
// }
// .chosenClass{
//   background-color: black;
//   font-size: 40px;
// }
.fullBox{
  display: flex;
}
.mainListBox{
  height: 240px;
  width: 300px;
  // color: black;
  background-color: aliceblue;
  margin-right: 50px;
  >div{

    width: 100%;
    display: flex;
    flex-wrap: wrap;
    .mainItem{
      background-color: rgb(120, 181, 155);
      font-weight: bolder;
      color: rgb(0, 0, 0);
      width: 100%;
      margin-bottom: 10px;
      cursor: pointer;
    }
  }
}
.column1{
  height: 150px;
  width: 100px;
  background-color: rgb(188, 210, 228);
  color: black;
  margin-right: 10px;
  position: relative; /* 让伪元素定位相对于 .column1 元素 */
  /* overflow: hidden; */
}
.column2{
  height: 150px;
  width: 100px;
  background-color: rgb(188, 210, 228);
  color: black;
  margin-right: 10px;
  /* overflow: hidden; */
}
.column3{
  height: 150px;
  width: 100px;
  background-color: rgb(188, 210, 228);
  color: black;
  /* overflow: hidden; */
}
.disable{
  background-color: rgba(249, 249, 204, 0) !important;
  color: rgba(255, 255, 255, 0);
}

.item{
  background-color: rgb(150, 155, 155);
  margin-bottom: 10px;
  height: 30px;
  position: relative;
}
.delete{
  color: rgb(255, 255, 255);
  background-color: rgb(0, 0, 0);
  width: 30px;
  height: 30px;
  position: absolute;
  top: 0%;
  transform: translateY(-50%);
  cursor: pointer;
}
.list-box{
  display: flex;
  flex-wrap: wrap;
  width: 300px;
  background-color: rgb(255, 255, 255);
  position: relative;
  margin-left: 50px;
}

.list{
  width: 100%;
  height: auto;
  .item{
    background-color: rgba(255, 255, 255, 0);
    height: 30px;
    color: rgb(0, 0, 0);
    margin: 10px;
    display: flex;
    align-items: center;
    .name{
      margin-right: 8px;
    }
  }
}
.height-1 {
  height: 30px; /* 设置您想要的高度 */
 background-color: rgb(120, 181, 155);
}

.height-2 {
  background-color: rgb(120, 181, 155);
  height: 30px; /* 设置您想要的高度 */
}

.height-3 {
  background-color: rgb(120, 181, 155);
  height: 30px; /* 设置您想要的高度 */
}
</style>
