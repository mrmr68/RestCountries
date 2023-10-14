<template>
  <div class="container">
    <div class="container_detail_country" v-if="arrayCountry.length > 0">
      <q-btn
        to="/"
        label="Back"
        icon="keyboard_backspace"
        :class="{
          'bg-custom-black': this.$store.state.myStore.darkMode,
          'bg-custom-white': this.$store.state.myStore.darkMode == false,
        }"
      />

      <div class="container_country">
        <img :src="arrayCountry[0].flags.png" alt="" />
        <div class="detail_country col-md-6 col-sm-12 col-xs-12">
          <h1>{{ arrayCountry[0].name.common }}</h1>
          <div class="country_features">
            <div class="features">
              <p>
                <b>Official Name: </b>
                {{ arrayCountry[0].name.official }}
              </p>
              <p><b>Population: </b> {{ arrayCountry[0].population }}</p>
              <p><b>Region: </b> {{ arrayCountry[0].region }}</p>
              <p><b>Sub Region: </b> {{ arrayCountry[0].subregion }}</p>
              <p><b>Capital: </b> {{ arrayCountry[0].capital[0] }}</p>
            </div>
            <div class="features">
              <p><b>Top Level Domain: </b>{{ arrayCountry[0].tld[0] }}</p>
              <p>
                <b>Currencies: </b>
                <span
                  v-for="(item, name, index) in arrayCountry[0].currencies"
                  :key="index"
                  >{{ item.name }}</span
                >
              </p>
              <p>
                <b>Languajes: </b>
                <span
                  v-for="(item, name, index) in arrayCountry[0].languages"
                  :key="index"
                >
                  <span v-if="index < Object.keys(arrayCountry[0].languages).length - 1"
                    >{{ item }}, &nbsp;
                  </span>
                  <span v-else>{{ item }}</span>
                </span>
              </p>
            </div>
          </div>

          <div class="border_countries">
            <b>Border Countries: </b>
            <div style="max-width: 400px">
              <q-btn
                v-for="(item, name, index) in borders"
                :key="index"
                :label="item.name.common"
                :class="{
                  'bg-custom-black': this.$store.state.myStore.darkMode,
                  'bg-custom-white':
                    this.$store.state.myStore.darkMode == false,
                }"
                :to="{
                  name: 'country-detail',
                  params: { detail: item.name.common },
                }"
              />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { api } from "boot/axios";
import { defineComponent } from "vue";
import { Loading } from "quasar";
export default defineComponent({
  name: "detailsPage",

  data() {
    return {
      arrayCountry: [],
      borders: [],
    };
  },
  watch: {
    $route(to, from) {
      if (to.params.details && to.params.detail != from.params.detail) {
        this.getData();
      }
    },
  },

  methods: {
    async getData() {
      Loading.show();
      await api
        .get("name/" + this.$route.params.detail + "?fullText=true")
        .then((response) => {
          this.arrayCountry = response.data;
        });

      if (this.arrayCountry[0].borders) {
        var bordersCode = "";
        this.arrayCountry[0].borders.map(function (res, index) {
          bordersCode = bordersCode + res + ",";
        });
        await api.get("alpha?codes=" + bordersCode).then((response) => {
          this.borders = response.data;
          Loading.hide();
        });
      } else {
        Loading.hide();
      }
    }
  },
  mounted() {
    this.getData();
  }
});
</script>

<style lang="scss" scoped>
  @import '../css/details.scss'
</style>