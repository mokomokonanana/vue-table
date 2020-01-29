<template lang="pug">
  .header
    template(v-for="(col, index) in header" ) 
      VueTableHeaderCol(v-bind="col")
</template>

<script>
import VueTableHeaderCol from '@/components/VueTableHeaderCol.vue'

export default {
  components:{ VueTableHeaderCol },
  props:{
    columns: Array,
    leftSticky: Number,
  },
  computed:{
    header: function(){
      return this.columns.reduce((result, current, index, list) => {
        // 単段ヘッダ追加
        if(!Array.isArray(current.label)){
          result.push(this.createColumn(current))
          if(current.stickyLeft) result[result.length - 1].stickyLeft = current.stickyLeft
          return result
        }

        // 多段ヘッダ追加
        //  一件目 || 前列が単段
        if(result.length < 1 || !result[result.length - 1].child){
          result.push(this.createColumns(current, 0))
          if(current.stickyLeft) result[result.length - 1].stickyLeft = current.stickyLeft
          return result
        }
        this.mergeColumns(current, 0, result)
        if(current.stickyLeft) result[result.length - 1].stickyLeft = current.stickyLeft
        return result
      }, [])
    }
  },
  methods:{
    createColumn(column){
      let result = {label: column.label }
      if(column.width) result.width = column.width
      return result
    },
    createColumns(column, index){
      let result = {label: column.label[index]}
      if(column.label.length - 1 == index && column.width)
        result.width = column.width
      if(column.label.length - 1 < index + 1) return result
      result.child = [this.createColumns(column, index + 1)]
      return result
    },
    mergeColumns(column, index, result){
      if(result[result.length - 1].label != column.label[index]){
        result.push(this.createColumns(column, index))
        return
      }
      this.mergeColumns(column, index + 1, result[result.length - 1].child)
    },
  }
}
</script>

<style lang="stylus" scoped>
  .header
    display flex
    >>> .column
      display flex
      flex-direction column
      > div
        flex 1 0 auto
    >>> .row
      display flex
      > div
        flex 1 0 auto
</style>