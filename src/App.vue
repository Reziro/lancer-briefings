<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "CRISIS",
      "current_md": "",
      "events": "",
      "missions": [
		{
		  "slug": "1",
		  "name": "Bug Hunt",
		  "status": "success"
		},
		{
		  "slug": "2",
		  "name": "Vigilant Gaze",
		  "status": "success"
		},
		{
		  "slug": "3",
		  "name": "Floodgate",
		  "status": "success"
		},
    {
      "slug": "4B",
      "name": "Rallying Cry",
      "status": "partial-success"
    },
    {
      "slug": "CRISIS",
      "name": "ABADDON",
      "status": "start"
    }
      ],
      "pilots": [
        {
          "callsign": "Aces",
          "alias": "Adrian Gallagher",
          "code": "8c2f582c-11a9-4930-8004-5c51ad5b8a7f.Adrian.Gallagher:8c2f582c-11a9-4930-8004-5c51ad5b8a7f//NDL-C-THIRD-SKY",
          "corpro": "SSC",
          "frame": "Swallowtail",
          "mech": "Goodbye Kiss"
        },
        {
          "callsign": "Longshot",
          "alias": "Billy Bob Smithson",
          "code": "b6f13a45-4e8a-4fed-af76-aab748028643.Smithson.Billy.Bob:b6f13a45-4e8a-4fed-af76-aab748028643//NDL-C-FOURTH-AXE",
          "corpro": "HA",
          "frame": "Genghis Mk I 'WORLDKILLER'",
          "mech": "The Uncomfortable Truth"
        },
        {
          "callsign": "Lonestar",
          "alias": "Jesse Williams",
          "code": "47fa04d6-5bcb-423a-a678-177b0b70d95d.Williams.Jesse:47fa04d6-5bcb-423a-a678-177b0b70d95d//NDL-C-FIRST-TEMPLE",
          "corpro": "IPS-N",
          "frame": "Tortuga",
          "mech": "Laugh While You Can"
        },
        {
          "callsign": "Dragon",
          "alias": "Voldrin Montrose",
          "code": "d2c3fd0e-72c5-43a4-af02-d59717712aeb.Montrose.Voldrin:d2c3fd0e-72c5-43a4-af02-d59717712aeb//NDL-C-TANGENT-MOUNTAIN",
          "corpro": "HORUS",
          "frame": "Calendula",
          "mech": "The Black Knight"
        },
      ],
      "header": {
        "planet": "Hercynia",
        "year": "5014u",
        "system": "Ardennes-3",
        "gate": "Atlas-Quanokrim",
        "ring": "Atlas-Line",
        "headerTitle": "Union",
        "headerSubtitle": "Auxiliary Corps",
        "subheaderTitle": "Crisis Response",
        "subheaderSubtitle": "Revolutionaries",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
