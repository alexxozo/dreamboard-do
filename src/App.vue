<template>
    <v-app style="background: #06001f !important;">
        <v-main class="py-5">
            <Dreamboard/>
        </v-main>
    </v-app>
</template>

<script>
    import Dreamboard from "./components/Dreamboard";

    export default {
        name: 'App',

        components: {
            Dreamboard,
        },

        data: () => ({
            //
        }),
    };
</script>
