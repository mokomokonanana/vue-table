<template lang="pug">
  div(:class="{col: !child, column: child, last: !child }" :style="colStyle")
    template(v-if="!child" ) {{ label }}
    template(v-else)
      div(class="col") {{ label }}
      .row
        VueTableHeaderCol(v-for="(col, index) in child" :key="index" v-bind="col")
      //- VueTableHeaderCol(v-if="child.length == 1" v-bind="child[0]")
      //- .row(v-else)
      //-   VueTableHeaderCol(v-for="(col, index) in child" :key="index" v-bind="col")
</template>

<script>
import VueTableHeaderCol from '@/components/VueTableHeaderCol.vue'

export default {
  name: 'VueTableHeaderCol',
  components:{ VueTableHeaderCol },
  props:{
    label: String,
    child: Array,
    width: Number
  },
  computed:{
    // スタイル調整
    colStyle: function(){
      const style = {}
      if(this.width) style.width = `${this.width}px`
      return style
    },
  }
}
</script>

// <style lang="stylus" scoped>
//   .header
//     // background-color red
//     border-right 1px solid black
//   .col
//     display flex
//     flex-direction column
//     > div
//       flex-grow 1
//   .row
//     display flex
//   // .row
//   //   display flex
//   //   flex-direction column
// </style>