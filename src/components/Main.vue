<template>
  <div class="backdrop-filter backdrop-blur-sm">
    <div class="flex flex-row">
      <div class="underline flex items-end">
        <a href="https://github.com/kochrt/timeline-maker">Github</a>
      </div>
      <button
        class="ml-auto hover:bg-gray-500 rounded px-3 flex flex-row items-center"
        @click="collapsed = !collapsed"
      >
        {{ collapsed ? "Expand" : "Collapse" }}
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-3 w-3 ml-1"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
          v-if="!collapsed"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M19 9l-7 7-7-7"
          />
        </svg>
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-3 w-3"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
          v-if="collapsed"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M5 15l7-7 7 7"
          />
        </svg>
      </button>
    </div>
    <div class="flex md:flex-row flex-col pt-3" v-show="!collapsed">
      <storage
        :list="list"
        @selected="selectedTimeline"
        @delete="deleteTimeline"
      />
      <div class="flex flex-col mb-3 text-black">
        <textarea
          class="border shadow-md flex-grow p-2"
          name="eventsField"
          cols="55"
          rows="12"
          v-model="events"
        ></textarea>
        <button
          class="bg-blue-100 mt-3 rounded shadow-md hover:shadow-none transition-all duration-100"
          @click="save"
        >
          Save timeline
        </button>
      </div>
      <timeline-maker :eventString="events" class="flex-grow mr-3 mb-3" />
    </div>
  </div>
</template>

<script lang="ts">
import Storage from "./Storage.vue";
import TimelineMaker from "./TimelineMaker.vue";

export default {
  components: { TimelineMaker, Storage },
  data() {
    return {
      collapsed: false,
      currentTimelineName: "",
      events: `08/2008-05/2012: Psych degree
02/2010-06/2012: Dispatcher
10/2010: Barn built across the street
06/2011-08/2011: Westover Air Reserve Base

// 2013
03/15/2013-04/2015: China

// 2014
07/2014: 4th of July in DC

// 2015
05/2015-08/2015: Summer classes so I can graduate in two years
05/2015: James graduation
06/2015: Built desk
06/2015: Kim and Matt wedding
08/2015-05/2017: CS degree

// 2016
05/22/2016-08/12/2016: Cardinal Health
08/16/2016-08/27/2016: Italy

// 2017
05/2017-05/2018: Cladwell
06/10/2017-06/17/2017: The Hague & Copenhagen

// 2018
07/21/2018-07/22/2018: Chicago
07/26/2018-07/31/2018: LA and Seattle (interviewing)
08/04/2018-08/14/2018: Mexico City
09/05/2018-09/11/2018: Hong Kong and Macau
09/19/2018-09/22/2018: Road trip to Seattle
10/01/2018-01/2021: [Google](https://www.google.com)
12/28/2018-12/29/2018: Nemacolin and Fallingwater

// 2019
06/08/2019: Paula's wedding
07/04/2019: 4th of July in Seattle with siblings
08/23/2019-08/27/2019: SF and Bishop's Ranch
09/2019: Hawaii with Google
12/20/2019-12/22/2019: Train from Seattle to Chicago
12/2019: Christmas at home, Dad to hospital

// 2020
02/29/2020: Molly and Kaitlyn to Seattle (thus starting covid)
03/28/2020: James to Austin
05/24/2020: Sold the Impala
07/2020: Oregon & Crater Lake
08/2020: Mt. Rainier
08/2020: Oak Island
09/2020: Hurricane Ridge
9/2020: Trip to Coeur d'Alene
11/2020: Trip to Denver
12/2020: Reese
12/25/2020: Christmas in Blaine

// 2021
01/2021: qr.new featured on [Hacker News](https://news.ycombinator.com/item?id=25481772)
02/2021: Hawaii
02/01/2021-now: Working on [swink](https://sw.ink) full time
05/25/2021: [cascade.page](https://cascade.page) featured on [Hacker News](https://news.ycombinator.com/item?id=27282842)`,
      list: [] as string[],
    };
  },
  mounted() {
    this.getTimelines();
  },
  methods: {
    deleteTimeline(name: string) {
      if (confirm(`Delete ${name}?`)) {
        localStorage.removeItem(name);
        this.list.splice(this.list.indexOf(name), 1);
        localStorage.setItem("timelines", this.list.join(","));
      }
    },
    selectedTimeline(name: string) {
      this.loadTimeline(name);
    },
    save() {
      const timelineName = prompt(
        "Save timeline as: ",
        this.currentTimelineName
      );
      if (timelineName) {
        localStorage.setItem(timelineName, this.events);
        if (this.list.includes(timelineName)) {
          return;
        }
        this.list.push(timelineName);
        localStorage.setItem("timelines", this.list.join(","));
      }
    },
    loadTimeline(name: string) {
      this.events = localStorage.getItem(name) ?? "";
      this.currentTimelineName = name;
    },
    getTimelines() {
      const concatenatedList = localStorage.getItem("timelines");
      if (concatenatedList) {
        this.list = concatenatedList.split(",");
        this.loadTimeline(this.list[0]);
      }
    },
  },
};
</script>

<style>
body {
  background-color: #384047;
  color: white;
  height: 100vh;
  margin: 0;
}
</style>