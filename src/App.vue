<template>
  <div>
    <TheMenu
      @setDisplayStoreIndex="setDisplayStoreIndex"
    />
    <TheContent
      :storeIndex="storeIndex"
    />
  </div>
</template>

<script>
import TheContent from './components/TheContent.vue'
import TheMenu from './components/TheMenu.vue'
export default {
  name: 'App',
  components: {
    TheContent, TheMenu
  },
  data(){
    return{
      storeIndex: null,
    }
  },
  methods:{
    setDisplayStoreIndex(index){
      this.storeIndex = index
    }
  }
}
</script>

<style scoped>
  @import url('./styles/main.css')
</style>
