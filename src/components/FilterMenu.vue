<template>
    
    <v-menu
        v-model="menu"
        :close-on-content-click="false"
        location="bottom"
    >
        <template v-slot:activator="{ props }">
        <v-icon small v-bind="props" :class="{ active : isActive}">
            mdi-filter
        </v-icon>
        </template>

        <v-card width="250">
        <v-card-title class="text-start">Filter</v-card-title>
        <v-divider></v-divider>
        <v-select
            v-model="optionFilterName"
            :items="props.filterSelect"
            class="px-4 pt-4"
        ></v-select>
        

        <v-text-field
            v-if="!props.filterSelect.includes('yes')"
            v-model="filterValue"
            class="px-4 pt-1"
            type="text"
            label="Enter the search term"
            :autofocus="true"
        ></v-text-field>

        <v-card-actions class="ma-0">
            <v-spacer></v-spacer>

            <v-btn
            v-if="!props.filterSelect.includes('yes')"
            variant="text"
            @click="filterValue = ''"
            >
            Clean
            </v-btn>
            <v-btn
            v-if="props.filterSelect.includes('yes')"
            variant="text"
            @click="filterValue='', fillFilter()"
            >
            Dismiss
            </v-btn>
            <v-btn
            v-if="!props.filterSelect.includes('yes')"
            color="primary"
            variant="text"
            @click="fillFilter"
            >
            Apply
            </v-btn>
            <v-btn
            v-if="props.filterSelect.includes('yes')"
            color="primary"
            variant="text"
            @click="filterValue='x', fillFilter()"
            >
            Apply
            </v-btn>
        </v-card-actions>
        </v-card>
    </v-menu>
    
</template>

<script setup>
    import { ref } from 'vue';

    const props = defineProps(["filterSelect", "indexKey"]);

    const emits = defineEmits(["getFilterValues"])

    const menu =  ref(false);
    const optionFilterName = ref(props.filterSelect.includes('yes') ? 'yes' : 'contains');
    const filterValue = ref('');
    const isActive = ref(false)

    const fillFilter = (() => {
        filterValue.value ? isActive.value=true : isActive.value=false;
        emits("getFilterValues", {key: props.indexKey, filterOption: optionFilterName.value, filterVal: filterValue.value})
        menu.value = false;
    })

</script>

<style scoped>
    .active{
        color: #1E88E5;
    }
</style>