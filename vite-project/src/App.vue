<script setup>
import { ref, reactive, watch, computed,onBeforeMount } from "vue";
import draggable from "vuedraggable";
import ListBarrier from './components/list/ListBarrier.vue';
import ListCCX from './components/list/ListCCX.vue';
import ListControlledNot from './components/list/ListControlledNot.vue';
import ListH from './components/list/ListH.vue';
import ListID from './components/list/ListID.vue';
import ListIP from './components/list/ListIP.vue';
import ListMeasure from './components/list/ListMeasure.vue';
import ListNotX from './components/list/ListNotX.vue';
import ListRC3X from './components/list/ListRC3X.vue';
import ListRCCX from './components/list/ListRCCX.vue';
import ListReset from './components/list/ListReset.vue';
import ListRX from './components/list/ListRX.vue';
import ListRXX from './components/list/ListRXX.vue';
import ListRY from './components/list/ListRY.vue';
import ListRZ from './components/list/ListRZ.vue';
import ListRZZ from './components/list/ListRZZ.vue';
import ListS from './components/list/ListS.vue';
import ListSDragger from './components/list/ListSDragger.vue';
import ListSWAP from './components/list/ListSWAP.vue';
import ListSX from './components/list/ListSX.vue';
import ListSXdg from './components/list/ListSXdg.vue';
import ListT from './components/list/ListT.vue';
import ListTDragger from './components/list/ListTDragger.vue';
import ListU from './components/list/ListU.vue';
import ListY from './components/list/ListY.vue';
import ListZ from './components/list/ListZ.vue';
// drag
import DragBarrier from './components/drag/dragBarrier.vue';
import DragCCX from './components/drag/dragCCX.vue';
import DragCX from './components/drag/dragCX.vue';
import DragP from './components/drag/dragP.vue';
import DragRc3x from './components/drag/dragRc3x.vue';
import DragRccx from './components/drag/dragRccx.vue';
import DragRX from './components/drag/dragRX.vue';
import DragRXX from './components/drag/dragRXX.vue';
import DragRY from './components/drag/dragRY.vue';
import DragRZ from './components/drag/dragRZ.vue';
import DragRZZ from './components/drag/dragRZZ.vue';
import DragSWAP from './components/drag/dragSWAP.vue';
import DragU from './components/drag/dragU.vue';
import DragX from './components/drag/dragX.vue';
const props = defineProps(['element']);

