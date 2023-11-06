<template>
    <div class="mx-auto w-75">
    <v-select
        v-model="selectedColumns"
        :items="props.headers"
        label="Select columns"
        multiple
        chips
    >
    <template v-slot:chip="{ chip, index }">
        <v-chip 
            :key="index"
            class="pa-4 ma-1"
            color="blue-darken-1"
        >
        </v-chip>
    </template>

    <template v-slot:prepend-item>
        <v-list-item
            title="Select all"
            @click="selectAllCol"
        >
            <template v-slot:prepend>
                <v-checkbox-btn
                    :model-value="isSelectedAll"
                ></v-checkbox-btn>
            </template>
        </v-list-item>
        <v-divider class="mt-2"></v-divider>
    </template>
    </v-select>
    </div>
</template>

<script setup>
    import { ref, computed, watch, watchEffect } from 'vue';

    const props = defineProps(["headers"])

    const emits = defineEmits(["selectHeadersTable"])

    const selectedColumns = ref([])

    const allCols = ref(false)
    

    const selectAllCol = (() => {
        if (selectedColumns.value.length == props.headers.length){
            selectedColumns.value = []
            allCols.value=false
        } else{
            selectedColumns.value = props.headers.map((item) => {return item.title})
            allCols.value = true
        }
    })

    const isSelectedAll = computed(() => {
        return selectedColumns.value.length == props.headers.length
    })

    watch(selectedColumns, () => {
        emits("selectHeadersTable", selectedColumns)
    })

</script>
