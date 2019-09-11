<template>
  <div v-if="!(Object.keys(cityDescription).length === 0 && cityDescription.constructor === Object)">
    <p>{{ cityDescription.extract || 'none :(' }}</p>
  </div>
</template>


<script>
    import axios from 'axios'

    export default {
        name: 'CityDescription',

        props: {
            cityName: {
                type: String,
                required: true
            }
        },

        data() {
            return {
                cityDescription: {}
            };
        },

        created() {
            axios.get('https://en.wikipedia.org/w/api.php', {
                params: {
                    origin: '*',
                    action: 'query',
                    prop: 'extracts',
                    exintro: '',
                    explaintext: '',
                    format: 'json',
                    redirects: '',
                    titles: this.cityName
                }
            })
                .then(response => {
                    // JSON responses are automatically parsed.
                    this.cityDescription = Object.values(response.data.query.pages)[0];
                })
                .catch(e => {
                    this.errors.push(e)
                })
        }
    };
</script>
