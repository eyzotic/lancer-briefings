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
      "mission_slug": "005",
      "current_md": "005.md",
      "events": "005.md",
      "missions": [
        {
          "slug": "001",
          "name": "Bug-Hunt",
          "status": "success"
        },
        {
          "slug": "002",
          "name": "Vigilant Gaze",
          "status": "success"
        },
        {
          "slug": "003",
          "name": "Floodgate",
          "status": "success"
        }, 
        {
          "slug": "004",
          "name": "Rallying Cry",
          "status": "success"
        },
        {
          "slug": "005",
          "name": "Split-Knuckle Haymaker",
          "status": "start"
        }
      ],
      "pilots": [
        {
          "callsign": "Ruiner",
          "alias": "Aster Elliot Rhea",
          "code": "cff6e4be-1636-4788-b52d-9a85b5baa8b4//NDL-C-STOLEN-STAR",
          "corpro": "HORUS",
          "frame": "Hydra",
          "mech": "Marionette God"
        },
        {
          "callsign": "Z3r0",
          "alias": "Ryōsuke Abane",
          "code": "f37a441a-7df3-43c1-963d-f197b4705da5//NDL-C-BLUE-GRAVE",
          "corpro": "HORUS",
          "frame": "Pegasus",
          "mech": "BFG-ba"
        },
        {
          "callsign": "Wanderer",
          "alias": "Kalatha Finsa",
          "code": "f989664f-e4b1-4a6c-8715-fb804632b87c//NDL-C-STEEL-REACH",
          "corpro": "HORUS",
          "frame": "Calendula",
          "mech": "Ten Thousand and a Half Steps"
        },
        {
          "callsign": "Martial",
          "alias": "James Saintjames",
          "code": "bfdb39eb-9f47-4670-a67c-6691c444b80e//NDL-C-SATELLITE-AXE",
          "corpro": "SSC",
          "frame": "Viceroy",
          "mech": "Regulus"
        },
      ],
      "header": {
        "planet": "Hercynia",
        "year": "5014u",
        "system": "Ardennes-3",
        "gate": "Atlas-Quanokrim",
        "ring": "Atlas-Line",
        "headerTitle": "Mirrorsmoke",
        "headerSubtitle": "Mercenary Company",
        "subheaderTitle": "Crisis Response",
        "subheaderSubtitle": "Echo-Yankee-Zulu",
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
