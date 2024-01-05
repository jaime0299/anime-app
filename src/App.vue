<!-- <template>
    <v-app>
        <v-main>
            <v-container>
                <SelectDay @update:selectedDay="handleChangeDay" @update:username="handleChangeUsername"
                    @update:showBehindOnly="handleChangeShowBehindOnly" />
                <v-row>
                    <v-col v-for="anime in animeList" :key="anime.id">
                        <AnimeCard :title="anime.title" :thumbnail="anime.thumbnail" :progress="anime.progress"
                            :lastAired="anime.lastAired" :url="anime.url" />
                    </v-col>
                </v-row>
            </v-container>
        </v-main>
    </v-app>
</template> -->

<template>
    <v-app>
        <v-main>
            <v-navigation-drawer v-model="drawer" :rail="rail" permanent @click="rail = false">
                <v-list-item prepend-avatar="https://randomuser.me/api/portraits/men/85.jpg" title="John Leider" nav>
                    <template v-slot:append>
                        <v-btn variant="text" icon="mdi-chevron-left" @click.stop="rail = !rail"></v-btn>
                    </template>
                </v-list-item>

                <v-divider></v-divider>

                <v-list density="compact" nav>
                    <v-list-item prepend-icon="mdi-home-city" title="Home" value="home"></v-list-item>
                    <v-list-item prepend-icon="mdi-account" title="My Account" value="account"></v-list-item>
                    <v-list-item prepend-icon="mdi-account-group-outline" title="Users" value="users"></v-list-item>
                </v-list>
            </v-navigation-drawer>
            <!-- <v-main> -->
                <div class="d-flex justify-center align-center h-100">
                    <v-container class="mb-6">
                        <SelectDay @update:selectedDay="handleChangeDay" @update:username="handleChangeUsername"
                            @update:showBehindOnly="handleChangeShowBehindOnly" />
                        <v-row>
                            <v-col v-for="anime in animeList" :key="anime.id">
                                <AnimeCard :title="anime.title" :thumbnail="anime.thumbnail" :progress="anime.progress"
                                    :lastAired="anime.lastAired" :url="anime.url" />
                            </v-col>
                        </v-row>
                    </v-container>
                </div>
            <!-- </v-main> -->
        </v-main>
    </v-app>
</template>

<script setup>

// import AnimeSideBar from '@/components/AnimeSideBar.vue'
import AnimeCard from '@/components/AnimeCard.vue'
import SelectDay from '@/components/SelectDay.vue'

import { ref } from 'vue';

let id = 0

const animeList = ref([])
const originalAnimeList = ref([])
const username = ref('')
const showBehindOnly = ref(false)
const day = ref(0)

const drawer = ref(true);
const rail = ref(true);

function makeQuery() {
    var query = `
          query ($username: String) { 
              MediaListCollection (userName: $username, status: CURRENT, type: ANIME) { 
                  lists {
                      name
                      entries {
                          media {
                              title {
                                  romaji
                              }
                              season
                              status
                              coverImage {
                                  extraLarge
                                  large
                                  medium
                                  color
                              }
                              nextAiringEpisode {
                                  airingAt
                                  timeUntilAiring
                                  episode
                              }
                              episodes
                              externalLinks {
                                  url
                                  site
                              }
                              airingSchedule {
                                  nodes {
                                      episode
                                      timeUntilAiring
                                  }
                              }
                          }
                          progress
                      }
                  }
              }
          }
      `;

    // Define our query variables and values that will be used in the query request
    var variables = {
        username: username.value
    };

    // Define the config we'll need for our Api request
    var url = 'https://graphql.anilist.co',
        options = {
            //Authorization: 'Bearer ' + accessToken,
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Accept': 'application/json',
            },
            body: JSON.stringify({
                query: query,
                variables: variables
            })
        };

    // Make the HTTP Api request
    fetch(url, options).then(handleResponse)
        .then(handleData)
        .catch(handleError);
}

function handleResponse(response) {
    return response.json().then(function (json) {
        return response.ok ? json : Promise.reject(json);
    });
}

function handleData(data) {
    animeList.value = []
    let tempAnimeList = data.data.MediaListCollection.lists[0].entries
    originalAnimeList.value = tempAnimeList
    tempAnimeList.forEach((anime) => {
        let status = anime.media.status

        if (status === 'RELEASING') {

            let today = new Date().getDay()
            let startDate = anime.media.airingSchedule.nodes[0].timeUntilAiring * 1000
            startDate = startDate < 0 ? startDate * -1 : startDate
            let airingDay = new Date(Date.now() - startDate).getDay()


            day.value = today;
            if (today === airingDay) {
                handleFilterAnimeList(anime)
            }
        }

    })
    // console.log(tempAnimeList)
}

function handleFilterAnimeList(anime) {

    let progressInt = parseInt(anime.progress)
    let lastAiredInt = anime.media.nextAiringEpisode != null ? anime.media.nextAiringEpisode.episode - 1 : 0

    // console.log(showBehindOnly.value);
    // console.log(progressInt);
    // console.log(lastAiredInt);
    if (showBehindOnly.value && progressInt == lastAiredInt) {
        return
    }

    let progress = anime.media.episodes != null ? `Progress: ${anime.progress}/${anime.media.episodes}` : `${anime.progress}/?`
    let lastAired = anime.media.nextAiringEpisode != null ? anime.media.nextAiringEpisode.episode - 1 : 0
    lastAired = lastAired > 0 ? 'Last aired episode: ' + lastAired : 'Finished'
    let animeLink = anime.media.externalLinks.find((link) => link.site === 'Crunchyroll' || link.site === 'HIDIVE')
    animeLink = animeLink != null ? animeLink.url : ''
    let tempAnime = {
        id: id++,
        title: anime.media.title.romaji,
        thumbnail: anime.media.coverImage.large,
        progress: progress,
        lastAired: lastAired,
        url: animeLink
    }
    animeList.value.push(tempAnime)
}

function handleChangeDay(dayTemp) {
    day.value = dayTemp
    if (dayTemp === 7) {
        animeList.value = []
        originalAnimeList.value.forEach((anime) => {
            let status = anime.media.status

            if (status === 'FINISHED') {
                handleFilterAnimeList(anime)
            }
        })
    } else if (dayTemp === 8) {
        animeList.value = []
        originalAnimeList.value.forEach((anime) => {
            handleFilterAnimeList(anime)
        })
    } else {
        animeList.value = []
        originalAnimeList.value.forEach((anime) => {
            let status = anime.media.status

            if (status === 'RELEASING') {
                let startDate = anime.media.airingSchedule.nodes[0].timeUntilAiring * 1000
                startDate = startDate < 0 ? startDate * -1 : startDate
                let airingDay = new Date(Date.now() - startDate).getDay()

                if (dayTemp === airingDay) {
                    handleFilterAnimeList(anime)
                }
            }

        })
    }
}

function handleChangeUsername(usernameTemp) {
    username.value = usernameTemp
    makeQuery()
}

function handleChangeShowBehindOnly(showBehindOnlyTemp) {
    showBehindOnly.value = showBehindOnlyTemp
    handleChangeDay(day.value)
}

function handleError(error) {
    alert('Error, check console');
    console.log(error)
}
</script>