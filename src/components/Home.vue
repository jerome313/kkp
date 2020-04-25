<template>
  <v-app id="inspire">
    <v-navigation-drawer
      v-model="drawer"
      dark
      app>
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
      dark>
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
        <v-row v-if="info!=null && (prayerId==0||prayerId==undefined)"> <!-- only show in the beginning -->
          <v-col vcols="12">
            <v-row>
              <v-col vcols="12">
                Today is 
                <span v-for="cel in info.celebrations" :key="cel.title">
                  <span v-if="cel.title.substring(0,5)=='Saint'">the feast of </span>
                  {{cel.title}}
                </span>
              </v-col>
            </v-row>
            <v-row justify="center">
              <v-col vcol="12" v-for="card in cardItems" :key="card.id">
                <v-card raised :id="card.id" @click="getPrayerBy">
                  <v-card-actions>
                      <v-btn :id="card.id" color="primary" fab x-large dark>
                        <v-icon>{{card.icon}}</v-icon>
                      </v-btn>
                      <v-card-text>{{card.name}}</v-card-text>
                  </v-card-actions>
                </v-card>
              </v-col>
            </v-row>
          </v-col>
        </v-row>

        <div v-if="info!=null && prayerId!=0"> <!-- Shows selected prayer -->
          <PrayerContent :key="dialog" :optionId="prayerId" :season="info.season" :day="sat" :group="groupName"/>
        </div>

      </v-container>
    </v-content>

    <v-footer
      v-bind:color="this.$vuetify.theme.themes.light.primary"
      app
    >
      <span class="white--text">&copy; 2020</span>
      <v-spacer></v-spacer>
      
      <v-btn v-show="(prayerId==3||prayerId==4)" text small @click.stop="dialog2 = true" class="white--text">Guide</v-btn>
      <v-btn v-show="(prayerId==3||prayerId==4)" text small @click.stop="dialog = true" class="white--text">Change grp. name</v-btn>
      
      <v-dialog v-model="dialog" max-width="300"> <!-- dialog -->
        <v-card>
          <v-card-title class="primary white--text">Enter your group name:</v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12">
                  <v-text-field v-model="groupName" class="primary--text" label="Group name" outlined></v-text-field>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
          <v-divider></v-divider>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn text @click="persist" color="primary">Change</v-btn>
          </v-card-actions>
          </v-card>
      </v-dialog>

      <v-dialog v-model="dialog2" max-width="300">
        <v-card>
          <v-card-title class="primary white--text">Guide:</v-card-title>
            <v-card-text>
              <p class="L">Blue text - Leader</p>
              <p class="A">Green text - Assistant</p>
              <p class="G">Black text - Group</p>
            </v-card-text>
            <v-divider></v-divider>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn text @click="dialog2 = false" color="primary">OK</v-btn>
            </v-card-actions>
        </v-card>
      </v-dialog>
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
     
      if (localStorage.groupName) {
        this.groupName = localStorage.groupName;  //to get a different community name for other community's users
      }
      else
        this.groupName = 'The Sword of the Spirit';

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
            this.$vuetify.theme.themes.light.primary = '#4A148C';
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
            this.$vuetify.theme.themes.light.primary = '#E2AB00';
            this.colourDark = true;
          }
          else if (this.info.celebrations[0].colour=="green"){
            this.$vuetify.theme.themes.light.primary = '#1B5E20';
            this.colourDark = true;
          }
          else {
            this.$vuetify.theme.themes.light.primary = '#3F51B5';
          }
          if(Date.parse(dateString)>=Date.parse("2020/01/18")&&Date.parse(dateString)<=Date.parse("2020/01/25"))
          {
            this.info.season='unity';
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
                          { id:4, icon:'mdi-baguette', name:'After Meal'}
                        ]
            },
            { id: 5, icon:'mdi-chess-rook', name: 'Prayer for the year' },
            { id: 6, icon:'mdi-shield-cross-outline', name: 'Prayer for Protection' }
          ],
        }
      ],
      info: null,
      prayerId: 0,
      sat: true, //variable that is true unless the day is Sunday. Saturday's prayer is the default prayer any day except for Sunday
      dialog: false,
      groupName: '',
      dialog2: false
    }),
    computed: {
      cardItems: function() {
        var prefix='';
        function getLeafNodes(nodes, result=[]) {
          
          for(var i=0; i<nodes.length;i++ ){
            if(nodes[i].children){
              prefix = prefix + nodes[i].name + ': ';
              result=getLeafNodes(nodes[i].children, result);
            }else {
              nodes[i].name = prefix + nodes[i].name;
              result.push(nodes[i]);
            }
          }
          prefix=prefix.substring(0, prefix.indexOf(' ')+1);
          return result;
        }
        return getLeafNodes(this.items);
      }
    },
    methods: { 
      getPrayer: function(array) { //gets the prayer id from the treeview
        console.log("prayer", array[0]);
        this.drawer=false; //hides the navigation drawer when a prayer is chosen
        if(this.info!=null) {
          this.sat = !(this.info.weekday=="sunday")
        }
        this.prayerId=array[0];
      },
      getPrayerBy: function(e) {
        console.log("prayerz", e);
        if(this.info!=null) {
          this.sat = !(this.info.weekday=="sunday")
        }
        this.prayerId=parseInt(e.currentTarget.id);
      },
      setSat() {
        this.sat=true;
      }, 
      setSun() {
        this.sat=false;
      },
      persist() {
        this.dialog = false;
        localStorage.groupName = this.groupName; //to set a different community name for other community's users
      }
    }
  }
</script>