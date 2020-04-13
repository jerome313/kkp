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
@import '../assets/styles/prayerStyles.css';
</style>