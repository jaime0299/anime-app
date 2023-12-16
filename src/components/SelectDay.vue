<template>
    <!-- Input to write the username with a button to search the list for that user -->
    <v-container>
        <v-row>
            <v-col cols="12" md="6">
                <v-text-field v-model="username" label="Username" outlined class="text-field"></v-text-field>
            </v-col>
            <v-col cols="12" md="6">
                <v-btn @click="searchList" color="primary" class="text-field">Search</v-btn>
            </v-col>
            <v-col cols="12">
                <v-checkbox v-model="showBehindOnly" label="Show Behind Only" class="text-field"></v-checkbox>
            </v-col>
            <v-col cols="12">
                <v-select :items="days" item-title="name" item-value="id" label="Select Day" variant="solo"
                    class="text-field" v-model="selectedDay"></v-select>
            </v-col>
        </v-row>
    </v-container>
</template>

<script setup>
import { ref, watch } from 'vue';

const emit = defineEmits(['update:selectedDay', 'update:username', 'update:showBehindOnly'])

const date = new Date()
const selectedDay = ref(date.getDay())
const days = ref([
    { id: 0, name: 'Sunday' },
    { id: 1, name: 'Monday' },
    { id: 2, name: 'Tuesday' },
    { id: 3, name: 'Wednesday' },
    { id: 4, name: 'Thursday' },
    { id: 5, name: 'Friday' },
    { id: 6, name: 'Saturday' },
    { id: 7, name: 'Finished' },
    { id: 8, name: 'All' }
])

const username = ref('')

const showBehindOnly = ref(false)

watch(selectedDay, (newValue, oldValue) => {
    emit('update:selectedDay', newValue)
})

watch(showBehindOnly, (newValue, oldValue) => {
    emit('update:showBehindOnly', newValue)
})

function searchList() {
    const date = new Date()
    selectedDay.value = date.getDay()
    emit('update:username', username.value)
}
</script>