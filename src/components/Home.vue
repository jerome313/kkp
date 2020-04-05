<template>
  <v-app id="inspire">
    <v-navigation-drawer
      v-model="drawer"
      app
    >
      <v-list dense>

        <v-treeview
          :items="items"
          hoverable
          activatable
          :open-on-click="true"
        >
          <template v-slot:prepend="{ item }">
            <v-icon>
              {{ item.icon }}
            </v-icon>
          </template>
        </v-treeview>

      </v-list>
    </v-navigation-drawer>

    <v-app-bar
      app
      color="indigo"
      dark
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-toolbar-title>KKP Prayer Guide</v-toolbar-title>
    </v-app-bar>

    <v-content>
      <v-container
        class="fill-height"
        fluid
      >
        <PrayerCard/>
      </v-container>
    </v-content>
    <v-footer
      color="indigo"
      app
    >
      <span class="white--text">&copy; 2019</span>
    </v-footer>
  </v-app>
</template>

<script>
  import PrayerCard from "./PrayerCard"
  export default {
    name: "Home",
    components: {
      PrayerCard
    },
    props: {
      source: String,
    },
    data: () => ({
      drawer: null,
      items: [
        {
          id: 1,
          name: 'Prayers',
          children: [
            { id: 1, icon:'mdi-glass-wine', name: 'LDC' },
            { id: 2, icon:'mdi-chess-rook', name: 'Prayer for the year' },
            { id: 3, icon:'mdi-shield-cross-outline', name: 'Prayer for Protection' }
          ],
        }
      ],
    }),
  }
</script>