<template>
  <div class="prayer">
    <p v-for="line in newLines" :key="line.no" v-bind:class="line.markUp">{{line.text}}</p>
  </div>
</template>

<script>
import satBeforeLines from "./info/LDCsat-before.json";
import satAfterLines from "./info/LDCsat-after.json";
import sunBeforeLines from './info/LDCsun-before.json'
import sunAfterLines from './info/LDCsun-after.json'
import protectionLines from "./info/protection.json";
export default {
  name: "PrayerContent",
  props: {
    optionId: Number,
    season: String,
    day: Boolean
  },
  data() {
    return {
      //lines: prayerLines
      groupName: "Krist Kiran Parivar"
    };
  },
  computed: {
    newLines: function() {
      //gets the appropriate prayer as a array; one element for each paragraph

      if(this.day){

        if (this.optionId == 3) {
          var isAllowed = aLine =>
            !(
              aLine.markUp.charAt(2) == "V" &&
              aLine.markUp.substring(4) != this.season
            );
          satBeforeLines.forEach(aLine => {
            aLine.text = aLine.text.replace("NN", this.groupName);
          });
          return satBeforeLines.filter(isAllowed);
        }

        if (this.optionId == 4) {
          satAfterLines.forEach(aLine => {
            aLine.text = aLine.text.replace("NN", this.groupName);
          });
          return satAfterLines;
        }
      }

      if(!this.day) {
        if (this.optionId == 3) {
          return sunBeforeLines;
        }
        if (this.optionId == 4) {
          return sunAfterLines
        }
      }

      if (this.optionId == 6) {
        return protectionLines;
      }
      return satBeforeLines;
    }
  }
};
</script>

<style>
/* styles for displaying prayers*/

p{
  margin-bottom: 0px !important;
  padding: 4px 0 4px 0 !important;
}
.T {
  /* title */
  background-color: maroon;
  font-size: 32px;
  color:white;
  padding: 4px 4px 4px 4px !important;
  text-align: center;
}

.H {
  /* heading */
  color: maroon;
  font-size: 24px;
}

.I {
  /* instruction */
  color: grey;
  font-size: 15px;
}

.S {
  /* subheading */
  color: maroon;
  font-size: 22px;
}

.L {
  /* leader */
  color: mediumblue;
  font-size: 20px;
}

.A {
  /* assistant */
  color: olivedrab;
  font-size: 20px;
}

.G {
  /* group */
  font-size: 20px;
}

.O {
  /* optional */
  background-color: #ffeeee;
}

.R {
  /* optionally responsive */
  font-style: italic;
}
</style>