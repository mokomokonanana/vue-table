<template lang="pug">
  .vue-table-cell(:style="style")
    slot
      template(v-if="row && field") {{ row[field] }}
      template(v-else) {{ label }}
</template>

<script>
export default {
  props:{
    label: String,
    field: String,
    width: Number,
    row: Object
  },
  computed:{
    style: function(){
      const style = {}
      if(this.width) style.width = `${this.width}px`
      return style
    }
  },
  mounted(){
    console.warn('mounted')
  },
  updated(){
    console.warn('updated')
  },
  methods:{
  }
}
</script>

<style lang="stylus" scoped>
</style>
