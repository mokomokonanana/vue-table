<template lang="pug">
  #app
    VueTable(:columns="columns" :list="list" :selectMode="selectMode")
      //- 〇行〇列の出力内容変更
      template(v-for="(item, index) in list" #[`body-r${index}c0`]="{col, r}")
        VueTableCell(v-if="r < 5" v-bind="col" :style="{color:'red'}") {{ r }}
      //- 〇列の出力内容変更
      template(#body-c0="{col}") 
        VueTableCell(v-bind="col") test
</template>

<script>
import VueTable, { VueTableSelectMode, VueTableSlotName }from '@/components/VueTable.vue'
import VueTableCell from '@/components/VueTableCell.vue'

export default {
  name: 'app',
  components:{ VueTable, VueTableCell },
    computed:{
    slotName: function(){ return (c, r) => VueTableSlotName(c, r) },
    selectMode: function(){ return VueTableSelectMode.multi }
  },
  data: function(){ return {
    list: [
      { No : 2, name: '名前1'},
      { No : 3, name: '名前1'},
      { No : 2, name: '名前2'},
      { No : 2, name: '名前1'},
      { No : 2, name: '名前1'},
      { No : 2, name: '名前1'},
      { No : 5, name: '名前1'},
      { No : 2, name: '名前1'},
      { No : 2, name: '名前1'},
      { No : 2, name: '名前1'}
    ],
    columns: [
      {label: 'index'},
      {label: 'No' , field: 'No'},
      {label: '名前' , field: 'name'}
    ]
  }}
}
</script>

<style lang="stylus">
#app
  font-family 'Avenir', Helvetica, Arial, sans-serif
  -webkit-font-smoothing antialiased
  -moz-osx-font-smoothing grayscale
  text-align center
  color #2c3e50
  margin-top 60px
</style>
