<template>
    <div class="d-flex justify-space-between">
        <ColumnSelector
            :headers="headers"
            @select-headers-table="selectHeaders"
       ></ColumnSelector>
       <div class="w-25">
            <v-text-field
                append-icon="mdi-magnify" 
                label="Search..."
                @input="event => sendSearch(event.target.value)"
                
            ></v-text-field>
       </div>
    </div>
    <v-divider></v-divider>
</template>

<script setup>
    import { ref } from 'vue'

    import ColumnSelector from './ColumnSelector.vue'

    const props = defineProps(["headers"])

    const emits = defineEmits(["selectHeadersTable", "setSearchTable"])
    
    const sendSearch = ((val) => {
        emits("setSearchTable", val)
    })

    const selectHeaders = ((lst) => {
        emits("selectHeadersTable", lst)
    })
</script>

<style scoped>
    .mw{
        max-width: 25%;
    }
</style>