<template>
<div id="header">
    <v-header color="indigo lighten-5" class="pa-3">
        <v-toolbar-items>
            <v-btn flat href='/producerSearch'>공연 기획자/제작자</v-btn>
            <v-btn flat href='/theater-manager'>극장 관계자</v-btn>
        </v-toolbar-items>
        <v-spacer></v-spacer>
    </v-header>
</div>
</template>

<script>
export default {
    name: "Header",
}
</script>

<style>
#header {
    margin-left: 10%;
    margin-right: 10%;

    background-color: #F5695F;

    margin-top: 50px;

    margin-bottom: -50px;
    z-index: 7;
}

</style>
