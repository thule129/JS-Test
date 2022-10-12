<template>
    <button v-bind:class="['button', color]">
        {{text}}
    </button>
</template>
<script>
export default {
    name: 'MButton',
    props: {
        text: String,
        color: String,
    }
}
</script>
<style>
</style>