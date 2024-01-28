<template>
    <!-- Input to write the username with a button to search the list for that user -->
    <v-container>
        <v-row>
            <v-spacer></v-spacer>
            <v-col cols="6" md="4">
                <v-text-field :loading="loadingSearch" density="compact" variant="solo" v-model="username" label="Username"
                    append-inner-icon="mdi-magnify" single-line hide-details @click:append-inner="searchList"
                    @keydown.enter.prevent="searchList" required here placeholder="Username"
                    class="text-field"></v-text-field>
            </v-col>
            <v-col cols="6" md="3">
                <v-switch color="blue" label="Show Behind Only" v-model="showBehindOnly"></v-switch>
            </v-col>
        </v-row>
        <v-row>
            <v-col cols="12">
                <v-card>
                    <v-tabs v-model="selectedDay" bg-color="deep-purple-darken-4" center-active
                        next-icon="mdi-arrow-right-bold-box-outline" prev-icon="mdi-arrow-left-bold-box-outline"
                        show-arrows align-tabs="center">
                        <v-tab v-for="day in days" :key="day" :value="day.id">
                            {{ day.name }}
                        </v-tab>
                    </v-tabs>
                </v-card>
            </v-col>
        </v-row>
    </v-container>
</template>

<script setup>

import { ref, watch } from 'vue';

const emit = defineEmits(['update:selectedDay', 'update:username', 'update:showBehindOnly'])

const date = new Date()
const selectedDay = ref(date.getDay())
const loadingSearch = ref(false);
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
// const username = ref('Jaime0299')

const showBehindOnly = ref(false)

watch(selectedDay, (newValue, oldValue) => {
    console.log(newValue);
    emit('update:selectedDay', newValue)
})

watch(showBehindOnly, (newValue, oldValue) => {
    emit('update:showBehindOnly', newValue)
})

function searchList() {
    loadingSearch.value = true;
    setTimeout(() => {
        loadingSearch.value = false;
        const date = new Date()
        selectedDay.value = date.getDay()
        emit('update:username', username.value)
    }, 2000)
}
</script>