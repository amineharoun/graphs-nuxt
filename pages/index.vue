<template>
  <v-container>
    <v-app-bar flat fixed app>
      <v-toolbar-title>Liste des graphes</v-toolbar-title>
      <v-spacer />
      <v-btn outlined color="blue" to="/fillDatas" class="mr-4"
        ><v-icon left>mdi-restore</v-icon> Reset LocalStorage</v-btn
      >
      <v-btn outlined color="blue" @click="addGraph"
        ><v-icon left>mdi-plus</v-icon> Ajouter un graphe</v-btn
      >
    </v-app-bar>
    <v-data-table
      :headers="headers"
      :items="items"
      hide-default-footer
      :items-per-page="20"
    >
      <template v-slot:[`item.name`]="{ item }">
        {{ item.name }}
      </template>
      <template v-slot:[`item.description`]="{ item }">
        {{ item.description }}
      </template>
      <template v-slot:[`item.created_at`]="{ item }">
        {{ item.created_at }}
      </template>
      <template v-slot:[`item.updated_at`]="{ item }">
        {{ item.updated_at }}
      </template>
      <template v-slot:[`item.actions`]="{ item }">
        <v-tooltip bottom>
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              v-bind="attrs"
              v-on="on"
              color="blue"
              icon
              :to="`/graphs/${item.id}`"
              ><v-icon>mdi-eye</v-icon></v-btn
            ></template
          ><span>Voir</span>
        </v-tooltip>

        <v-tooltip bottom>
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              v-bind="attrs"
              v-on="on"
              color="green"
              icon
              @click="editGraph(item)"
            >
              <v-icon>mdi-pencil</v-icon>
            </v-btn> </template
          ><span>Editer</span>
        </v-tooltip>

        <v-tooltip bottom>
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              v-bind="attrs"
              v-on="on"
              color="red"
              icon
              @click="confirmDeleteGraph(item.id)"
            >
              <v-icon>mdi-delete</v-icon>
            </v-btn> </template
          ><span>Supprimer</span>
        </v-tooltip>
      </template>
    </v-data-table>
    <v-dialog v-model="graphDialog" max-width="500px">
      <graphDialog
        @refetch="loadGraphs"
        @close="graphDialog = false"
        v-model="graph"
        :graphDialog="graphDialog"
      />
    </v-dialog>

    <confirmDialog v-model="deleteDialog" @confirm="deleteGraph" />
  </v-container>
</template>

<script>
export default {
  head() {
    return {
      title: "Liste des graphes",
    };
  },
  data() {
    return {
      headers: [
        { text: "Name", value: "name" },
        { text: "Description", value: "description" },
        { text: "Created at", value: "created_at" },
        { text: "Updated at", value: "updated_at" },
        { text: "Actions", value: "actions" },
      ],
      items: [],
      graphDialog: false,
      deleteDialog: false,
      selectedGraphToDelete: null,
      graph: {
        id: null,
        name: null,
        description: null,
        created_at: null,
        updated_at: null,
        new: true,
      },
    };
  },

  mounted() {
    this.loadGraphs();
  },

  methods: {
    loadGraphs() {
      const graphsLocalStorage = localStorage.getItem("graphs");
      this.items = graphsLocalStorage ? JSON.parse(graphsLocalStorage) : [];
    },
    editGraph(item) {
      this.graph.name = item.name;
      this.graph.id = item.id;
      this.graph.description = item.description;
      this.graphDialog = true;
    },
    addGraph() {
      this.graph.name = null;
      this.graph.id = null;
      this.graph.description = null;
      this.graphDialog = true;
    },
    confirmDeleteGraph(id) {
      this.deleteDialog = true;
      this.selectedGraphToDelete = id;
    },
    deleteGraph() {
      this.items = this.items.filter((e) => e.id != this.selectedGraphToDelete);
      localStorage.setItem("graphs", JSON.stringify(this.items));
    },
  },
};
</script>
