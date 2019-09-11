<template>
  <form class="autocomplete" @submit.prevent="submitForm" autocomplete="off">
    <label for="country-name" class="h1">Check the most pulluted places</label>

    <div class="search-bar">
      <input type="text" id="country-name" @input="onChange" v-model="search" @keydown.down="onArrowDown"
             @keydown.up="onArrowUp" @keydown.enter="onEnter" @click="handleOnClick" placeholder=" " required>
      <label for="country-name">Enter the country</label>

      <div class="requirements" v-if="results.length === 0">No country listed. Rename it.</div>

      <ul id="autocomplete-results" v-show="isOpen" class="autocomplete-results">
        <li class="loading" v-if="isLoading">
          Loading results...
        </li>
        <li v-else v-for="(result, i) in results" :key="i" @click="setResult(result)" class="autocomplete-result"
            :class="{ 'is-active': i === arrowCounter }">
          {{ result }}
        </li>
      </ul>
    </div>

    <button type="submit" class="center-block">Show most polluted cities</button>
  </form>
</template>

<script>
    export default {
        name: 'Autocomplete',

        props: {
            items: {
                type: Array,
                required: false,
                default: () => [],
            },
            isAsync: {
                type: Boolean,
                required: false,
                default: false,
            }
        },

        data() {
            return {
                isOpen: false,
                results: [],
                search: '',
                isLoading: false,
                arrowCounter: 0,
                isoCountries: {
                    'Poland': 'PL',
                    'Germany': 'DE',
                    'Spain': 'ES',
                    'France': 'FR',
                }
            };
        },

        methods: {
            onChange() {
                this.$emit('input', this.search);

                // Handle ajax request if needed
                if (this.isAsync) {
                    this.isLoading = true;
                } else {
                    this.filterResults();
                    this.isOpen = true;
                }

                this.updateCustomValidity();
            },

            updateCustomValidity() {
                let input = [...this.$el.elements].find(element => element.tagName.toLowerCase() === 'input');

                if (this.results.length === 0) {
                    input.setCustomValidity("Please enter a valid country name.");
                    return;
                }

                let resultsLowerCase = this.results.map(v => v.toLowerCase());

                if (!resultsLowerCase.includes(this.search.toLowerCase())) {
                    input.setCustomValidity("Please enter a valid country name.");
                } else {
                    input.setCustomValidity("");
                    this.isOpen = false;
                }
            },
            filterResults: function() {
                // first uncapitalize all the things
                this.results = this.items.filter((item) => {
                    this.search = this.search || '';

                    return item.toLowerCase().indexOf(this.search.toLowerCase()) > -1;
                });
            },
            setResult(result) {
                this.search = result;
                this.isOpen = false;
                this.onChange();
            },
            onArrowDown() {
                if (this.arrowCounter < this.results.length) {
                    this.arrowCounter = this.arrowCounter + 1;
                }
            },
            onArrowUp() {
                if (this.arrowCounter > 0) {
                    this.arrowCounter = this.arrowCounter - 1;
                }
            },
            onEnter() {
                this.search = this.results[this.arrowCounter];
                this.isOpen = false;
                this.onChange();
            },
            handleClickOutside(evt) {
                if (!this.$el.contains(evt.target)) {
                    this.isOpen = false;
                    this.arrowCounter = -1;

                    this.updateCustomValidity();
                }
            },
            handleOnClick() {
                this.filterResults();
                this.isOpen = true;
            },
            submitForm: function() {
                let countryCode = this.getCountryIsoCode(this.search);

                this.$root.$emit('PollutedCities', countryCode);
            },
            getCountryIsoCode(countryName) {
                if (this.isoCountries.hasOwnProperty(countryName)) {
                    return this.isoCountries[countryName];
                } else {
                    return countryName;
                }
            }
        },
        watch: {
            items: function (val, oldValue) {
                // actually compare them
                if (val.length !== oldValue.length) {
                    this.results = val;
                    this.isLoading = false;
                }
            },
        },
        mounted() {
            document.addEventListener('click', this.handleClickOutside)
        },
        destroyed() {
            document.removeEventListener('click', this.handleClickOutside)
        }
    };
</script>
