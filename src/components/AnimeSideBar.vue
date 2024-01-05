<template>
    <v-card>
    <v-layout>
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
        <v-main style="height: 250px">
            <div class="d-flex justify-center align-center h-100">
                <v-btn
            color="primary"
            @click.stop="drawer = !drawer"
          >
            Toggle
          </v-btn>
                <v-container class="bg-surface-variant mb-6">
                    <v-row align="start" no-gutters style="height: 150px;">
                        <v-col v-for="n in 3" :key="n">
                            <v-sheet class="pa-2 ma-2">
                                .align-start
                            </v-sheet>
                        </v-col>
                    </v-row>
                </v-container>
            </div>
        </v-main>
    </v-layout>
    </v-card>
</template>

<script setup>
import { ref } from 'vue';

const drawer = ref(true);
const rail = ref(true);

</script>