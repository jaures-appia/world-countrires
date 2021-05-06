<template>
  <q-page v-if="countries" class="q-pa-xs bgImage">
      <div class="row flex flex-center q-pa-lg q-mb-md">
        <div class="col-lg-4 col-md-5 col-sm-8 col-xs-12">
          <div class="q-my-md text-bold text-center" style="font-size: 20px">Ready to learn about the countries of the world</div>
          <q-select
            outlined
            v-model="country"
            clearable
            use-input
            input-debounce="0"
            label="search country"
            :options="options"
            @filter="filterFn"
            color="dark"
          >
            <template v-slot:no-option>
              <q-item>
                <q-item-section class="text-grey">
                  No results
                </q-item-section>
              </q-item>
            </template>
          </q-select>
        </div>
      </div>

      <div v-if="singleCountry.name" class="row justify-center" style="height: 65%">
        <q-card class="my-card q-ma-sm col-lg-3 col-md-4 col-sm-6 col-xs-10" style="height: 90%">
          <q-img style="height: 45%" :src="singleCountry.flag" />

          <q-card-section>
            <div class="row no-wrap items-center">
              <div class="col text-h6 ellipsis">
                {{ singleCountry.name }} ({{ singleCountry.alpha3Code }})
              </div>
            </div>
          </q-card-section>

          <q-card-section class="q-pt-none">
            <div class="text-caption">
              capital, {{ singleCountry.capital }}, {{ singleCountry.name }} is an {{ singleCountry.region }} 
              country with {{ formatNum(singleCountry.population) }} inhabitants over {{ formatNum(singleCountry.area) }} km2
            </div>
            <div class="text-caption q-mt-xs">
              <span class="text-bold">languages:</span>  
              <span v-for="(language, i) in singleCountry.languages" :key="i">
                {{ language.name }} <span v-if="i != singleCountry.languages.length -1">,</span> 
              </span>
            </div>
          </q-card-section>

          <q-separator />

          <q-card-section>
            <q-btn flat size="sm" :label="'+' + singleCountry.callingCodes[0]">
              <q-tooltip>
                callingCodes
              </q-tooltip>
            </q-btn> 
            <q-btn flat size="sm" :label="singleCountry.currencies[0].name">
              <q-tooltip>
                currency
              </q-tooltip>
            </q-btn>
            
          </q-card-section>

        </q-card>

      </div>
      
      <div v-else class="row q-gutter-md justify-center" style="height: 65%">
        <q-carousel
          v-if="cardWidth.name == 'sm' || cardWidth.name == 'xs'"
          animated
          v-model="slide"
          control-color="primary"
          navigation
          infinite
          class="col-sm-8 col-xs-10 transparent q-pb-xl"
        >
          <q-carousel-slide v-for="(c, index) in homeCard" :key="index" :name="index">
            <q-card class="my-card col-lg-3 col-md-3 col-sm-4 col-xs-10 ">
              <q-img style="height: 45%" :src="countries[c].flag" />

              <q-card-section style="height: 55%">
                <q-card-section class="q-pa-none">
                    <div class="text-bold" style="font-size: 16px">
                      {{ countries[c].name }} ({{ countries[c].alpha3Code }})
                    </div>
                </q-card-section>

                <q-card-section class="q-pa-xs">
                  <div class="text-caption">
                    capital, {{ countries[c].capital }}, {{ countries[c].name }} is an {{ countries[c].region }} 
                    country with {{ formatNum(countries[c].population) }} inhabitants over {{ formatNum(countries[c].area) }} km2
                  </div>
                  <div class="text-caption q-mt-xs">
                    <span class="text-bold">languages:</span> 
                    <span v-for="(language, i) in countries[c].languages" :key="i">
                      {{ language.name }} <span v-if="i != countries[c].languages.length -1">,</span> 
                    </span>
                  </div>
                </q-card-section>

                <q-separator />

                <q-card-section>
                  <q-btn flat size="sm" :label="'+' + countries[c].callingCodes[0]">
                    <q-tooltip>
                      callingCodes
                    </q-tooltip>
                  </q-btn> 
                  <q-btn flat size="sm" :label="countries[c].currencies[0].name">
                    <q-tooltip>
                      currency
                    </q-tooltip>
                  </q-btn>
                </q-card-section>
              </q-card-section>

            </q-card>
          </q-carousel-slide>
        </q-carousel>

        
        <q-card v-else class="my-card col-lg-3 col-md-3 col-sm-4 col-xs-10 " style="height: 90%" v-for="(c, index) in homeCard" :key="index">
          <q-img style="height: 250px; width: 100%" :src="countries[c].flag"  spinner-color="white" />

          <q-card-section style="height: 55%">
            <q-card-section class="q-pa-none">
                <div class="text-bold" style="font-size: 16px">
                  {{ countries[c].name }} ({{ countries[c].alpha3Code }})
                </div>
            </q-card-section>

            <q-card-section class="q-pa-xs">
              <div class="text-caption">
                capital, {{ countries[c].capital }}, {{ countries[c].name }} is an {{ countries[c].region }} 
                country with {{ formatNum(countries[c].population) }} inhabitants over {{ formatNum(countries[c].area) }} km2
              </div>
              <div class="text-caption q-mt-xs">
                <span class="text-bold">languages:</span> 
                <span v-for="(language, i) in countries[c].languages" :key="i">
                  {{ language.name }} <span v-if="i != countries[c].languages.length -1">,</span> 
                </span>
              </div>
            </q-card-section>

            <q-separator />

            <q-card-section>
              <q-btn flat size="sm" :label="'+' + countries[c].callingCodes[0]">
                <q-tooltip>
                  callingCodes
                </q-tooltip>
              </q-btn> 
              <q-btn flat size="sm" :label="countries[c].currencies[0].name">
                <q-tooltip>
                  currency
                </q-tooltip>
              </q-btn>
            </q-card-section>
          </q-card-section>

        </q-card>

        
      </div>

  </q-page>
