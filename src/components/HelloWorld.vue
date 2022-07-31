<template>
  <div class="">
    <hr class="me-2" />
    <div class="row">
      <div class="col-md-8 mt-2">
        <div class="border rounded-3 p-1 pt-0 pb-0">
          <div class="w-100 border-bottom">
            <h6 class="m-0 p-2" v-text="chartOptions.summary.header"></h6>
          </div>
          <GChart
            class=""
            type="ColumnChart"
            :data="chartSummary"
            :resizeDebounce="500"
            :options="chartOptions.summary"
          />
        </div>
      </div>
      <div class="col-md-4 mt-2">
        <div class="border rounded-3 p-1 pt-0 pb-0">
          <div class="w-100 border-bottom">
            <h6 class="m-0 p-2" v-text="chartOptions.gender.header"></h6>
          </div>
          <GChart
            class=""
            type="PieChart"
            :data="chartGender"
            :resizeDebounce="500"
            :options="chartOptions.gender"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import record from "../record.json";
import { GChart } from "vue-google-charts/legacy";
export default {
  name: "users-chart",
  components: {
    GChart,
  },
  data() {
    return {
      info: [],
      loadStatus: "load",
      chartSummary: [],
      chartGender: [],
      chartOptions: {
        summary: {
          header: "Summary",
          title: "",
          is3D: true,
          height: 250,
        },
        gender: {
          header: "Genders",
          title: "",
          is3D: true,
          height: 250,
        },
      },
    };
  },

  created() {
    this.getRecord();
  },
  mounted() {},
  methods: {
    getRecord: function () {
      this.info = record;
      // console.log(this.info);
      this.genderPlot();
      this.summaryPlot();
    },

    genderPlot: function () {
      const grouped = _.groupBy(this.info, (info) => info.gender_id);
      this.chartGender.push(["Gender", "Total"]);
      this.chartOptions.gender.title = "Users by Genders";
      for (var key in grouped) {
        this.chartGender.push([key.toString(), grouped[key].length]);
      }
    },

    summaryPlot: function () {
      const grouped = _.groupBy(this.info, (info) =>
        info.date_created.substring(5, 7)
      );
      this.chartSummary.push(["Year", "New", "Active", "Blocked"]);
      this.chartOptions.summary.title =
        "Users' Summary (New, Active and blocked) by Year";
      for (var key in grouped) {
        var active = 0;
        var blocked = 0;
        var newUser = 0;
        for (let index = 0; index < grouped[key].length; index++) {
          newUser += grouped[key][index].status_name == "New" ? 1 : 0;
          active += grouped[key][index].status_name == "Active" ? 1 : 0;
          blocked += grouped[key][index].status_name == "Blocked" ? 1 : 0;
        }
        this.chartSummary.push([key.toString(), active, blocked, newUser]);
      }
    },
  },
};
</script>