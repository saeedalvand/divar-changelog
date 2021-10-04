<template>
  <img alt="Vue logo" src="./assets/logo.png">  
  <div class="filters">
    <form @submit.prevent="applyFilters">
        <div class="form-group">        
            <label for="name">نام تغییردهنده</label>
            <input type="text" id="name" name="name" :value="filters.name">
        </div>
        <div class="form-group">        
            <label for="field">فیلد</label>
            <input type="text" id="field" name="field" :value="filters.field">
        </div>
        <div class="form-group">        
            <label for="title">عنوان آگهی</label>
            <input type="text" id="title" name="title" :value="filters.title">
        </div>
        <div class="form-group">
            <label for="date">تاریخ</label>
            <input type="date" id="date" name="date" :value="filters.date">
        </div>        
        <input type="submit" value="اعمال">
    </form>
  </div>
  <table>
    <thead>
        <th>شماره <span title="مرتب‌سازی" class="sort-button" @click="changeSortTo('id')">&#x25BC;</span></th>
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
/**
This function analyzes the filters' form and extracts a data object and a string for url
*/
function  analyzeForm(inputs){
  var result = {
    filters:{
      name: '',
      field: '',
      title: '',
      date: ''
    },
    urlSearch: '?'
  }
  let text = '?'
  for(var i=0; i<4; i++)  {
    if(inputs[i].value){
      text+=inputs[i].name+'='+inputs[i].value+'&'
      result.filters[inputs[i].name] = inputs[i].value
    }
  }
  
  result.urlSearch = text.substr(0,text.length-1);
  return result
}
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
      if(by==='date'){
        this.dataCache = this.sortedByDate.slice(0,step)
      }
      else if(by==='id'){
        this.dataCache = this.changelogs.slice(0,step)
      }
    },

    applyFilters(){
      let inputs = document.getElementsByTagName('input')
      let formResult = analyzeForm(inputs)
      let filtersString = formResult.urlSearch
      history.pushState(null,'',filtersString)
      this.filters = formResult.filters
      let temp = this.changelogs.filter(x => x.name.includes(this.filters.name))
      console.log(temp.length)
      this.dataCache = temp.slice(0,step)
      lastCachedItem = step
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
      dataCache: cache,
      filters: {}
    }
  },
  mounted(){
    console.log('mounted')
    window.addEventListener('scroll', this.loadMore);
    let urlParams = new URLSearchParams(window.location.search)
    let filters = {
      name: urlParams.get('name') || '',
      field: urlParams.get('field') || '',
      title: urlParams.get('title') || '',
      date: urlParams.get('date') || ''
    }
    this.filters = filters
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

.sort-button{
  cursor: pointer;
}
form{
  display: flex;
}
</style>
