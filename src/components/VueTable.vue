<template lang="pug">
  .vue-table
    div
      VueTableCell(v-for="(col, index) in columns" :key="index" v-bind="col")
    div(v-for="(row, r) in list" :class="{select: row.selected}" @click="rowClick(row, r)")
      template(v-for="(col, c) in columns")
        slot(:name="slotName(c, r)" :col="col" :r="r")
          slot(:name="slotName(c)" :col="col" :r="r" )
            VueTableCell(v-bind="col" :row="row")
</template>

<script>
import VueTableCell from '@/components/VueTableCell.vue'

export const VueTableSelectMode = {
  none : 0,
  single : 1,
  multi : 2
}
export function VueTableSlotName(c, r){
  let name = `body-`  
  if(r != undefined) name += `r${r}`
  if(c != undefined) name += `c${c}`
  return name
}
export default {
  components:{ VueTableCell },
  props:{
    columns: Array,
    list: Array,
    selectMode: {
      type: Number,
      default: VueTableSelectMode.none,
      validator: function (value) {
        return Object.values(VueTableSelectMode).indexOf(value) !== -1
      }
    }
  },
  computed:{
    // slot名
    slotName: function(){ return (c, r) =>{
      let name = `body-`  
      if(r != undefined) name += `r${r}`
      if(c != undefined) name += `c${c}`
      return name
    }},
    // スタイル調整
    colStyle: function(){return (col)=>{
      const style = {}
      if(col.width) style.width = `${col.width}px`
      return style
    }}
  },
  mounted(){
    // 横サイズ調整
    this.columns.forEach((col, index)=>{
      const nodes = this.$el.querySelectorAll(`.vue-table > div > div:nth-child(${index + 1})`)
      nodes.forEach(node =>{
        if(col.width && col.width > node.clientWidth) return
        col.width =  node.clientWidth
      })
    })
    // 定義に変更があったらにしたい
    this.$forceUpdate()
  },
  methods:{
    // 行選択
    rowClick(row, r){
      // 非選択モード
      if(this.selectMode == VueTableSelectMode.none) return

      // 選択解除
      if(row.selected == true){
        delete row.selected
        this.$set(this.list, r, row)
        return
      }

      // 1行選択モード
      if(this.selectMode == VueTableSelectMode.single){
        this.list.forEach((item, index) => {
          if(item.selected != true) return
          delete item.selected
          this.$set(this.list, index, item)
        })
      }

      // 行選択
      row.selected = true
      this.$set(this.list, r, row)
    }
  }
}
</script>

<style lang="stylus" scoped>
.vue-table
  display inline-block
  overflow auto
  border 1px solid red
  > div
    display flex
    &.select
      background-color #d5ead8
    &:not(:last-child)
      border-bottom 1px solid black
    > div
      // border 1px solid black
      // margin-top -1px
      // margin-left -1px
      display flex
      justify-content center
      align-items center
      flex-shrink 0
      padding 0.2rem
      &:not(:last-child)
        border-right 1px solid black
</style>
