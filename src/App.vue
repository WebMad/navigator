<template>
    <div id="app">
        <Map
            appId="Rf3NJEjBfi5X8qK551df"
            appCode="QDk1ZECXprsqiwQRFFUTsA"
            lat="37.7397"
            lng="-121.4252"
            width="100%"
            height="835px" />
    </div>
</template>

<script>
    import Map from './components/Map.vue'

    export default {
        name: 'app',
        components: {
            Map
        }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
</style>
