<template>
    <v-app>
        <v-main>
            <v-container>
                <SelectDay @update:selectedDay="handleChangeDay" @update:username="makeQuery" />
                <v-row>
                    <v-col v-for="anime in animeList" :key="anime.id">
                        <AnimeCard :title="anime.title" :thumbnail="anime.thumbnail" :progress="anime.progress"
                            :lastAired="anime.lastAired" :url="anime.url" />
                    </v-col>
                </v-row>
            </v-container>
        </v-main>
    </v-app>
</template>

<script setup>
import AnimeCard from '@/components/AnimeCard.vue'
import SelectDay from '@/components/SelectDay.vue'
import { ref } from 'vue';

let id = 0

const animeList = ref([])
const originalAnimeList = ref([])

function makeQuery(username) {
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
        username: username
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

            console.log(today)
            console.log(startDate)
            console.log(airingDay)

            if (today === airingDay) {
                handleFilterAnimeList(anime)
            }
        }

    })
    console.log(tempAnimeList)
}

function handleFilterAnimeList(anime) {
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

function handleChangeDay(day) {
    if (day === 7) {
        animeList.value = []
        originalAnimeList.value.forEach((anime) => {
            let status = anime.media.status

            if (status === 'FINISHED') {
                handleFilterAnimeList(anime)
            }
        })
    } else if (day === 8) {
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

                if (day === airingDay) {
                    handleFilterAnimeList(anime)
                }
            }

        })
    }
}

function handleError(error) {
    alert('Error, check console');
    console.log(error)
}
</script>
