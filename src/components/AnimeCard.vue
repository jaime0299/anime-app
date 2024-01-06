<template>
    <!-- <v-card class="mx-auto" max-width="250" width="210">
        <v-card class="mx-auto" max-width="250" width="210" :variant="variant"> 
        <v-card-item>
            <v-img :src="thumbnail" height="150px"></v-img>
            <v-card-title @mouseover="isHovering = true" @mouseout="isHovering = false"
                :class="`${isHovering ? 'text-wrap' : ''}`">{{ title }}</v-card-title>
            <v-card-subtitle class="text-wrap">{{ progress }}</v-card-subtitle>
            <v-card-subtitle class="text-wrap">{{ lastAired }}</v-card-subtitle>
        </v-card-item>

        <v-card-actions class="anime-card">
            <v-spacer></v-spacer>
            <v-btn size="small" class="custom-icon-button button-mark-anime" variant="outlined">
                <v-icon class="custom-icon" icon="mdi-checkbox-marked-circle"></v-icon>
            </v-btn>
            <v-btn size="small" class="custom-icon-button button-play-anime" variant="outlined">
                <v-icon class="custom-icon" icon="mdi mdi-play-circle"></v-icon>
            </v-btn>
            <v-btn size="x-large" color="success" variant="outlined" icon="mdi mdi-check-circle" @click="animeCradMark"></v-btn>
            <v-btn size="x-large" color="info" variant="text" icon="mdi-share-variant" :href="url"></v-btn> 
        </v-card-actions>
    </v-card> -->
    <v-card class="card-anime">
        <v-card-item class="card-anime-content">
            <v-img :src="thumbnail" max-width="200" height="200"></v-img>
            <v-card-title class="card-anime-title">{{ title }}</v-card-title>
            <v-card-subtitle class="card-anime-data">{{ progress }}</v-card-subtitle>
            <v-card-subtitle class="card-anime-data">{{ lastAired }}</v-card-subtitle>
        </v-card-item>
        <v-card-actions class="card-anime-buttons">
            <v-btn size="small" class="card-anime-button button-mark" variant="outlined">
            <!-- <v-btn size="small" class="card-anime-button button-mark" variant="outlined" @click="animeCradMark"> -->
                <v-icon class="card-anime-button-icon" icon="mdi-checkbox-marked-circle"></v-icon>
            </v-btn>
            <v-btn size="small" class="card-anime-button button-play" variant="outlined">
            <!-- <v-btn size="small" class="card-anime-button button-play" variant="outlined" :href="url"> -->
                <v-icon class="card-anime-button-icon" icon="mdi mdi-play-circle"></v-icon>
            </v-btn>
        </v-card-actions>
    </v-card>
</template>

<script setup>
import { defineProps, ref } from 'vue'

const props = defineProps({
    title: String,
    progress: String,
    thumbnail: String,
    lastAired: String,
    url: String
})

// const isHovering = ref(false)

function animeCradMark(title) {
    console.log(title);
}

</script>

<style scoped>
.card-anime {
    width: 210px;
    /* height: 450px; */
    border: 1px solid #ccc;
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

/* .card-anime img {
    width: 100%;
    height: auto;
} */

.card-anime-content {
    padding: 20px;
}

.card-anime-title {
    font-size: 1.0rem;
    margin-bottom: 10px;
}

.card-anime-data {
    font-size: 1rem;
    color: #555;
    margin-bottom: 5px;
}

.card-anime-buttons {
    display: flex;
    justify-content: space-between;
    /* margin-top: 15px; */
    box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
    transition: background-color 0.3s ease;
}

.card-anime-button {
    flex: 1;
    margin-right: 5px;
}

.card-anime-button:last-child {
    margin-right: 0;
}

.button-mark {
    background-color: #16d646;
    color: #ffffff;
}

.button-mark:hover {
    background-color: #ffffff;
    color: #00ff6a;
}

.button-play {
    background-color: #1689d6;
    color: #ffffff;
}

.button-play:hover {
    background-color: #ffffff;
    color: #1689d6;
}

.card-anime-button-icon {
    font-size: 24px;
}
</style>