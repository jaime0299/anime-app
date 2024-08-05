<template>
    <v-card class="card-v-anime">
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
                    <v-tooltip activator="parent" location="bottom">MARK</v-tooltip>
                    <v-icon class="card-anime-button-icon" icon="mdi-checkbox-marked-circle"></v-icon>
                </v-btn>
                <v-btn size="small" class="card-anime-button button-play" variant="outlined" target="_blank">
                    <!-- <v-btn size="small" class="card-anime-button button-play" variant="outlined" :href="url" target="_blank"> -->
                    <v-icon class="card-anime-button-icon" icon="mdi mdi-play-circle"></v-icon>
                    <v-tooltip activator="parent" location="bottom">WATCH</v-tooltip>
                </v-btn>
                <v-btn size="small" class="card-anime-button button-donwload" variant="outlined" :href="download"
                    target="_blank">
                    <v-icon class="card-anime-button-icon" icon="mdi mdi-download-circle"></v-icon>
                    <v-tooltip activator="parent" location="bottom">DOWNLOAD</v-tooltip>
                </v-btn>
            </v-card-actions>
        </v-card>
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

const download = ref('');
let tempTitle = props.title.replace(/ /g, '+');
let downloadLink = 'https://nyaa.si/?f=0&c=0_0&q=' + tempTitle;
download.value = downloadLink;

// const isHovering = ref(false)

function animeCradMark(title) {
    console.log(title);
}

</script>

<style scoped>
.card-v-anime {
    width: 210px;
    /* background-image: linear-gradient(163deg, #00ff75 0%, #3700ff 100%); */
    background-image: linear-gradient(163deg, #ffae00 0%, #fbff00 100%);
    border-radius: 20px;
    transition: all .3s;
}

.card-anime {
    background-color: #b7b7c7;
    width: 210px;
    overflow: hidden;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    transition: all .2s;
}

.card-anime:hover {
    transform: scale(0.98);
    border-radius: 20px;
}

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
    color: #16d646;
}

.button-donwload {
    background-color: #ff9100;
    color: #ffffff;
}

.button-donwload:hover {
    background-color: #ffffff;
    color: #ff9100;
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