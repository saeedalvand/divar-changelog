<template>
  <img alt="Vue logo" src="./assets/logo.png">  
  <table>
    <thead>
        <th>شماره</th>
        <th> نام شخص</th>
        <th>تاریخ</th>
        <th>عنوان</th>
        <th>فیلد</th>
        <th>قبلی</th>
        <th>جدید</th>
    </thead>    
    <tbody>      
        <row  v-for="item in dataCache" :key="item.id" :changelog="item"/>             
    </tbody>
</table>
</template>

<script>
import row from './components/row.vue'
import inputData from './assets/data.json'
const step = 20;
var lastCachedItem = step;
export default {
  name: 'App',
  components: {
    row
  }, methods:{    
    loadMore(){
      if(window.scrollMaxY == window.scrollY){
        console.log('calling handleScroll');
        lastCachedItem += step;
        this.dataCache = inputData.slice(0,lastCachedItem)
      }
    }
  },
  data:function () {
    let cache = inputData.slice(0,step)
    return{
      changelogs : inputData,
      dataCache: cache
    }
  },
  created(){
    console.log('mounted')
    window.addEventListener('scroll', this.loadMore);
  },     
  unmounted () {
    window.removeEventListener('scroll', this.loadMore);
  },
}
</script>

<style>

@font-face {
  font-family: "vazir";
  src: local("vazir"),   url("./assets/fonts/vazir.ttf") format("truetype");
}

#app {
  direction: rtl;  
  font-family: "vazir";
}
table{
  border: 1px solid gray;
}
thead{
  background-color: #ffe26a;
}
tr{
  border-top: 1px solid lightgray;
}
</style>
