<template lang="pug">
  .vue-table
    VueTableHeader(:columns="columns")
    div(v-for="(row, r) in list" :class="{select: row.selected}" @click="rowClick(row, r)")
      template(v-for="(col, c) in columns")
        slot(:name="slotName(c, r)" :col="col" :r="r")
          slot(:name="slotName(c)" :col="col" :r="r" )
            VueTableCell(v-bind="col" :row="row")
</template>

<script>
import VueTableCell from '@/components/VueTableCell.vue'
import VueTableHeader from '@/components/VueTableHeader.vue'

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
  components:{ VueTableCell, VueTableHeader },
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
    }},
    headerRowCount:function(){
      return this.columns.reduce((result, current)=>{
        const type = Object.prototype.toString.call(current.label).slice(8, -1).toLowerCase()
        if(type == 'string'){
          if(result < 1) result = 1
        }
        if(type == 'array'){
          if(result < current.label.length) result = current.label.length
        }
        return result
      }, 0)
    },
  },
  mounted(){
    // 横サイズ調整
    this.columns.forEach((col, index)=>{
      // header
      const node = this.$el.querySelectorAll(`.vue-table > .header .col.last`)[index]
      if(!col.width || col.width > node.clientWidth) col.width =  node.clientWidth

      // body
      const nodes = this.$el.querySelectorAll(`.vue-table > div:nth-child(n + 2) > div:nth-child(${index + 1})`)
      nodes.forEach(node =>{
        if(col.width && col.width > node.clientWidth) return
        col.width =  node.clientWidth
      })
      this.$set(this.columns, index, col)
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
  border 1px solid black
  > div
    display flex
    &.select
      background-color #d5ead8
  >>> .col
    display flex
    align-items center
    justify-content center
    line-height 1.5rem
    padding 0.1rem 0.5rem
    border-color black
    border-style solid
    border-width 0px
  >>> > div:not(:last-child) .col
    border-bottom-width 1px
  >>> .col:not(:first-child),
  >>> > .header > :not(:first-child) .col
  >>> > .header > :first-child > .row > :not(:first-child) .col
    border-left-width 1px
</style>
