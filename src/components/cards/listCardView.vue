<script setup>
    import {defineProps} from 'vue';
   


    const props = defineProps({
        cards: {
            type: Array,
            required: true
        }
    });


</script>
<template>
    <div>
        <h1>Lista de tarjetas</h1>
        {{ props.cards }}
    </div>  
</template>
<style scoped>
</style>