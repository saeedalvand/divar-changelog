<template>  
  <tr>
    <td>{{changelog.id}}</td>    
    <td>{{changelog.name}}</td>    
    <td>{{changelog.date}}</td>    
    <td>{{changelog.title}}</td>    
    <td>{{changelog.field}}</td>    
    <td>{{changelog.old_value}}</td>    
    <td>{{changelog.new_value}}</td>    
  </tr>
</template>

<script>
export default {
  name: 'row',
  props: {
    changelog: Object
  }
}
</script>

<style>

td {  
  text-align: center;
  border-left: 1px solid lightgray;
  border-top: 1px solid lightgray;
}
</style>
