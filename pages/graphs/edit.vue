<template>
  <v-container>
    <v-app-bar flat fixed app>
      <v-btn
        depressed
        small
        fab
        :to="`/graphs/${$route.params.id}`"
        class="mr-4"
        ><v-icon>mdi-arrow-left</v-icon></v-btn
      >
      <v-toolbar-title
        >Edition du graphe #{{ $route.params.id }}</v-toolbar-title
      >
      <v-spacer />
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
    </v-app-bar>

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
  </v-container>
</template>

<script>
export default {
  head() {
    return {
      title: "Edition du graphe " + this.$route.params.id,
    };
  },
  data() {
    return {
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
    },
  },
};
</script>
