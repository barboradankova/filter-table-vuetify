<template>
    
    <v-menu
        v-model="menu"
        :close-on-content-click="false"
        location="bottom"
    >
        <template v-slot:activator="{ props }">
        <v-icon small v-bind="props">
            mdi-filter
        </v-icon>
        </template>

        <v-card width="250">
        <v-card-title class="text-start">Filter</v-card-title>
        <v-divider></v-divider>
        <v-select
            v-model="optionFilterName"
            :items="props.filterSelect"
            value="contains"
            class="px-4 pt-4"
        ></v-select>
        

        <v-text-field
            v-model="filterValue"
            class="px-4 pt-1"
            type="text"
            label="Enter the search term"
            :autofocus="true"
        ></v-text-field>

        <v-card-actions class="ma-0">
            <v-spacer></v-spacer>

            <v-btn
            variant="text"
            @click="filterValue = ''"
            >
            Clean
            </v-btn>
            <v-btn
            color="primary"
            variant="text"
            @click="fillFilter"
            >
            Apply
            </v-btn>
        </v-card-actions>
        </v-card>
    </v-menu>
    
</template>

<script setup>
    import { ref } from 'vue';

    const props = defineProps(["filterSelect", "index"]);

    const emits = defineEmits(["getFilterValues"])

    const menu =  ref(false);
    const optionFilterName = ref('contains');
    const filterValue = ref('');

    const fillFilter = (() => {
        emits("getFilterValues", {index: props.index, filterOption: optionFilterName.value, filterVal: filterValue.value})
        menu.value = false;
    })

    
</script>