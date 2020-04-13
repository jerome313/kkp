<template>
  <v-app id="inspire">
    <v-navigation-drawer
      v-model="drawer"
      :dark ="colourDark"
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
      color="primary"
      :dark ="!colourDark"
    ><!--for when the liturgical color is white -->
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"/>
      <v-toolbar-title>Prayer Guide</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn v-show="(prayerId==3||prayerId==4)" text v-if="sat" @click="setSun()">SAT.</v-btn>
      <v-btn v-show="(prayerId==3||prayerId==4)" text v-else @click="setSat()">SUN.</v-btn>
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
          <PrayerContent :optionId="prayerId" :season="info.season" :day="sat"/>
        </div>
      </v-container>
    </v-content>
    <v-footer
      v-bind:color="this.$vuetify.theme.themes.light.primary"
      app
    >
      <span v-if="!colourDark" class="white--text">&copy; 2020</span>
      <span v-else >&copy; 2020</span>
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
    mounted () { 
      //loading liturgical info for the day
      var d = new Date();
      var dateString = d.getFullYear()+'/'+(d.getMonth()+1)+'/'+d.getDate();
      var requestString = 'http://calapi.inadiutorium.cz/api/v0/en/calendars/default/' + dateString;
      axios
      .get(requestString)
      .then(response => (this.info = response.data))
      .catch(error => {
        console.log(error);
        this.errored = true;
      })
      .finally(() =>{
          if(this.info.celebrations[0].colour=="violet") {
            this.$vuetify.theme.themes.light.primary = '#5E35B1';
            this.colourDark = false;
          }
          else if (this.info.celebrations[0].colour=="red"){
            this.$vuetify.theme.themes.light.primary = '#B71C1C';
            this.colourDark = false;
          }
          else if (this.info.celebrations[0].colour=="rose"){
            this.$vuetify.theme.themes.light.primary = '#FF80AB';
            this.colourDark = false;
          }
          else if (this.info.celebrations[0].colour=="white"){
            this.$vuetify.theme.themes.light.primary = '#FFFFFF';
            this.colourDark = true;
          }
          else {
            this.$vuetify.theme.themes.light.primary = '#3F51B5';
          }
      });
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
      prayerId: 0,
      sat: true, //variable that is true unless the day a Sunday. Saturday's prayer is the default prayer any day except for Sunday
      colourDark: false
    }),
    methods: { 
      getPrayer: function(array) { //gets the prayer id from the treeview
        console.log("prayer", array[0]);
        this.drawer=false; //hides the navigation drawer when a prayer is chosen
        if(this.info!=null) {
          this.sat = !(this.info.weekday=="sunday")
        }
        this.prayerId=array[0];
      },
      setSat() {
        this.sat=true;
      }, 
      setSun() {
        this.sat=false;
      }
    }
  }
</script>