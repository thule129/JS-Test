<template>
    <div class="menu" ref="menu">
        <div class="menu-header">Milk Tea Store</div>
        <div class="menu-item" @click="itemOnClick(index)" v-for="(store, index) in stores" :key="index">{{store.name}}</div>
    </div>
</template>
<script>
import Stores from '../data/stores.json'
export default {
    Name: "TheMenu",
    data(){
        return{
            stores: []
        }
    },
    methods:{
        itemOnClick(index){
            var itemsList = this.$refs.menu.querySelectorAll('.menu-item')
            itemsList.forEach(item => {
                item.classList.remove('selected-item')
            });
           itemsList[index].classList.add('selected-item')
            this.$emit('setDisplayStoreIndex', index)
        }
    },
    created(){
        this.stores = Stores.stores
    },
    mounted(){
        this.itemOnClick(0)
    }
}
</script>
<style scoped>
    @import url('../styles/menu.css');
</style>