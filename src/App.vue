<template>
  <img alt="Vue logo" src="./assets/logo.png">  
  <table>
    <thead>        
        <th>شماره <span title="مرتب‌سازی" class="sort-button active" @click="changeSortTo('id')">&#x25BC;</span></th>
        <th> نام شخص</th>
        <th>تاریخ <span title="مرتب‌سازی" class="sort-button" @click="changeSortTo('date')">&#x25BC;</span></th>
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
var sortParam = 'id'
export default {
  name: 'App',
  components: {
    row
  }, methods:{    
    loadMore(){
      if(window.scrollMaxY == window.scrollY){
        console.log('calling handleScroll');
        lastCachedItem += step;
        if(sortParam === 'id')
          this.dataCache = this.changelogs.slice(0,lastCachedItem)
        else if (sortParam === 'date')
          this.dataCache = this.sortedByDate.slice(0,lastCachedItem)
      }
    },
    changeSortTo(by){
      console.log('sort by '+ by)
      sortParam = by
      lastCachedItem = step;
      if(by==='date')
        this.dataCache = this.sortedByDate.slice(0,step)
      else if(by==='id')
        this.dataCache = this.changelogs.slice(0,step)

      document.getElementsByClassName('sort-button')[0].classList.toggle('active')
      document.getElementsByClassName('sort-button')[1].classList.toggle('active')
    }
  },
  data:function () {
    let sorted = inputData.slice()
    sorted.sort((b,a) => {
      if (a.date > b.date) {
        return 1;
      }
      if (a.date < b.date) {
        return -1;
      }
      return 0;
    });
    let cache = inputData.slice(0,step)
    return{
      changelogs : inputData,
      sortedByDate: sorted,
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
.sort-button.active{
  color: #ddc049;
}
.sort-button:not(.active){
  cursor: pointer;
}

</style>
