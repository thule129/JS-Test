<template lang="">
    <div class="checkbox" @click="checkboxOnClick">
        <div class="checkbox-inner"></div>
    </div>
</template>
<script>
export default {
    name: 'MCheckbox',
    methods: {
        checkboxOnClick(e){
            var isCheckbox = e.target.classList.contains('checkbox')
            if(isCheckbox){
                e.target.classList.toggle('checked')
                this.$emit('setCheckbox', e.target)
            }else{
                e.target.parentNode.classList.remove('checked')
                this.$emit('setCheckbox', e.target.parentNode)
            }
        }
    },
}
</script>
<style lang="">
    
</style>