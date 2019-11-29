<template lang="pug">
#app(@click="checkEditMode")
  .wrapper
    header
      input(type="text" placeholder="Write selector")
      //- @paste="toggleEditMode" 
    textarea(       
      @keydown.esc="toggleEditMode"       
      v-model="html"
      :class="{show:editMode}"
      ref="area"
    ) {{html}}
    pre(v-if="!editMode" ref="code")
      code.language-html(@click="edit") {{html}}
        
        

</template>

<script>
import Vue from 'vue'
import PrismJS from 'prismjs'
import 'prismjs/themes/prism.css'
PrismJS.manual = true

export default {
  name: 'app',
  data: () => ({
    editMode: false,  
    html: ""  
  }),
  methods:{
    render(){
      this.highlightCode()
    },

    edit(){
    },

    toggleEditMode(){
      this.editMode = !this.editMode
      Vue.nextTick(()=>{
        if(this.editMode){this.edit()}
        else{this.render()}
      })
    },
    checkEditMode(e){
      if(e.target == this.$refs.area && this.editMode){ return }

      if(
        (e.target.closest('pre') == this.$refs.code && !this.editMode) || 
        (this.editMode && this.$refs.area)
      ){      
        this.toggleEditMode()
      }
    },
    highlightCode(){
      Vue.nextTick(()=>{
        PrismJS.highlightAll()
      })
    },
    async init(){
      const html = await fetch('/page.html')
      this.html = await html.text()
      this.highlightCode()
    }
  },

  created(){
    this.init()
  }
}
</script>

<style lang="sass" scoped>
body
  margin: 0
.wrapper
  display: flex
  flex-direction: column
  align-items: center
  min-height: 100vh
  padding: 2rem

  header
    position: sticky
    top: 0
    input
      border-radius: 0.25rem
      padding: 0.5rem
      margin: 0.5rem
      border: 1px solid rgba(0,0,0,0.5)
      font-size: 1rem

  .show
    display: block
  pre
    overflow: scroll
    min-width: 80%;
  textarea
    
    min-width: 80%;
    min-height: 100vh
    font-size: 1rem
    font-family: Consolas, Monaco, 'Andale Mono', 'Ubuntu Mono', monospace
    line-height: 1.5
    white-space: nowrap
    padding: 1rem
    background-color: rgba(0,0,0,0.01)
    margin: 0.5em
    overflow-x: scroll
    border: 0
    outline: none
    resize: none
    display: none
</style>
