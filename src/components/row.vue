<template>  
  <tr>
    <td>{{changelog.id}}</td>    
    <td>{{changelog.name}}</td>    
    <td>{{changelog.date}}</td>    
    <td>{{changelog.title}}</td>    
    <td>{{changelog.field}}</td>    
    <td>{{changelog.old_value}}</td>    
    <td>{{changelog.new_value}}</td>    
    <td class="star" :class="classList" @click="starClicked($event,changelog.id)">&#x2605;</td>    
  </tr>
</template>

<script>
export default {
  name: 'row',
  props: {
    changelog: Object
  },
  data(){
    return {isStarred:false}
  },
  computed: {
    classList: function() {
        return this.isStarred ? 'active' : '';
    }
  },
  methods:{
    starClicked(event,id){
      console.log('star clicked on: '+id)
      event.target.classList.toggle('active')
      if(localStorage){
        let starredId = id;
        var starredList = [];
        if (localStorage.getItem("starredList"))
            starredList = JSON.parse(localStorage.getItem("starredList"));
        if (!starredList.includes(starredId))
            starredList.push(starredId);
        else
            starredList.includes(starredId) && starredList.splice(starredList.indexOf(starredId), 1);
        localStorage.setItem("starredList", JSON.stringify(starredList))
      }
    }
  },
  mounted(){    
    if (localStorage && localStorage.getItem("starredList"))
      var starredList = JSON.parse(localStorage.getItem("starredList"));
      if(starredList.includes(this.changelog.id))
        this.isStarred = true
  }
}
</script>

<style>

td {  
  text-align: center;
  border-left: 1px solid lightgray;
  border-top: 1px solid lightgray;
}
.star{
  cursor: pointer;
  color: #aaa;
}
.star.active{
  cursor: pointer;
  color: gold;
}
</style>
