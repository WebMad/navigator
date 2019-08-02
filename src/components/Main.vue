<template>
    <div class="main">
        <Search
            :here-keys="hereKeys"
            @selectedPlace="setPlaceOnMap"
        />
        <Map
            appId="Rf3NJEjBfi5X8qK551df"
            appCode="QDk1ZECXprsqiwQRFFUTsA"

        />
    </div>
</template>

<script>
    import Search from '@/components/elements/Search.vue'
    import Map from '@/components/elements/Map.vue'

    export default {
        name: "Main",
        components: {
            Search,
            Map,
        },
        props: {
            hereKeys: Object,
        },
        methods: {
            setPlaceOnMap (place) {
                console.log(place);
            }
        },
        mounted() {

        }
    }
</script>

<style scoped>

</style>