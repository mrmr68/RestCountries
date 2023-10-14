<template>
  <div class="col-md-3 col-sm-6 col-xs-12 columCard" v-if="arrayCountry.capital">
    <router-link :to="{
        name: 'country-detail',
        params: { detail: arrayCountry.name.common },
      }">
      <q-intersection transition="scale" class="intersection">
        <q-card class="cardCountry">
          <div class="imgContainer">
            <img :src="arrayCountry.flags.png" />
          </div>
          <q-card-section>
            <div class="text-h6 cardCountryName">
              <b>{{ arrayCountry.name.common }}</b>
            </div>
            <div class="text-subtitle2">
              <b>Population: </b><span>{{ arrayCountry.population.toLocaleString() }}</span>
            </div>
            <div class="text-subtitle2">
              <b>Region: </b><span>{{ arrayCountry.region }}</span>
            </div>
            <div class="text-subtitle2">
              <b>Capital: </b><span>{{ arrayCountry.capital[0] }}</span>
            </div>
          </q-card-section>
        </q-card>
      </q-intersection>
    </router-link>
  </div>

</template>

<script>
  export default {
    name: "cardCountry",
    props: ["arrayCountry"]
  };

</script>
<style lang="scss" scoped>
  @import '../css/cardCountry.scss'

</style>
