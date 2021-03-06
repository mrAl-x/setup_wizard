<template>
  <div>
    <label class="invisible" for="searchBox">Search app</label>
    <input
      type="text"
      id="searchBox"
      class="filterInput"
      v-model="filter"
      ref="searchInput"
      placeholder="Search"
    />
    <ul class="categoryList">
      <li class="category" v-for="(category, index) in categories" :key="index">
        <h2
          v-if="filter === '' || appsOfCategory(category).length > 0"
          class="category__title"
        >{{ category }}</h2>
        <ul class="appList">
          <AppCheck
            v-for="(app, index) in appsOfCategory(category)"
            :name="app.name"
            :code="app.code"
            :logoUrl="app.logoUrl"
            :key="index"
            @handleAppCheck="(payload) => $emit('handleAppCheck', payload)"
          />
        </ul>
      </li>
    </ul>
  </div>
</template>

<script>
import apps from "../../shared/data/apps";
import AppCheck from "../app-check/AppCheck.vue";

export default {
  name: "AppList",
  components: { AppCheck },
  data() {
    return {
      apps: apps,
      filter: "",
      selectedApps: []
    };
  },
  created() {
    this.categories = this.getCategories();
  },

  mounted() {
    const searchInput = this.$refs.searchInput;

    this.inputFocus(searchInput);
  },

  computed: {
    filteredApps() {
      /* Regex will be case insentive */
      const regex = new RegExp(this.filter, "i");
      const filteredApps = this.apps.filter(app => app.name.match(regex));

      return filteredApps;
    }
  },
  methods: {
    getCategories() {
      const categories = apps.reduce((acc, app) => {
        if (!acc) {
          return [app.category];
        }

        return acc.find(category => category === app.category)
          ? acc
          : [...acc, app.category];
      }, null);

      return categories;
    },

    sortAlphabetically(list) {
      return list.sort((first, second) => {
        if (first.name < second.name) {
          return -1;
        }

        if (first.name > second.name) {
          return 1;
        }

        return 0;
      });
    },

    appsOfCategory(category) {
      const appsOfCategory = this.filteredApps.filter(
        app => app.category === category
      );

      return this.sortAlphabetically(appsOfCategory);
    },

    inputFocus(ref) {
      ref.focus();
    }
  }
};
</script>

<style src='./AppList.css' />;