<template>
  <q-page v-if="countries" class="q-pa-xs bgImage">
    <q-card flat class="transparent">
      <q-tabs
        v-model="tab"
        dense
        class="text-dark"
        active-color="primary"
        indicator-color="primary"
        narrow-indicator
      >
        <q-tab name="home" label="Home" />
        <q-tab name="stats" label="Statistics" />
      </q-tabs>

      <q-separator />

        <!-- TAB PANELS -->

      <q-tab-panels v-model="tab" animated class="transparent" style="height: 100%">

        <!-- HOME PAGE -->
        <q-tab-panel name="home">
          <div class="row flex flex-center q-pa-md q-mb-md">
            <div class="col-lg-4 col-md-5 col-sm-8 col-xs-12">
              <div class="q-my-md text-bold text-center" style="font-size: 20px">Ready to learn about the countries of the world ?</div>
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
              <q-card-section class="q-pa-none" style="height: 45%; width: 100%">
                <q-img style="height: 100%; width: 100%" :src="singleCountry.flag" />
              </q-card-section>
              <!-- <q-img style="height: 100%; width: 100%" :src="singleCountry.flag" /> -->

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
              <q-carousel-slide v-show="homeCard[0]" v-for="(c, index) in homeCard" :key="index" :name="index">
                <q-card class="my-card col-lg-3 col-md-3 col-sm-4 col-xs-10 ">
                  <q-card-section class="q-pa-none" style="height: 45%; width: 100%">
                    <q-img style="height: 100%; width: 100%; margin-left: auto; margin-right: auto;" :src="countries[c].flag" />
                  </q-card-section>
                  <!-- <q-img style="height: 45%" :src="countries[c].flag" /> -->

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

          <q-page-sticky position="bottom-right" :offset="[10, 10]">
            <div class="text-caption">by Jaures Appia &#128526;</div>
          </q-page-sticky>
        </q-tab-panel>

        <!-- STATISTICS PAGE -->
        <q-tab-panel name="stats" style="height: 100%">
          <div class="text-h5 text-center q-mt-md">
            {{ this.countries.length }} Countries
          </div>

          <div class="row q-gutter-md justify-center q-my-sm">
            <q-card class="col-4 q-pa-md bg-brown-12">
              <div class="text-h6 text-bold text-center">{{africanCountires}}</div>
              <div class="text-center text-caption">countries of Africa</div>
            </q-card>

            <q-card class="col-4 q-pa-md bg-blue-grey-12">
              <div class="text-h6 text-bold text-center">{{americanCountires}}</div>
              <div class="text-center text-caption">countries of Americas</div>
            </q-card>

            <q-card class="col-4 q-pa-md bg-yellow-11">
              <div class="text-h6 text-bold text-center">{{asiaCountires}}</div>
              <div class="text-center text-caption">countries of Asia</div>
            </q-card>

            <q-card class="col-4 q-pa-md bg-blue-11">
              <div class="text-h6 text-bold text-center">{{europaCountires}}</div>
              <div class="text-center text-caption">countries of Europe</div>
            </q-card>

            <q-card class="col-4 q-pa-md bg-light-blue-11">
              <div class="text-h6 text-bold text-center">{{oceaniaCountires}}</div>
              <div class="text-center text-caption">countries of oceania</div>
            </q-card>

            <q-card class="col-4 q-pa-md bg-cyan-11">
              <div class="text-h6 text-bold text-center">{{polarCountires}}</div>
              <div class="text-center text-caption">countries of Polar</div>
            </q-card>
          </div>
          <!-- <q-carousel
            v-model="slideStat"
            animated
            control-color="primary"
            navigation
            padding
            infinite
            class="text-dark transparent q-mt-md"
            style="height: 100%"
          >
            <q-carousel-slide name="AllStats">
              <div class="text-h5 text-center q-mt-md">
                {{ this.countries.length }} Countries
              </div>

              <div class="row q-gutter-md justify-center q-my-sm">
                <q-card class="col-4 q-pa-md">
                  <div class="text-h6 text-bold text-center">{{africanCountires}}</div>
                  <div class="text-center text-caption">countries of africa</div>
                </q-card>

                <q-card class="col-4 q-pa-md">
                  <div class="text-h6 text-bold text-center">{{americanCountires}}</div>
                  <div class="text-center text-caption">countries of americas</div>
                </q-card>

                <q-card class="col-4 q-pa-md">
                  <div class="text-h6 text-bold text-center">{{asiaCountires}}</div>
                  <div class="text-center text-caption">countries of asia</div>
                </q-card>

                <q-card class="col-4 q-pa-md">
                  <div class="text-h6 text-bold text-center">{{europaCountires}}</div>
                  <div class="text-center text-caption">countries of europe</div>
                </q-card>

                <q-card class="col-4 q-pa-md">
                  <div class="text-h6 text-bold text-center">{{oceaniaCountires}}</div>
                  <div class="text-center text-caption">countries of oceania</div>
                </q-card>

                <q-card class="col-4 q-pa-md">
                  <div class="text-h6 text-bold text-center">{{polarCountires}}</div>
                  <div class="text-center text-caption">countries of polar</div>
                </q-card>
              </div>
            </q-carousel-slide>

            <q-carousel-slide name="bigArea">
              <div class="text-center text-h6">top 5 countries with the largest area</div>
              <div class="text-center flex flex-center">
                <div class="q-pb-sm">
                  <apexcharts width="500" type="bar" :options="optionsChart" :series="series"></apexcharts>
                </div>
              </div>
            </q-carousel-slide>

            <q-carousel-slide name="samallArea">
              <div class="text-center text-h6">top 5 countries with the smallest area</div>
              <div class="text-center flex flex-center">
                <div class="q-pb-sm">
                  <apexcharts width="500" type="bar" :options="fiveBigCountryArea" :series="fiveBigCountryName"></apexcharts>
                </div>
              </div>
            </q-carousel-slide>

            <q-carousel-slide name="4">
              <div class="q-mt-md text-center">
                top 5 countries with the largest population
              </div>
            </q-carousel-slide>

            <q-carousel-slide name="5">
              <div class="q-mt-md text-center">
                top 5 countries with the smallest population
              </div>
            </q-carousel-slide>
          </q-carousel> -->

        </q-tab-panel>
      </q-tab-panels>
    </q-card>

  </q-page>
