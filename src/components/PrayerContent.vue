<template>
  <div class="prayer">
    <p v-for="line in newLines" :key="line.no" v-bind:class="line.markUp">{{line.text}}</p>
  </div>
</template>

<script>
import satBeforeLines from './info/LDCsat-before.json'
import satAfterLines from './info/LDCsat-after.json'
//import sunBeforeLines from './info/LDCsun-before.json'
//import sunAfterLines from './info/LDCsun-after.json'
import protectionLines from './info/protection.json'
export default {
  name: "PrayerContent",
  props: {
    optionId: Number,
    season:String
  },
  data() {
    return {
      //lines: prayerLines
    }
  }, 
  computed: {
    newLines: function() { //gets the appropriate prayer as a array; one element for each paragraph

      //const prayerLines = () => import('./info/LDCsat-before.json').then(({default: myData}) => myData);
      //console.log(prayerLines);
      if(this.optionId==3) {
        var isAllowed =(aLine)=> !(aLine.markUp.charAt(2)=='V' && aLine.markUp.substring(4)!=this.season );
        return satBeforeLines.filter(isAllowed);
      }
      if(this.optionId==4) {
        return satAfterLines;
      }
      if(this.optionId==6) {
        return protectionLines;
      }
      return satBeforeLines;
    }
  }
}
</script>

<style> /* styles for displaying prayers*/
  .T { /* title */
    background-color: maroon;
    font-size: 32px;
    color:white;
    padding: 4, 4, 4, 4;
    text-align: center;
  }

  .H { /* heading */
    color:maroon;
    font-size: 24px;
  }

  .I { /* instruction */
    color:grey;
    font-size: 15px;
  }

  .S { /* subheading */
    color: maroon;
    font-size: 22px;
  }

  .L { /* leader */
    color: mediumblue;
    font-size: 20px;
    padding-bottom: 16px;
    margin-bottom: 0px!important;
  }

  .A { /* assistant */
    color: olivedrab;
    font-size: 20px;
    padding-bottom: 16px;
    margin-bottom: 0px!important;
  }

  .G { /* group */
    font-size: 20px;
    padding-bottom: 16px;
    margin-bottom: 0px!important;
  }
  
  .O { /* optional */
    background-color: #ffeeee;
  }

  .R { /* optionally responsive */
    font-style: italic;
  }

</style>