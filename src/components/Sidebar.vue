<template>
  <div class="">
    <h4>Search</h4>
    <div class="form-group">
      <div class="input-group">
        <div class="input-group-prepend">
          <span class="input-group-text">
            <i class="fa fa-search" aria-hidden="true"></i>
          </span>
        </div>
        <input
          type="search"
          class="form-control"
          v-model="mutableQuery"
          @input="runSearch"
          placeholder="Search"
        />
      </div>
      <button class="btn btn-danger mt-4" @click="resetDb">
        Clear Cached DB
      </button>
      <button class="btn btn-success mt-4" @click="runSearch()">
        Search All
      </button>
    </div>

    <h4>Results</h4>
    <div class="list-group">
      <div class="row">
        <div v-for="item in results" :key="item.id" class="" style="padding: 10px">
          <Item :item="item" class="" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import _debounce from 'lodash/debounce';
import db from '@/database';
import Item from './Item';

export default {
  props: {
    results: Array,
  },
  components: {
    Item,
  },
  created() {
    if (this.$route.params.item_name) {
      let self = this;
      db.items.get({ name: this.$route.params.item_name }).then((result) => {
        if (result) {
          self.mutableQuery = result.displayName;
        } else {
          self.mutableQuery = self.$route.params.item_name;
        }
        self.$emit('runSearch', self.mutableQuery);
      });
    } else {
      this.$emit('runSearch', '');
    }
  },
  data() {
    return {
      mutableQuery: '',
    };
  },
  computed: {
    runSearch() {
      return _debounce(function inputCaptured(e) {
        this.$emit('runSearch', e?.srcElement?.value || ''  );
      }, 50).bind(this);
    },
  },
  methods: {
    resetDb: function() {
      db.delete();
      location.reload();
    },
  },
};
</script>
