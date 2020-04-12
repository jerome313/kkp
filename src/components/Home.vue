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
          @update:active="getPrayer"
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
      v-bind:color="getColour()"
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
        <div v-if="info!=null && prayerId==0"><!-- Liturgical celebration. Only displayed in the beginnning -->
          <p class="display-1 text--primary" v-for="cel in info.celebrations" :key="cel.title">
            {{cel.title}}
          </p>
        </div>
        <div v-if="info!=null && prayerId!=0"> <!-- Shows selected prayer -->
          <PrayerContent :optionId="prayerId" :season="info.season"/>
        </div>
      </v-container>
    </v-content>
    <v-footer
      v-bind:color="getColour()"
      app
    >
      <span class="white--text">&copy; 2020</span>
    </v-footer>
  </v-app>
</template>

<script>
  import PrayerContent from "./PrayerContent"
  import axios from 'axios'; //to make rest calls to the liturgical calendar api
  export default {
    name: "Home",
    components: {
      PrayerContent
    },
    props: {
      source: String,
    },
    mounted () { //loading liturgical info for the day
      var d = new Date();
      var dateString = d.getFullYear()+'/'+(d.getMonth()+1)+'/'+d.getDate();
      var requestString = 'http://calapi.inadiutorium.cz/api/v0/en/calendars/default/' + dateString;
      axios
      .get(requestString)
      .then(response => (this.info = response.data))
    },
    data: () => ({
      drawer: null,
      items: [
        {
          id: 1,
          name: 'Prayers',
          children: [
            { id: 2, icon:'mdi-glass-wine', name: 'LDC',
              children: [
                          { id:3, icon:'mdi-candle', name:'Before Meal'},
                          { id:4, icon:'mdi-apple', name:'After Meal'}
                        ]
            },
            { id: 5, icon:'mdi-chess-rook', name: 'Prayer for the year' },
            { id: 6, icon:'mdi-shield-cross-outline', name: 'Prayer for Protection' }
          ],
        }
      ],
      info: null,
      prayerId: 0
    }),
    methods: { //gets liturgical colour to add into tags
      getColour()
      {
        if(this.info== null){
          return "indigo";
        }
        else {
          if(this.info.celebrations[0].colour=="violet")
          return "deep purple darken-4";
        else if (this.info.celebrations[0].colour=="rose")
          return "pink accent-1";
        else
          return this.info.celebrations[0].colour;
        }
      },
      getPrayer: function(array) { //gets the prayer id from the treeview
        console.log("prayer", array[0]);
        this.prayerId=array[0];
      }
    }
  }
</script>