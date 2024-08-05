<template>
  <v-app>
    <v-main>
      <v-navigation-drawer expand-on-hover rail>
        <v-list>
          <v-list-item prepend-avatar="./assets/Logo_Official_AA.png"
            :title="username == '' ? 'ANIME APP' : username.toUpperCase()"></v-list-item>
        </v-list>

        <v-divider></v-divider>

        <v-list density="compact" nav>
          <v-list-item prepend-icon="mdi-home-city" title="HOME" value="home"></v-list-item>
          <v-list-item prepend-icon="mdi-account-group-outline" title="USERS" value="users"></v-list-item>
          <v-list-item prepend-icon="mdi-account" title="MY ACCOUNT" value="account"></v-list-item>
        </v-list>
      </v-navigation-drawer>
      <SelectDay @update:selectedDay="handleChangeDay" @update:username="handleChangeUsername"
        @update:showBehindOnly="handleChangeShowBehindOnly" />
      <v-row class="myRowCustom">
        <div class="card-container">
          <v-col v-for="anime in animeList" :key="anime.id">
            <AnimeCard :title="anime.title" :thumbnail="anime.thumbnail" :progress="anime.progress"
              :lastAired="anime.lastAired" :url="anime.url" />
          </v-col>
        </div>
      </v-row>
    </v-main>
  </v-app>
</template>

<script setup>

import AnimeCard from "@/components/AnimeCard.vue";
import SelectDay from "@/components/SelectDay.vue";

import { ref } from "vue";

let id = 0;

const animeList = ref([]);
const originalAnimeList = ref([]);
const username = ref("");
const showBehindOnly = ref(false);
const day = ref(0);

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
    username: username.value,
  };

  
  if (username.value != '') {
    // Define the config we'll need for our Api request
  var url = "https://graphql.anilist.co",
    options = {
      //Authorization: 'Bearer ' + accessToken,
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        Accept: "application/json",
      },
      body: JSON.stringify({
        query: query,
        variables: variables,
      }),
    };

  // Make the HTTP Api request
  fetch(url, options).then(handleResponse).then(handleData).catch(handleError);
  } else {
    animeList.value = [];
    originalAnimeList.value = [];
  }
}

function handleResponse(response) {
  return response.json().then(function (json) {
    return response.ok ? json : Promise.reject(json);
  });
}

function handleData(data) {
  animeList.value = [];
  let tempAnimeList = data.data.MediaListCollection.lists[0].entries;
  originalAnimeList.value = tempAnimeList;
  tempAnimeList.forEach((anime) => {
    let status = anime.media.status;

    if (status === "RELEASING") {
      let today = new Date().getDay();
      let startDate =
        anime.media.airingSchedule.nodes[0].timeUntilAiring * 1000;
      startDate = startDate < 0 ? startDate * -1 : startDate;
      let airingDay = new Date(Date.now() - startDate).getDay();

      day.value = today;
      if (today === airingDay) {
        handleFilterAnimeList(anime);
      }
    }
  });
  // console.log(tempAnimeList)
}

function handleFilterAnimeList(anime) {
  let progressInt = parseInt(anime.progress);
  let lastAiredInt =
    anime.media.nextAiringEpisode != null
      ? anime.media.nextAiringEpisode.episode - 1
      : 0;

  // console.log(showBehindOnly.value);
  // console.log(progressInt);
  // console.log(lastAiredInt);
  if (showBehindOnly.value && progressInt == lastAiredInt) {
    return;
  }

  let progress =
    anime.media.episodes != null
      ? `Progress: ${anime.progress}/${anime.media.episodes}`
      : `${anime.progress}/?`;
  let lastAired =
    anime.media.nextAiringEpisode != null
      ? anime.media.nextAiringEpisode.episode - 1
      : 0;
  lastAired = lastAired > 0 ? "Last aired episode: " + lastAired : "Finished";
  let animeLink = anime.media.externalLinks.find(
    (link) => link.site === "Crunchyroll" || link.site === "HIDIVE"
  );
  animeLink = animeLink != null ? animeLink.url : "";
  let tempAnime = {
    id: id++,
    title: anime.media.title.romaji,
    thumbnail: anime.media.coverImage.large,
    progress: progress,
    lastAired: lastAired,
    url: animeLink,
  };
  animeList.value.push(tempAnime);
}

function handleChangeDay(dayTemp) {
  day.value = dayTemp;
  // console.log(day.value);
  if (dayTemp === 7) {
    animeList.value = [];
    originalAnimeList.value.forEach((anime) => {
      let status = anime.media.status;

      if (status === "FINISHED") {
        handleFilterAnimeList(anime);
      }
    });
  } else if (dayTemp === 8) {
    animeList.value = [];
    originalAnimeList.value.forEach((anime) => {
      handleFilterAnimeList(anime);
    });
  } else {
    animeList.value = [];
    originalAnimeList.value.forEach((anime) => {
      let status = anime.media.status;

      if (status === "RELEASING") {
        let startDate =
          anime.media.airingSchedule.nodes[0].timeUntilAiring * 1000;
        startDate = startDate < 0 ? startDate * -1 : startDate;
        let airingDay = new Date(Date.now() - startDate).getDay();

        if (dayTemp === airingDay) {
          handleFilterAnimeList(anime);
        }
      }
    });
  }
}

function handleChangeUsername(usernameTemp) {
  username.value = usernameTemp;
  makeQuery();
}

function handleChangeShowBehindOnly(showBehindOnlyTemp) {
  showBehindOnly.value = showBehindOnlyTemp;
  handleChangeDay(day.value);
}

function handleError(error) {
  alert("Error, check console");
  console.log(error);
}
</script>

<style scoped>
.card-container {
  display: flex;
  justify-content: space-around;
  align-items: flex-start;
  flex-wrap: wrap;
  gap: 15px;
  /* Establecer el espacio entre las tarjetas */
  padding: 20px;
}

.myRowCustom {
  display: flex;
  justify-content: center;
  /* Centra el contenido horizontalmente */
  align-items: center;
  /* Centra el contenido verticalmente */
}

.v-navigation-drawer {
  background-color: #222;
  /* Cambia el color de fondo a tu preferencia */
  color: #fff;
  /* Cambia el color del texto a tu preferencia */
}

.v-navigation-drawer v-list-item {
  padding: 12px 24px;
  /* Ajusta el espacio alrededor de cada elemento de la lista */
  color: #fff;
  /* Cambia el color del texto a tu preferencia */
}

.v-main {
  background: url('./assets/wallpaper_v1.png') center/cover no-repeat fixed;
}
</style>
