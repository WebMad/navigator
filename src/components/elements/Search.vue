<template>
    <div class="search">
        <input v-model="searchText" ref="search" type="text">
        <ul v-if="searchText" class="search-results">
            <li @click="setPlace(result)" v-for="result in resultsSearch" class="search-result">{{
                result.Location.Address.Label }}
            </li>
        </ul>
    </div>
</template>

<script>
    import {debounce} from "debounce"; //для ограничения по времени вызова функции запроса к api

    export default {
        name: "Search",
        props: {
            hereKeys: Object,
        },
        data() {
            return {
                searchText: '',
                resultsSearch: Object,
                currentObject: Object,
                platform: Object,
                isSearch: true,
            }
        },
        methods: {
            searchPlace() {
                if (!this.isSearch) {
                    this.isSearch = true;
                    return;
                }
                if (this.searchText !== '') {
                    this.geocoder.geocode({
                        searchText: this.searchText,
                    }, (data) => {

                        console.log(data);
                        if (data.Response.View.length > 0) {
                            this.resultsSearch = data.Response.View[0].Result;
                        }

                    }, function (e) {
                        alert(e);
                    });
                } else {
                    this.resultsSearch = {};
                }
            },

            setPlace(place) {

                this.currentObject = place;

                this.$emit('selectedPlace', place);

                this.searchText = place.Location.Address.Label;
                this.resultsSearch = {};

                this.isSearch = false;

            },
        },
        watch: {
            searchText: function (newData, oldData) {
                this.debouncedGetSearchResults();
            }
        },
        created() {
            this.platform = new H.service.Platform({
                "apikey": this.hereKeys.apiKey,
                "app_id": this.hereKeys.appId,
                "app_code": this.hereKeys.appCode
            });

            this.debouncedGetSearchResults = debounce(this.searchPlace, 500)
        },
        mounted() {
            console.log(this.platform);
            this.geocoder = this.platform.getGeocodingService();
        }
    }
</script>

<style scoped>
    ul.search-results {
        position: absolute;
    }
</style>