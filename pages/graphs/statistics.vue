<template>
  <v-container>
    <AppBar returnBtn>
      <v-toolbar-title>Statistiques du graphe</v-toolbar-title>
      <v-spacer />

      <v-btn
        outlined
        color="blue"
        :to="{ name: 'graph-edit', params: { id: $route.params.id } }"
        ><v-icon left>mdi-pencil</v-icon> Editer le graphe</v-btn
      >
    </AppBar>

    <v-data-table
      :headers="headers"
      :items="nodes"
      hide-default-footer
      :items-per-page="20"
    >
      <template v-slot:[`item.id`]="{ item }">
        {{ item.id }}
      </template>
      <template v-slot:[`item.tooltip`]="{ item }">
        {{ item.tooltip }}
      </template>
      <template v-slot:[`item.neighbors`]="{ item }">
        {{ item.neighbors }}
      </template>
    </v-data-table>
  </v-container>
</template>

<script>
export default {
  head() {
    return {
      title: "Statistiques du graphe",
    };
  },
  data() {
    return {
      headers: [
        { text: "Node ID", value: "id" },
        { text: "Node tooltip", value: "tooltip" },
        { text: "Node neighbors", value: "neighbors" },
      ],
      nodes: [],
    };
  },

  mounted() {
    const graphsLocalStorage = localStorage.getItem("graphs");
    const graphsParsed = graphsLocalStorage
      ? JSON.parse(graphsLocalStorage)
      : [];
    let currentGraph = graphsParsed.find((e) => e.id == this.$route.params.id);

    currentGraph.nodes?.forEach((node) => {
      let neighbors = [];
      currentGraph.relations.forEach((r) => {
        if (r.start == node.id) {
          neighbors.push(r.end);
        }
        if (r.end == node.id) {
          neighbors.push(r.start);
        }
      });
      node.neighbors = neighbors.join(", ");
    });
    this.nodes = currentGraph.nodes || [];
  },
};
</script>
