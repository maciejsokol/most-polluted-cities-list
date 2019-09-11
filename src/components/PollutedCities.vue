<template>
  <div class="pollute-list" v-if="results.length">
    <svg xmlns="http://www.w3.org/2000/svg" class="clouds" viewBox="0 0 300 206">
      <path id="cloud3" class="cloud"
            d="M-300 0h600c200 53 200 107 0 160a267 267 0 0 1-300 0 267 267 0 0 1-300 0c-200-53-200-107 0-160"/>
      <path id="cloud2" class="cloud"
            d="M-300 0h600c200 43 200 87 0 130a317 317 0 0 1-300 0 317 317 0 0 1-300 0c-200-43-200-87 0-130"/>
      <path id="cloud1" class="cloud"
            d="M-300 0h600c200 33 200 67 0 100a260 260 0 0 1-300 0 260 260 0 0 1-300 0c-200-33-200-67 0-100"/>
    </svg>

    <table>
      <thead>
      <tr>
        <th>City</th>
        <th>Pollute value</th>
        <th></th>
      </tr>
      </thead>
      <tbody v-for="(cityInfo, i) in results" :key="i">
      <tr>
        <td>{{ cityInfo.city }}</td>
        <td>{{ cityInfo.count }}</td>
        <td>
          <button type="button" @click="manageDescription(cityInfo)">&#128712;</button>
          <!-- https://www.compart.com/en/unicode/U+1F6C8 -->
        </td>
      </tr>
      <tr>
        <td colspan="3">
          <transition-expand>
            <div v-if="cityInfo.showDescription">
              <CityDescription :city-name="cityInfo.city"/>
            </div>
          </transition-expand>
        </td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
  * {
    will-change: height;
    transform: translateZ(0);
    backface-visibility: hidden;
    perspective: 1000px;
  }
</style>

<script>
    import axios from 'axios'
    import CityDescription from './CityDescription.vue'
    import TransitionExpand from './TransitionExpand.vue'

    export default {
        name: 'PollutedCities',

        components: {
            CityDescription,
            TransitionExpand
        },

        data() {
            return {
                results: [],
            };
        },

        methods: {
            getPollutedData: function (countryCode) {
                if (!countryCode) {
                    // eslint-disable-next-line no-console
                    console.error('No country code specified');
                    return;
                }

                axios.get('https://api.openaq.org/v1/cities', {
                    params: {
                        country: countryCode,
                        limit: 10,
                        order_by: 'count',
                        sort: 'desc'
                    }
                })
                    .then(response => {
                        this.results = response.data.results
                    })
                    .then(() => {
                        this.initLogicVars()
                    })
                    .catch(e => {
                        this.errors.push(e)
                    })
            },
            initLogicVars() {
                this.results.map(element => this.$set(element, 'showDescription', false))
            },
            manageDescription: function (cityInfo) {
                if (!cityInfo.showDescription) {
                    this.$set(cityInfo, 'showDescription', true);
                    this.$root.$emit('CityDescription')
                } else {
                    this.$set(cityInfo, 'showDescription', false)
                }
            }
        },

        mounted() {
            this.$root.$on('PollutedCities', (countryCode) => {
                this.getPollutedData(countryCode);
            })
        }
    };
</script>
