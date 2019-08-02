<template>
    <div id="app">

        <Main
            :here-keys="hereKeys"
        />

        <!--<Map
            appId="Rf3NJEjBfi5X8qK551df"
            appCode="QDk1ZECXprsqiwQRFFUTsA"
            lat="37.7397"
            lng="-121.4252"
            width="100%"
            height="835px" /> -->
    </div>
</template>

<script>

    import Map from './components/elements/Map.vue'
    import Search from './components/elements/Search.vue'
    import Main from './components/Main.vue'

    export default {
        name: 'app',
        components: {
            Map,
            Search,
            Main
        },
        data() {
            return {
                hereKeys: {
                    apiKey: 'brm9oXsSTBFbLRv1Y-so7W8Q1TNs8dH0EeXvDEHuRcw',
                    appId: 'Rf3NJEjBfi5X8qK551df',
                    appCode: 'QDk1ZECXprsqiwQRFFUTsA',
                },
            }
        },
        created() {
        },
        methods: {

        }
    }
</script>

<style>

</style>
