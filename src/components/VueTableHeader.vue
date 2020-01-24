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
    columns: Array
  },
  computed:{
    header: function(){
      return this.columns.reduce((result, current, index, list) => {
        // 単段ヘッダ追加
        if(!Array.isArray(current.label)){
          result.push(this.createColumn(current))
          return result
        }

        // 多段ヘッダ追加
        //  一件目 || 前列が単段
        if(result.length < 1 || !result[result.length - 1].child){
          result.push(this.createColumns(current, 0))
          return result
        }
        this.mergeColumns(current, 0, result)
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
    // > div
    //   flex 1 0 auto
    // > div:not(:first-child)
    //   border-left 1px solid black
    >>> .column
      display flex
      flex-direction column
      > div
        flex 1 0 auto
        // &:not(:first-child)
        //   border-top 1px solid black
    >>> .row
      display flex
      > div
        flex 1 0 auto
      // > div:not(:first-child)
      //   border-left 1px solid black

    > div:not(.column):not(.row)
    >>> .column > div:not(.column):not(.row)
    >>> .row > div:not(.column):not(.row)
      // border-bottom 1px solid black 
      // border-right 1px solid black

      // background-color red
      display flex
      align-items center
      justify-content center
      // padding 0.2rem 0.5rem
</style>