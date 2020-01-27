<template lang="pug">
  .vue-table(@scroll="scroll" @wheel="wheel")
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
    },
    // IEの場合ヘッダ固定スクロールがガタつくのを防止
    wheel(event){
      if(this.isIE()) return
      event.preventDefault()
      this.$el.scrollTop += event.deltaY
    },
    scroll(){
      this.stickyTop()
    },
    // ヘッダ固定
    stickyTop(){
      if(this.isIE()) return
      this.$el.firstChild.style.position = 'relative'
      this.$el.firstChild.style.top = `${this.$el.scrollTop}px`
    },
    // IE判定
    isIE(){
      var ua = window.navigator.userAgent.toLowerCase()
      if (ua.indexOf("msie") != -1) return false
      if (ua.indexOf('trident/7') != -1) return false
      return true
    }
  }
}
</script>

<style lang="stylus" scoped>
.vue-table
  display inline-block
  overflow auto
  border 1px solid black
  >>> > .header
    position sticky
    top 0
  > div
    display flex
    // 行選択色
    &.select
      background-color #d5ead8
  // カラム（headerもbodyも共通の設定）
  >>> .col
    display flex
    align-items center
    justify-content center
    flex 1 0 auto
    line-height 1.5rem
    padding 0.1rem 0.5rem
    border-color black
    border-style solid
    border-width 1px
    border-right 0px
    border-top 0px
  // header背景色
  >>> .header .col
    background-color lightgrey
  // // カラムの区切り線
  >>> > div:last-child .col
    border-bottom-width 0px
  >>> > div > .col:first-child
  >>> > .header > :not(:nth-child(n + 2)) > .col
  >>> > .header > :not(:nth-child(n + 2)) > .row > .col:first-child
  >>> > .header > :not(:nth-child(n + 2)) > .row :not(:nth-child(n + 2)) .col:first-child
    border-left-width 0px
</style>
