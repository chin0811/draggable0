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
const boxs = reactive(['list2','list3'])
const state = reactive({
  //需要拖拽的数据，拖拽后数据的顺序也会变化
  list: [
    { name: "程式1", id: 0 ,height:1},
    { name: "程式2", id: 1 ,height:2},
    { name: "程式3", id: 2 ,height:3},
    { name: "程式4", id: 0 ,height:1},
    { name: "程式5", id: 1 ,height:2},
    { name: "程式6", id: 2 ,height:3},
  ],
  list2: [
    { name: "_", id: 0 },
    { name: "_", id: 1 },
    { name: "_", id: 2 },
    { name: "_", id: 3 },
    { name: "_", id: 4 },
  ],
  list3: [

  ],
  list4: [

  ],
});
let index
watch(state.list2, (newValue, oldValue) => {
});
const log = (evt,num) => {
  console.log(evt)
  console.log(num)
};
const onStart = (evt) => {
  // console.log('start')
  // console.log(evt)
};
const onEnd = (evt) => {
  // console.log('end')
  // console.log(evt)
};
</script>

<template>
  <div class="itxst">
    <div class="box1">
      <draggable
        :list="state.list"
        chosen-class="chosenClass"
        animation="300"
        @start="onStart"
        @end="onEnd"
        :group="{ name: 'people', pull: 'clone', put: false }"
        @change="log"
        item-key="name"
      >
        <template #item="{ element }">
          <div class="item">
            {{ element.name }}
          </div>
        </template>
      </draggable>
    </div>
    <div v-for="(box, index) in boxs" :key="index" :class="'box' + (index + 2)">
      <draggable
        :list="state[box]"
        chosen-class="chosenClass"
        animation="300"
        @start="onStart"
        @end="onEnd"
        group="people"
        @change="(event) => log(event, box)"
        item-key="name"
      >
        <template #item="{ element }">
          <div class="item" :class="[element.name === '_' ? 'opp' : '', 'height-' + element.height]">
            {{ element.name }}
          </div>
        </template>
      </draggable>
    </div>
    <!-- <div class="box2">
      <draggable
        :list="state.list2"
        chosen-class="chosenClass"
        animation="300"
        @start="onStart"
        @end="onEnd"
        group="people"
        @change="(event) => log(event, 1)"
        item-key="name"
      >
        <template #item="{ element }">
          <div class="item" :class="[element.name === '_' ? 'opp' : '', 'height-' + element.height]">
            {{ element.name }}
          </div>
        </template>
      </draggable>
    </div>
    <div class="box3">
      <draggable
        :list="state.list3"
        chosen-class="chosenClass"
        animation="300"
        @start="onStart"
        @change="(event) => log(event, 2)"
        @end="onEnd"
        group="people"
      >
        <template #item="{ element }">
          <div class="item" :class="[element.name === '_' ? 'opp' : '', 'height-' + element.height]">
            {{ element.name }}
          </div>
        </template>
      </draggable>
    </div> -->
    <!-- <div class="box2">
      <draggable
        :list="state.list4"
        chosen-class="chosenClass"
        animation="300"
        @start="onStart"
        @end="onEnd"
        group="people"
      >
        <template #item="{ element }">
          <div class="item">
            {{ element.name }}
          </div>
        </template>
      </draggable>
    </div> -->
    <div class="list-box">
      <div class="list" v-for="item in state.list2">
        <p v-if="item.name!='_'">{{ item.name}}</p>
      </div>
      <div class="list" v-for="item in state.list3">
        <p v-if="item.name!='-'">{{ item.name}}</p>
      </div>
    </div>
    <rawDisplayer class="col-3" :value="list1" title="List 1" />

<rawDisplayer class="col-3" :value="list2" title="List 2" />
  </div>
</template>

<style scoped>

.opp{
  background-color: rgba(249, 249, 204, 0.826) !important;
  color: rgba(255, 255, 255, 0);
}
.itxst{
  display: flex;
}
.item{
  background-color: rgb(150, 155, 155);
  margin-bottom: 10px;
}
.list-box{
  display: flex;
  flex-wrap: wrap;
  width: 300px;
  background-color: beige;
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
.box0{
  height: 300px;
  width: 300px;
  background-color: aliceblue;
  color: black;
  position: absolute;
}
.box1{
  height: 300px;
  width: 300px;
  color: black;
  background-color: aliceblue;
}
.box2{
  height: 300px;
  width: 50px;
  background-color: rgb(72, 118, 158);
  color: black;
}
.box3{
  height: 300px;
  width: 50px;
  background-color: rgb(153, 195, 232);
  color: black;
}
.height-1 {
  height: 30px; /* 设置您想要的高度 */
  background-color: beige;
}

.height-2 {
  height: 60px; /* 设置您想要的高度 */
  background-color: blueviolet;
}

.height-3 {
  height: 90px; /* 设置您想要的高度 */
}
</style>
