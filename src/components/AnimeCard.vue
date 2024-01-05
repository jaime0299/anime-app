<template>
    <v-card class="mx-auto" max-width="250" width="210">
    <!-- <v-card class="mx-auto" max-width="250" width="210" :variant="variant"> -->
        <v-card-item>
            <v-img :src="thumbnail" height="150px">
            </v-img>
            <v-card-title @mouseover="isHovering = true" @mouseout="isHovering = false" :class="`${isHovering? 'text-wrap': ''}`">{{ title }}</v-card-title>
            <v-card-subtitle class="text-wrap">{{ progress }}</v-card-subtitle>
            <v-card-subtitle class="text-wrap">{{ lastAired }}</v-card-subtitle>
        </v-card-item>

        <v-card-actions>
            <!-- <v-btn :href="url" target="_blank" text>Watch</v-btn>
            <v-btn text>Mark</v-btn> -->
            <v-spacer></v-spacer>
            <v-btn size="small" color="success" variant="outlined" icon="mdi-check-circle" @click="animeCradMark"></v-btn>
            <!-- <v-btn size="small" color="info" variant="outlined" icon="mdi-share-variant"></v-btn> -->
            <v-btn size="medium" color="info" variant="text" icon="mdi-share-variant" :href="url"></v-btn>
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

const isHovering = ref(false)

function animeCradMark(title) {
    console.log(title);
}

</script>

<style scoped>
.anime-card {
    margin-bottom: 20px !important;
}

</style>