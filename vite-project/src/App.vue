<script setup>
import { ref, reactive, watch, computed,onBeforeMount } from "vue";
import draggable from "vuedraggable";
import CatalogItemH from './components/CatalogItemH.vue';
import CatalogItemX from './components/CatalogItemX.vue';
import CatalogItemCX from './components/CatalogItemCX.vue';
import CatalogItemCCX from './components/CatalogItemCCX.vue';
import CatalogItemSWAP from './components/CatalogItemSWAP.vue';
import CatalogItemID from './components/CatalogItemID.vue';
import CatalogItemT from './components/CatalogItemT.vue';
import CatalogItemS from './components/CatalogItemS.vue';
import CatalogItemZ from './components/CatalogItemZ.vue';
import CatalogItemTdagger from './components/CatalogItemTdagger.vue';
import CatalogItemSdagger from './components/CatalogItemSdagger.vue';
import CatalogItemP from './components/CatalogItemP.vue';
import CatalogItemRZ from './components/CatalogItemRZ.vue';
console.log(CatalogItemH)
const props = defineProps(['element']);

const getComponent = (name) => {
  switch (name) {
    case 'H':
      return CatalogItemH;
    case 'X':
      return CatalogItemX;
    case 'CX':
      return CatalogItemCX;
    case 'CCX':
      return CatalogItemCCX;
    case 'SWAP':
      return CatalogItemSWAP;
    case 'ID':
      return CatalogItemID;
    case 'T':
      return CatalogItemT;
    case 'S':
      return CatalogItemS;
    case 'Z':
      return CatalogItemZ;
    case 'Tdagger':
      return CatalogItemTdagger;
    case 'Sdagger':
      return CatalogItemSdagger;
    case 'p':
      return CatalogItemP;
    case 'RZ':
      return CatalogItemRZ;
    default:
      return null; // 或者其他的預設組件
  }
};
// https://www.itxst.com/vue-draggable/vnqb7fey.html
const limitHeight = ref(6)
const columns = reactive([])
const state = reactive({
  mainList: [
    { name: "H", height: 1, id: 1 },
    { name: "X", height: 1, id: 2 },
    { name: "CX", height: 2, id: 3 },
    { name: "CCX", height: 3, id: 4 },
    { name: "SWAP", height: 2, id: 5 },
    { name: "ID", height: 1, id: 6 },
    { name: "T", height: 1, id: 7 },
    { name: "S", height: 1, id: 8 },
    { name: "Z", height: 1, id: 9 },
    { name: "Tdagger", height: 1, id: 10 },
    { name: "Sdagger", height: 1, id: 11 },
    { name: "p", height: 1, id: 12 },
    { name: "RZ", height: 1, id: 13 }
  ]
});
onBeforeMount(() => {
  // Generate 10 lists
  for (let i = 1; i <= 10; i++) {
    const listName = 'list' + i;
    columns.push(listName);
    state[listName] = Array.from({ length: 5 }, () => ({ name: "_", height: 1 }));
    state[listName].push({ name: "__", height: 1 });
  }
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
let needDelete = false
let lastIndex
const log = (evt,num) => {
  console.log('log')
  if (evt.added) {
    let itemHeight=evt.added.element.height
    let addedIndex = evt.added.newIndex;
    let heightToRemove = itemHeight;

    // 往下刪除
    for (let i = addedIndex + 1; i < state[num].length && heightToRemove > 0; i++) {
        if (state[num][i].name === "_") {
            state[num].splice(i, 1);
            heightToRemove--;
            i--; // 因為刪除了一個元素，索引需要減一
        }
    }

    // 如果往下刪除不足夠，再往上刪除
    for (let i = addedIndex - 1; i >= 0 && heightToRemove > 0; i--) {
        if (state[num][i].name === "_") {
            state[num].splice(i, 1);
            heightToRemove--;
        }
    }
    // console.log(needDelete)
    // console.log(evt.added.newIndex)
    // console.log(lastIndex)
    // if(needDelete==true&&evt.added.newIndex>lastIndex){
    //   state[num].splice(-2, 1);;
    //   needDelete=false
    //   lastIndex=null
    // }

  }else if (evt.removed){
    let itemHeight=evt.removed.element.height
    for(let i =0; i<itemHeight;i++){
      state[num].splice(evt.removed.oldIndex , 0, { name: "_" ,height:1});
    }
  }else if (evt.moved){
    console.log('temp')
    if(evt.moved.newIndex==3){
      console.log('temp2')
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
  console.log(evt)
  // console.log(num)
  // console.log(evt.relatedContext.list)
  // console.log(evt.from.__draggable_component__.list)
  // console.log(evt.relatedContext.list==evt.from.__draggable_component__.list)
  let sameList = evt.relatedContext.list==evt.from.__draggable_component__.list
  let toList = evt.to.__draggable_component__.list
  let totalHeight = 0;
  let visualHeight = 0
  for (let i = 0; i < toList.length; i++) {
    visualHeight += toList[i].height;
    if (toList[i].name !== "_") {
      totalHeight += toList[i].height;
    }
  }
  if(startHeight> limitHeight.value-totalHeight&&!sameList){
    return false
  }
  if(startHeight+evt.related.__draggable_context.index>=limitHeight.value&&!sameList){
    return false
  }
  if (evt.relatedContext.element.name == '__') return false;
  lastIndex = evt.relatedContext.list.length-2
  if(evt.relatedContext.list[lastIndex].name!='_'){
    needDelete = true
  }
  // if(visualHeight >= limitHeight.value&&!sameList){
  //   let lastIndex = evt.relatedContext.list.length-1
  //   if(evt.relatedContext.list[lastIndex].name!='_'){
  //     let endHeight = evt.relatedContext.list[lastIndex].height
  //     if(evt.draggedContext.futureIndex>=limitHeight.value-endHeight){
  //       return false
  //     }
  //   }
  //   let letSpace = 0
  //   while(lastIndex >= 0 && evt.relatedContext.list[lastIndex].name === '_'){
  //     letSpace++;
  //     lastIndex--;
  //   }
  //   console.log(letSpace)
  //   if(startHeight>letSpace){
  //     return false
  //   }
  // }
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
  let visualHeight = 0
  for (let i = 0; i < toList.length; i++) {
    visualHeight += toList[i].height;
    if (toList[i].name !== "_") {
      totalHeight += toList[i].height;
    }
  }
  console.log(visualHeight)
  if(startHeight> limitHeight.value-totalHeight&&!sameList){
    return false
  }
  if(startHeight+evt.related.__draggable_context.index>=limitHeight.value){
    return false
  }
  if (evt.relatedContext.element.name == '__') return false;
  lastIndex = evt.relatedContext.list.length-2
  if(evt.relatedContext.list[lastIndex].name!='_'){
    needDelete = true
  }
  // if(visualHeight >= limitHeight.value){
  //   let lastIndex = evt.relatedContext.list.length-1
  //   if(evt.relatedContext.list[lastIndex].name!='_'){
  //     let endHeight = evt.relatedContext.list[lastIndex].height
  //     if(evt.draggedContext.futureIndex>=limitHeight.value-endHeight){
  //       return false
  //     }
  //   }
  //   let letSpace = 0
  //   while(lastIndex >= 0 && evt.relatedContext.list[lastIndex].name === '_'){
  //     letSpace++;
  //     lastIndex--;
  //   }
  //   console.log(letSpace)
  //   if(startHeight>letSpace){
  //     return false
  //   }
  // }
}
const onClone = (evt,num) => {
  console.log('clone')
}
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
        animation="300"
        @start="mainStart"
        @end="mainEnd"
        :group="{ name: 'formula', pull: 'clone', put: false }"
        @change="mainLog"
        item-key="name"
        :sort="false"
        :move="(event) => mainMove(event)"
      >
        <template #item="{ element }">
          <div class="mainItem">
            <component :is="getComponent(element.name)"></component>
          </div>
        </template>
      </draggable>
    </div>
    <!-- 右邊的每個coulumn -->
    <!-- :class="'column' + (index + 1)"  -->
    <div v-for="(column, index) in columns" :key="index" class="column"  @mouseenter="onMouseEnter(column)">
      <draggable
        :list="state[column]"
        chosen-class="chosenClass"
        animation="300"
        @start="(event) => onStart(event, column)"
        @end="(event) => onEnd(event, column)"
        @add="(event) => onAdd(event, column)"
        @clone="(event) => onClone(event, column)"
        :group="{ name: 'formula'}"
        @change="(event) => log(event, column)"
        :item-key=index
        filter=".opp"
        @beforeEnd="onBeforeEnd"
        :move="(event) => onMove(event, column)"
        swapThreshold: 0.20,
      >
        <template #item="{ element,index }">
          <div class="item" :class="[element.name === '_' || element.name === '__' ? 'opp' : '', 'height-' + element.height]"  >
             <component :is="getComponent(element.name)"></component>
            <p v-if="element.name != '_' && element.name != '__'" @click="deletItem(element,index,column)" class="delete">x</p>
          </div>
        </template>
      </draggable>
    </div>
    <!-- 程式清單 -->
    <div class="list-box">
      <div v-for="column in columns" :key="column" class="list">
        <div v-for="(item,index) in state[column]" :key="item.id" >
          <div v-if="item.name !== '_' && item.name != '__'" class="item">
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
.fullBox{
  display: flex;
}
.mainListBox{
  height: 240px;
  width: 300px;
  color: black;
  background-color: aliceblue;
  margin-right: 50px;
  >div{

    width: 100%;
    display: flex;
    flex-wrap: wrap;
    // .mainItem{
    //   background-color: rgb(203, 203, 203);
    //   width: 50px;
    //   margin-right: 10px;
    //   margin-bottom: 10px;
    //   cursor: pointer;
    // }
  }
}
.column{
  // height: 150px;
  height: 200px;
  width: 100px;
  background-color: rgb(255, 255, 255);
  color: black;
  margin-right: 2px;
  position: relative;
}
.column1{
  height: 150px;
  width: 100px;
  background-color: rgb(72, 118, 158);
  color: black;
  margin-right: 10px;
  position: relative; /* 让伪元素定位相对于 .column1 元素 */
  /* overflow: hidden; */
}
.column2{
  height: 150px;
  width: 100px;
  background-color: rgb(160, 207, 249);
  color: black;
  margin-right: 10px;
  /* overflow: hidden; */
}
.column3{
  height: 150px;
  width: 100px;
  background-color: rgba(144, 218, 243, 0.245);
  color: black;
  /* overflow: hidden; */
}
.opp{
  background-color: rgba(249, 249, 204, 0.428) !important;
  color: rgba(255, 255, 255, 0);
}

.item{
  // background-color: rgb(150, 155, 155);
  height: 30px;
  position: relative;
  border-bottom: 1px solid black;
}
.delete{
  color: rgb(255, 255, 255);
  background-color: rgb(0, 0, 0);
  width: 20px;
  height: 20px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
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
  height: 40px; /* 设置您想要的高度 */
  // background-color: beige;
}

.height-2 {
  height: 80px; /* 设置您想要的高度 */
  background-color: blueviolet;
}

.height-3 {
  height: 120px; /* 设置您想要的高度 */
  background-color: rgb(88, 139, 25);
}
</style>
