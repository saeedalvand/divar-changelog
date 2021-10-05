<template>
  <img alt="Vue logo" src="./assets/logo.png">  
  <div class="filters">
    <form @submit.prevent="applyFilters">
        <div class="form-group">        
            <label for="name">نام تغییردهنده</label>
            <input type="text" id="name" name="name" :value="filters.name">
        </div>
        <div class="form-group">
            <label for="date">تاریخ</label>
            <input type="date" id="date" name="date" :value="filters.date">
        </div>        
        <div class="form-group">        
            <label for="title">عنوان آگهی</label>
            <input type="text" id="title" name="title" :value="filters.title">
        </div>
        <div class="form-group">        
            <label for="field">فیلد</label>
            <input type="text" id="field" name="field" :value="filters.field">
        </div>
        <div class="buttons">          
          <input type="submit" value="اعمال">
          <button id="reset" @click="resetFilters">حذف فیلترها</button>
        </div>
    </form>
  </div>
  <div>مرتب‌سازی کنونی بر اساس: <span id="current-sort">{{this.sortParam}}</span></div>
  <div class="table-container">
    <table>
      <thead>
          <th>شماره <span title="مرتب‌سازی" class="sort-button" @click="changeSortTo('id')">&#x25BC;</span></th>
          <th> نام شخص</th>
          <th>تاریخ <span title="مرتب‌سازی" class="sort-button" @click="changeSortTo('date')">&#x25BC;</span></th>
          <th>عنوان</th>
          <th>فیلد</th>
          <th>قبلی</th>
          <th>جدید</th>
          <th>&#x2605;</th>
      </thead>    
      <tbody>      
          <row  v-for="item in dataCache" :key="item.id" :changelog="item"/>             
      </tbody>
    </table>
  </div>
</template>

<script>
import row from './components/row.vue'
import inputData from './assets/data.json'
const step = 20;
var lastCachedItem = step;

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
    urlSearch: ''
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
        if(this.sortParam === 'id')
          this.dataCache = this.filteredData.slice(0,lastCachedItem)
        else if (this.sortParam === 'date')
          this.dataCache = this.filteredSortedData.slice(0,lastCachedItem)
      }
    },
    changeSortTo(by){
      console.log('sort by '+ by)
      this.sortParam = by
      lastCachedItem = step;
      if(by==='date'){
        this.dataCache = this.filteredSortedData.slice(0,step)
      }
      else if(by==='id'){
        this.dataCache = this.filteredData.slice(0,step)
      }
    },
    applyFilters(){
      let inputs = document.getElementsByTagName('input')
      let formResult = analyzeForm(inputs)
      let filtersString = formResult.urlSearch
      history.pushState(null,'',filtersString)
      let tempUnsorted = this.rawData
      let tempSorted = this.sortedByDate
      this.filters = formResult.filters
      for (const key in this.filters) {          
        let value = this.filters[key];
        tempUnsorted = tempUnsorted.filter(function(item){
          return item[key].includes(value)
        },this)
        tempSorted = tempSorted.filter(function(item){
          return item[key].includes(value)
        },this)
      }      
      this.dataCache = tempUnsorted.slice(0,step)
      lastCachedItem = step
      this.filteredData = tempUnsorted
      this.filteredSortedData = tempSorted
    },

    resetFilters(){
      if(this.filters === {})
        return
      console.log('resetting')
      this.filters = {}
      this.filteredData = this.rawData
      this.filteredSortedData = this.sortedByDate
      this.dataCache = (this.sortParam==='id')?this.rawData.slice(0,step):this.sortedByDate.slice(0,step)
      for(let element of document.getElementsByTagName('input')){
          if(element.type !== 'submit')
            element.value = ''
      }
      history.pushState(null,'','/')
      console.log('reset')
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
      rawData : inputData,
      filteredData: inputData,
      sortedByDate: sorted,
      filteredSortedData: sorted,
      dataCache: cache,
      filters: {},
      sortParam: 'id'
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
    for(let element of document.getElementsByTagName('input')){
        if(element.type !== 'submit')
          element.value = filters[element.name]
    }
    this.applyFilters()
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
  justify-content: space-around;
}
.table-container{
  overflow: auto;
}
input[type="submit"], button{
  cursor: pointer;
}
@media only screen and (max-width: 997px) {
  form{
    flex-direction: column;
  }
}
</style>