</template>

<script>
import numeral from 'numeral'

export default {
  name: 'PageIndex',
  data(){
    return{
      slide: 1,
      country: "",
      countries: null,
      url: "https://restcountries.eu/rest/v2/all",
      test: ["a", "b", "c"],
      options: this.myCountries,
      num: 100000000
    }
  },
  computed: {
    myCountries(){
      return this.countries.map(c => c.name)
    },
    homeCard(){
      let a = []
      for (let i = 0; i < 3; i++) {
        a.push(Math.floor(Math.random() * 250))
      }
      return a
    },
    singleCountry(){
      let mySingleCountry = []
      for (let i = 0; i < this.countries.length; i++) {
        if (this.countries[i].name == this.country) {
          mySingleCountry = this.countries[i]
        } 
      }
      return mySingleCountry
    },
    cardWidth(){
      return this.$q.screen
    }
  },
  beforeMount(){
    fetch(this.url)
      .then(res => res.json())
      .then(data => {
        this.countries = data
        this.homeCard = this.countries[Math.random()]
        })
  },
  methods: {
    filterFn (val, update) {
      if (val === '') {
        update(() => {
          this.options = this.myCountries

          // with Quasar v1.7.4+
          // here you have access to "ref" which
          // is the Vue reference of the QSelect
        })
        return
      }

      update(() => {
        const needle = val.toLowerCase()
        this.options = this.myCountries.filter(v => v.toLowerCase().indexOf(needle) > -1)
      })
    },
    formatNum(num){
      return numeral(num).format('0,0')
    }
  }
}
</script>

<style lang="scss" scoped>
.bgImage{
  width: 100%;
  background-image: linear-gradient(to right top, #c0ee9c, #a1f0c1, #97eedf, #a6e8ef, #bfe1f0);
}

</style>
