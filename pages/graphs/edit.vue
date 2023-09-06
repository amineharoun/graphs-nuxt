<template>
  <v-container>
    <AppBar returnBtn>
      <v-toolbar-title>Edition du graphe</v-toolbar-title>
      <v-spacer />
      <v-btn
        class="mr-4"
        outlined
        color="blue"
        :to="{ name: 'graph-stats', params: { id: $route.params.id } }"
        ><v-icon left>mdi-chart-bar</v-icon> Voir les stats</v-btn
      >
      <v-btn outlined color="blue" @click="nodeDialog = true" class="mr-4"
        ><v-icon left>mdi-plus</v-icon> Ajouter un noeud</v-btn
      >
      <v-btn outlined color="blue" @click="relationDialog = true" class="mr-4"
        ><v-icon left>mdi-arrow-top-left-bottom-right</v-icon> Ajouter une
        relation</v-btn
      >
      <v-btn outlined color="green" @click="save"
        ><v-icon left>mdi-content-save-check</v-icon> Enregistrer</v-btn
      >
    </AppBar>

    <GraphView
      editable
      v-model="graph"
      :id="$route.params.id"
      ref="graphViewRef"
    />

    <relationDialog
      v-model="relationDialog"
      :nodes="graph.nodes"
      :relations="graph.relations"
      @addRelation="addRelation"
    />

    <nodeDialog v-model="nodeDialog" @addNode="addNode" :nodes="graph.nodes" />
    <v-snackbar color="success" v-model="snackbar" :timeout="timeout">
      Graphe sauvegardé avec succès !
    </v-snackbar>
  </v-container>
</template>

<script>
export default {
  head() {
    return {
      title: "Edition du graphe",
    };
  },
  data() {
    return {
      snackbar: false,
      timeout: 3000,
      relationDialog: false,
      nodeDialog: false,
      graph: {
        nodes: [],
        relations: [],
      },
    };
  },
  methods: {
    addNode(node) {
      this.graph.nodes.push({
        id: node.id,
        tooltip: node.tooltip,
        left: 20,
        top: 20,
        selected: false,
      });
    },

    addRelation(relation) {
      this.graph.relations.push({
        id: relation.id,
        start: relation.startNode,
        end: relation.endNode,
        selected: false,
      });
      this.$refs.graphViewRef.generateCoordinate(relation.id);
    },
    save() {
      const graphesLocalStorage =
        JSON.parse(localStorage.getItem("graphs")) || [];
      graphesLocalStorage.forEach((e) => {
        if (e.id == this.$route.params.id) {
          e.nodes = this.graph.nodes;
          e.relations = this.graph.relations;

          const nodesSanitized = e.nodes.map((obj) => {
            const { selected, ...rest } = obj;
            return rest;
          });

          const relationsSanitized = e.relations.map((obj) => {
            const { selected, ...rest } = obj;
            return rest;
          });

          e.nodes = nodesSanitized;
          e.relations = relationsSanitized;

          localStorage.setItem("graphs", JSON.stringify(graphesLocalStorage));
        }
      });

      this.snackbar = true;
    },
  },
};
</script>
