<template>
  <div class="" style="padding: 20px;">
    <div class="row">
      <div class="col-md-4">
        <Sidebar v-on:runSearch="updateQuery" :results="results" />
      </div>
      <div class="col-md-8">
        <ResultView :item="selectedItem" />
      </div>
    </div>
  </div>
</template>

<script>
import Sidebar from "@/components/Sidebar.vue";
import ResultView from "@/components/ResultView.vue";
import db from "@/database";

export default {
  components: {
    Sidebar,
    ResultView
  },
  data() {
    return {
      results: [],
      selectedItem: undefined
    };
  },
  methods: {
    updateQuery(query = "") {
      if (query.length) {
        let self = this;
        let regex = new RegExp("", "i");

        console.log(db.items);

        db.items
          .filter(item => regex.test(item.displayName))
          .toArray()
          .then(function(results) {
            self.results = results;
          });
      } else {
        this.results = [];
      }
    }
  },
  watch: {
    "$route.params": {
      immediate: true,
      handler(routeParams) {
        let self = this;
        if (!routeParams.item_name) {
          return;
        }

        db.items
          .where("name")
          .equals(routeParams.item_name)
          .first()
          .then(function(item) {
            if (item) {
              self.selectedItem = item;
            }
          });
      }
    }
  }
};
</script>