</template>

<script>
import numeral from 'numeral'
// import apexcharts from 'vue-apexcharts'

export default {
  name: 'PageIndex',
  // components: { apexcharts},
  data(){
    return{
      tab: 'home',
      stats: "",
      slide: 1,
      slideStat: "AllStats",
      country: "",
      countries: [],
      fiveBigCountryArea: [{
        name: 'Pays les plus grand en terme de superficie',
        data: []
      }],
      fiveBigCountryName: {
        chart: {
          id: 'vuechart-example'
        },
        xaxis: {
          categories: ["toto", "tata", "titi", "tutu", "tété"]
        }
      },
      url: "https://restcountries.eu/rest/v2/all",
      options: this.myCountries,
      moreStats: [
        "stat 1",
        "stat 2",
        "stat 3",
      ],


      optionsChart: {
        chart: {
          id: 'vuechart-example'
        },
        xaxis: {
          categories: ["toto", "tata", "titi", "tutu", "tété"]
        }
      },
      series: [{
        name: 'Pays les plus grand en terme de superficie',
        data: [70,50,35,20,10]
      }]
    }
  },
  computed: {
    myCountries(){
      return this.countries.map(c => "(+" + c.callingCodes[0] + ") " + c.name )
    },
    myCallingCodes(){
      return this.countries.map(c => c.callingCodes[0])
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
        if (this.country != null) {
          if (this.country.includes(this.countries[i].name)) {
            mySingleCountry = this.countries[i]
          }
        } 
      }
      return mySingleCountry
    },
    cardWidth(){
      return this.$q.screen
    },
    africanCountires(){
      let a = this.countries.map(c => c.region)
      let compter = 0
      a.forEach(item => {
        if(item == "Africa"){
          compter+=1
        }
      });
      return compter
    },
    americanCountires(){
      let a = this.countries.map(c => c.region)
      let compter = 0
      a.forEach(item => {
        if(item == "Americas"){
          compter+=1
        }
      });
      return compter
    },
    asiaCountires(){
      let a = this.countries.map(c => c.region)
      let compter = 0
      a.forEach(item => {
        if(item == "Asia"){
          compter+=1
        }
      });
      return compter
    },
    europaCountires(){
      let a = this.countries.map(c => c.region)
      let compter = 0
      a.forEach(item => {
        if(item == "Europe"){
          compter+=1
        }
      });
      return compter
    },
    oceaniaCountires(){
      let a = this.countries.map(c => c.region)
      let compter = 0
      a.forEach(item => {
        if(item == "Oceania"){
          compter+=1
        }
      });
      return compter
    },
    polarCountires(){
      let a = this.countries.map(c => c.region)
      let compter = 0
      a.forEach(item => {
        if(item == "Polar"){
          compter+=1
        }
      });
      return compter
    },
  },
  beforeMount(){
    fetch(this.url)
      .then(res => res.json())
      .then(data => {
        this.countries = data
        this.homeCard = this.countries[Math.random()]

        // let a = this.countries.map(c => c.area)
        // let b = a.sort(function(a, b){return a-b})
        // for (let index = 0; index < 5; index++) {
        //   this.fiveBigCountryArea.push(b.pop())
        // }
        // this.series[0].data = this.fiveBigCountryArea

        // this.fiveBigCountryArea.forEach(item => {
        //   this.fiveBigCountryName.push(this.countries.filter(p => p.area == item))
        // })
        // let finalBigArea = this.fiveBigCountryName.map(p => p[0].name)
        // // this.optionsChart.xaxis.categories = this.fiveCountryName
        // this.fiveBigCountryName = {
        //   chart: {
        //     id: 'vuechart-example'
        //   },
        //   xaxis: {
        //     categories: finalBigArea
        //   }
        // }
      })
  },
  mounted(){
  },
  methods: {
    filterFn (val, update) {
      if (val === '') {
        update(() => {
          this.options = this.myCountries

          // with Quasar v1.7.4+ .concat(this.myCallingCodes)
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
