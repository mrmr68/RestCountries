<template>
  <div class="container">
    <div class="rowFilterHome">
      <q-input
        v-model="search"
        input-class="text-left"
        label="Search for a country"
        class="searchCountryInput"
      >
        <template v-slot:prepend>
          <q-icon v-if="search === ''" name="search" />
          <q-icon
            v-else
            name="clear"
            class="cursor-pointer"
            @click="search = ''"
          />
        </template>
      </q-input>

      <q-select
        v-model="filterSortBy"
        :options="optionsSortBy"
        label="Sort Coutries by"
        class="filterCountrySelect"
      />

      <q-select
        v-model="filterSortMethod"
        :options="optionsSortMethod"
        label="Sort Method"
        class="filterCountrySelect"
      />

      <q-select
        v-model="filterRegion"
        :options="optionsRegions"
        label="Filter by Region"
        class="filterCountrySelect"
      />      
    </div>
    <div class="container-countries">
      <div class="row">
        <card-country
          v-for="(item, index) in filteredCountries"
          :key="index"
          :arrayCountry="item">
        </card-country>
      </div>
    </div>
  </div>
</template>
<script>
import { defineComponent } from "vue";
import { Loading, Notify } from "quasar";
import cardCountry from "../components/cardCountry.vue";
import { api } from "boot/axios";
export default defineComponent({
  components: { cardCountry },
  name: "homePage",
  watch: {
    filterRegion(value) {
      if (value == "All") {
        this.filteredCountries = this.allCountries;
      } 
      else {
        this.filteredCountries = this.allCountries.filter((country) => {
          return country.region.match(value);            
        });
      }      
      this.sortCountries();
    },
    filterSortBy() {
      this.sortCountries();
    },
    filterSortMethod() {
      this.sortCountries();
    },    
    search(value) {
      clearTimeout(this.timeout);
      this.timeout = setTimeout(() => {
        this.filterRegion = "All";
        if (value == "") {
          this.filteredCountries = this.allCountries;
        } 
        else {
          let country = Object.keys(this.abbreviations).find(country => this.abbreviations[country].includes(value.toLowerCase()));
          if (country) {
            value = country;
          }
          this.filteredCountries = this.allCountries.filter((country) => {
            return country.name.common.toLowerCase().includes(value.toLowerCase());
          });    
        }
        this.sortCountries();
      }, 0);
    }
  },
  data() {
    return {
      search: "",
      filterRegion: null,
      filterSortBy: null,
      filterSortMethod: null,
      optionsSortBy: ["CountryName","Population"],
      optionsSortMethod: ["Asc","Desc"],
      optionsRegions: ["Africa", "America", "Asia", "Europe", "Oceania", "All"],
      allCountries: [],
      filteredCountries: [],
      timeout: null,
      abbreviations: {
        "germany": ["grmany", "grmny"],
        "iran": ["irn"]
      }
    };
  },
  methods: {
    async getCountries() {
      Loading.show();
      await api
        .get("all")
        .then((response) => {
          this.allCountries = response.data;
          const url = new URL(location);
          this.filterRegion = this.optionsRegions.indexOf(url.searchParams.get("region")) > -1 ? url.searchParams.get("region") : "All";
          this.filterSortBy = this.optionsSortBy.indexOf(url.searchParams.get("sortBy")) > -1 ? url.searchParams.get("sortBy") : "CountryName";
          this.filterSortMethod = this.optionsSortMethod.indexOf(url.searchParams.get("sortMethod")) > -1 ? url.searchParams.get("sortMethod") : "Asc";
          this.search = url.searchParams.get("search") || "";
          Loading.hide();
        })
        .catch(() => {
          Loading.hide();
          this.$q.notify({
            type: "info",
            message: "Error in get information of countries"
          });
        });
    },
    sortCountries() {
      if (this.filterSortBy == "CountryName") {
        if (this.filterSortMethod == "Asc") {
          this.filteredCountries.sort((a, b) => a.name.common.toLowerCase().localeCompare(b.name.common.toLowerCase()));
        }
        else {
          this.filteredCountries.sort((a, b) => b.name.common.toLowerCase().localeCompare(a.name.common.toLowerCase()));
        }
      } 
      else {
        if (this.filterSortMethod == "Asc") {
          this.filteredCountries.sort((a, b) => a.population - b.population);
        }
        else {
          this.filteredCountries.sort((a, b) => b.population - a.population);
        }        
      }
      this.setQueryString();
    },
    setQueryString() {
      const url = new URL(location);   
      url.searchParams.set("region", this.filterRegion);
      url.searchParams.set("sortBy", this.filterSortBy);
      url.searchParams.set("sortMethod", this.filterSortMethod);
      if (this.search){
        url.searchParams.set("search", this.search);
      }
      else {
        url.searchParams.delete("search")
      }
      history.pushState({}, "", url);
    }
  },
  mounted() {
    this.getCountries();
  }
});
</script>
<style lang="scss" scoped>
  @import '../css/home.scss'
</style>