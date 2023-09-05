<template>
  <v-container>
    <v-app-bar flat fixed app>
      <v-btn depressed small fab to="/" class="mr-4"
        ><v-icon>mdi-arrow-left</v-icon></v-btn
      >
      <v-toolbar-title>Vue du Graphe #{{ $route.params.id }}</v-toolbar-title>
      <v-spacer />
      <v-btn
        outlined
        color="blue"
        :to="{ name: 'graph-edit', params: { id: $route.params.id } }"
        class="mr-4"
        ><v-icon left>mdi-pencil</v-icon> Editer le graphe</v-btn
      >
      <v-btn
        outlined
        color="blue"
        :to="{ name: 'graph-stats', params: { id: $route.params.id } }"
        ><v-icon left>mdi-chart-bar</v-icon> Voir les stats</v-btn
      >
    </v-app-bar>

    <GraphView v-model="graph" :id="$route.params.id" />
  </v-container>
</template>

<script>
export default {
  head() {
    return {
      title: "Vue du graphe " + this.$route.params.id,
    };
  },
  data() {
    return {
      graph: {
        nodes: [],
        relations: [],
      },
    };
  },
};
</script>