const getComponent = (name) => {
  switch (name) {
    case 'Barrier':
      return ListBarrier;
    case 'CCX':
      return ListCCX;
    case 'ControlledNot':
      return ListControlledNot;
    case 'H':
      return ListH;
    case 'ID':
      return ListID;
    case 'IP':
      return ListIP;
    case 'Measure':
      return ListMeasure;
    case 'NotX':
      return ListNotX;
    case 'RC3X':
      return ListRC3X;
    case 'RCCX':
      return ListRCCX;
    case 'Reset':
      return ListReset;
    case 'RX':
      return ListRX;
    case 'RXX':
      return ListRXX;
    case 'RY':
      return ListRY;
    case 'RZ':
      return ListRZ;
    case 'RZZ':
      return ListRZZ;
    case 'S':
      return ListS;
    case 'SDragger':
      return ListSDragger;
    case 'SWAP':
      return ListSWAP;
    case 'SX':
      return ListSX;
    case 'SXdg':
      return ListSXdg;
    case 'T':
      return ListT;
    case 'TDragger':
      return ListTDragger;
    case 'U':
      return ListU;
    case 'Y':
      return ListY;
    case 'Z':
      return ListZ;
    default:
      return null; // 或者其他的預設組件
  }
};
const getComponentDrag = (name) => {
  switch (name) {
    case 'Barrier':
      return DragBarrier || ListBarrier;
    case 'CCX':
      return DragCCX || ListCCX;
    case 'ControlledNot':
      return DragCX || ListCX;
    case 'P':
      return DragP || ListP;
    case 'RC3X':
      return DragRc3x || ListRC3X;
    case 'RCCX':
      return DragRccx || ListRCCX;
    case 'RX':
      return DragRX || ListRX;
    case 'RXX':
      return DragRXX || ListRXX;
    case 'RY':
      return DragRY || ListRY;
    case 'RZ':
      return DragRZ || ListRZ;
    case 'RZZ':
      return DragRZZ || ListRZZ;
    case 'SWAP':
      return DragSWAP || ListSWAP;
    case 'U':
      return DragU || ListU;
    case 'X':
      return DragX || ListX;
    case 'H':
      return ListH;
    case 'ID':
      return ListID;
    case 'IP':
      return ListIP;
    case 'Measure':
      return ListMeasure;
    case 'NotX':
      return DragX;
    case 'Reset':
      return ListReset;
    case 'S':
      return ListS;
    case 'SDragger':
      return ListSDragger;
    case 'SX':
      return ListSX;
    case 'SXdg':
      return ListSXdg;
    case 'T':
      return ListT;
    case 'TDragger':
      return ListTDragger;
    case 'Y':
      return ListY;
    case 'Z':
      return ListZ;
    default:
      return null; // 或者其他的默认组件
  }
};
// 右鍵事件
const showContextMenu = ref(false);
const showContextMenuMap = ref({});
const contextMenuPosition = ref({ top: 0, left: 0 });
const handleContextMenu = (index, column, event) => {
    event.preventDefault();
    console.log(index)
    console.log(column)
    console.log(event)
    showContextMenu.value = true
    contextMenuPosition.value = {
        top: event.clientY,
        left: event.clientX
    };
};
// https://www.itxst.com/vue-draggable/vnqb7fey.html
const limitHeight = ref(6)
const columnNum = ref(10)
const columns = reactive([])
const state = reactive({
  mainList: [
  { name: "H", height: 1, id: 1 },
  { name: "NotX", height: 1, id: 2 },
  { name: "ControlledNot", height: 2, id: 3 },
  { name: "CCX", height: 3, id: 4 },
  { name: "SWAP", height: 2, id: 5 },
  { name: "ID", height: 1, id: 6 },
  { name: "T", height: 1, id: 7 },
  { name: "S", height: 1, id: 8 },
  { name: "Z", height: 1, id: 9 },
  { name: "tdg", height: 1, id: 10 },
  { name: "sdg", height: 1, id: 11 },
  { name: "P", height: 1, id: 12 },
  { name: "SX", height: 1, id: 13 },
  { name: "SXdg", height: 1, id: 14 },
  { name: "Y", height: 1, id: 15 },
  { name: "RX", height: 1, id: 16 },
  { name: "RY", height: 1, id: 17 },
  { name: "RZ", height: 1, id: 18 },
  { name: "RXX", height: 2, id: 19 },
  { name: "RZZ", height: 2, id: 20 },
  { name: "U", height: 1, id: 21 },
  { name: "RCCX", height: 3, id: 22 },
  { name: "RC3X", height: 4, id: 23 },
  { name: "reset", height: 1, id: 24 },
  { name: "Barrier", height: 1, id: 25 },
  { name: "Measure", height: 1, id: 26 },
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
  startHeight = evt.target.__draggable_component__.context.element.height
  if(state[num].length<limitHeight){
    state[num].push({name:'_',type:'temp'})
  }
}
const onEnd = (evt,num) =>{
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
const newColumn =()=>{
    columnNum.value++
    const listName = 'list' + columnNum.value;
    columns.push(listName);
    state[listName] = Array.from({ length: 5 }, () => ({ name: "_", height: 1 }));
    state[listName].push({ name: "__", height: 1 });
    console.log(state)
}
const download = () => {
  let indexedArray = printList.map(function(element, index) {
      return index + ' ' + element;
  });
  let content = indexedArray.join('\n');
  // 建立 Blob 物件
  const blob = new Blob([content], { type: 'text/plain' });
  
  // 建立下載連結
  const link = document.createElement('a');
  link.href = window.URL.createObjectURL(blob);
  link.download = 'data.txt';

  // 觸發點擊連結
  link.click();
}

const totalHeightList1 = computed(() => {
  let height = state.list1.reduce((acc, item) => acc + item.height, 0);
  // console.log(height)
  return height
});
let printList = []
watch(state, (newValue, oldValue) => {
  console.log(newValue);
  let list = []
  for(let i =0; i<columns.length;i++){
    let heightNow=0
    for(let j=0;j<state[columns[i]].length;j++){
      let item = state[columns[i]][j]
      // item.name!== '_' &&item.name!== '__'
      if(item.height==1){
        let name = item.name
        let index = heightNow
        let fullname=name+' '+'q['+index+'];'
        if(item.name!== '_' &&item.name!== '__'){
          list.push(fullname)
        }
        heightNow++
      }else{
        let name = item.name
        let indexblock=''
        console.log(item.height)
        for(let k=0;k<item.height;k++){
          let index = heightNow+k
          indexblock+=' q['+index+'] '
          console.log(indexblock)
        }
        heightNow+=item.height
        console.log(heightNow)
        let fullname=name+indexblock+';'
        if(item.name!== '_' &&item.name!== '__'){
          list.push(fullname)
        }
      }
    }
  }
  console.log( list)
  printList = list
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
    <div class="lineIndex">
      <p  v-for="(item, index) in 5" >q[{{item-1}}]</p>
    </div>
    <div class="columnBox">
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
            <div class="item" 
            @mouseleave="showContextMenu = false"
            @contextmenu.prevent="handleContextMenu(index, column, $event)"
  :class="[element.name === '_' || element.name === '__' ? 'opp' : '', 'height-' + element.height],element.name === '__' ?'none':''"  >
              <component :is="getComponentDrag(element.name)">
              </component>
              <span v-if="element.name != '_' && element.name != '__'" @click="deletItem(element,index,column)" class="delete">x</span>
            </div>
          </template>
        </draggable>
      </div>
    </div>
    <div>
      <p @click="newColumn()" class="new">新增</p>
    </div>
    <!-- 程式清單 -->
    <div class="rightBlock">
      <div class="list-box" >
        <div v-for="(item,index) in printList"  class="list">
          <p class="indexNum">{{ index }}</p>
          <div class="item">
            <p>{{ item }}</p>
          </div>
        </div>
      </div>
      <div class="download" @click="download()">
        <p>下載</p>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.fullBox{
  width: 100%;
  display: flex;
  align-items: flex-start;
  justify-content: center;
}
.new{
  margin-left: 10px;
  width: 50px;
  display: flex;
  justify-content: center;
  background-color: rgb(200, 219, 235);
  color: black;
  cursor: pointer;
  &:hover{
    background-color: rgb(137, 155, 199);
  }
}
.lineIndex{
  width: 60px;
  height: 210px;
  display: flex;
  flex-wrap: wrap;
  p{
    width: 100%;
    height: 40px;
    margin: 0;
  }
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
.columnBox{
  width: 800px;
  display: flex;
  overflow-x: scroll;
  overflow-y: hidden;
}
.column{
  // height: 150px;
  height: 200px;
  min-width: 100px;
  background-color: rgb(92, 92, 92);
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
  background-color: rgba(249, 249, 204, 0) !important;
  // border-bottom:  none !important;
  color: rgba(255, 255, 255, 0);
}

.item{
  // background-color: rgb(150, 155, 155);
  height: 30px;
  position: relative;
  display: flex;
  justify-content: center;
  border-bottom: 1px solid rgb(255, 255, 255);
}
.context-menu{
  color: rgb(255, 255, 255);
  background-color: rgb(0, 0, 0);
  width: 50px;
  height: 50px;
  // display: flex;
  // align-items: center;
  // justify-content: center;
  // position: absolute;
  // top: 0%;
  // transform: translateY(-50%);
  // cursor: pointer;
}
.delete{
  color: rgb(255, 255, 255);
  background-color: rgb(0, 0, 0);
  width: 15px;
  height: 15px;
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  top: 0px;
  right: 0%;
  // transform: translateY(-50%);
  cursor: pointer;
}
.rightBlock{
  display: flex;
  width: 300px;
  background-color: rebeccapurple;
  margin-left: 60px;
  flex-wrap: wrap;
  position: relative;
  height: 500px;
  .download{
    width: 300px;
    height: 30px;
    position: relative;
    top: 10px;
    background-color: rgb(205, 219, 255);
    color:rgb(64, 58, 99);
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    &:hover{
      background-color: rgb(64, 58, 99);
      color: white;
    }
  }
}
.list-box{
  display: flex;
  flex-wrap: wrap;
  width: 100%;
  height: 100%;
  flex-direction: column;
  background-color: rgb(255, 255, 255);
  position: relative;
  .list{
    display: flex;
    align-items: center;
    height: 30px;
    .indexNum{
      color: rgb(58, 58, 58);
    }
  }
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
.none{
  border-bottom: none !important;
}
.height-1 {
  height: 40px; /* 设置您想要的高度 */
  // background-color: beige;
}

.height-2 {
  height: 81px; /* 设置您想要的高度 */
  // background-color: blueviolet;
}

.height-3 {
  height: 122px; /* 设置您想要的高度 */
  // background-color: rgb(88, 139, 25);
}
</style>
