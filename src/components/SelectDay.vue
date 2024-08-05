<template>
    <!-- Input to write the username with a button to search the list for that user -->
    <v-container>
        <v-row justify="center">
            <v-col cols="12" md="8">
                <v-container class="inputs-container">
                    <v-text-field density="compact" v-model="username" @click:append-inner="searchList"
                        @keydown.enter.prevent="searchList" append-inner-icon="mdi-magnify" label="Username"
                        variant="outlined" :loading="loadingSearch" class="outlined-text-field"
                        :rules="[() => !!username || 'This field is required']" required here></v-text-field>
                    <v-switch class="custom-switch" color="black" label="Show Behind Only" v-model="showBehindOnly"
                        :disabled="switchState" hide-details></v-switch>
                </v-container>
            </v-col>
        </v-row>
        <v-row justify="center">
            <v-col cols="12" md="8">
                <v-tabs v-model="selectedDay" center-active next-icon="mdi-arrow-right-bold-box-outline"
                    prev-icon="mdi-arrow-left-bold-box-outline" show-arrows align-tabs="center" class="custom-Tabs">
                    <v-tab v-for="day in days" :key="day" :value="day.id">
                        <!-- <v-icon class="card-anime-button-icon" icon="mdi-checkbox-marked-circle"></v-icon> -->
                        {{ day.name }}
                    </v-tab>
                </v-tabs>
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

// const username = ref('')
const username = ref('Jaime0299')

const showBehindOnly = ref(false)
const switchState = ref(true);

watch(selectedDay, (newValue, oldValue) => {
    console.log(newValue);
    emit('update:selectedDay', newValue)
})

watch(showBehindOnly, (newValue, oldValue) => {
    emit('update:showBehindOnly', newValue)
})

function searchList() {

    if (username.value != '') {
        loadingSearch.value = true;
        showBehindOnly.value = false;   
        setTimeout(() => {
            loadingSearch.value = false;
            switchState.value = false;
            const date = new Date()
            selectedDay.value = date.getDay()
            emit('update:username', username.value)
        }, 3000)     
    } else {
        switchState.value = true;
        showBehindOnly.value = false;
        emit('update:username', username.value)
    }
    
}
</script>

<style scoped>
.inputs-container {
    border-radius: 20px;
    /* gap: 5px; */
    flex-direction: column;
    justify-content: center;
    align-items: center;
    display: flex;
    border-radius: 15px;
    background: #311B92;
}

.outlined-text-field {
    border-radius: 10px;
    width: 400px;
    color: white;
    font-weight: bold;
}

.custom-switch {
    color: white;
}

.custom-Tabs {
    border-radius: 15px;
    background-color: #311B92;
    color: #ffffff;
}

.custom-Tabs .v-tabs-slider {
    background-color: #ffffff;
}

.custom-Tabs .v-tab {
    color: white;
    font-weight: bold;
}

/* .custom-Tabs .v-tab.v-tab--active {
  background-color: black;
} */
</style>