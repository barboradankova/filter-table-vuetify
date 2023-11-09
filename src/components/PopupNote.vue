<template>
    <v-dialog v-model="dialog" width="auto">
      <template v-slot:activator="{ props }">
        <v-btn density="compact" v-bind="props">
            <span class="material-symbols-outlined">
                notes
            </span>
        </v-btn>
      </template>

      <v-card>
        <v-card-text>
          {{ props_.note}}
        </v-card-text>
        <v-card-actions>
          <v-btn color="blue-darken-1" block @click="dialog = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
</template>

<script setup>
    import { ref } from 'vue'

    const props_ = defineProps(["note"])

    const dialog = ref(false)

    console.log(props_.note)
</script>