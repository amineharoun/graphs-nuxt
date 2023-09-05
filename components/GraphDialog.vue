<template>
  <v-card>
    <v-card-title>{{
      graph.id ? "Edition du graphe" : "Ajout d'un graphe"
    }}</v-card-title>

    <form @submit.prevent="submit">
      <v-card-text>
        <v-text-field
          outlined
          v-model="graph.name"
          label="Name"
          autocomplete="off"
        ></v-text-field>
        <v-text-field
          outlined
          v-model="graph.description"
          label="Description"
          autocomplete="off"
        ></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-spacer />
        <v-btn @click="cancel" color="background" class="mr-2" depressed
          >Annuler</v-btn
        >
        <v-btn @click="save" color="green" class="white--text" depressed
          >Enregistrer</v-btn
        >
      </v-card-actions>
    </form>
  </v-card>
</template>
<script>
import { v4 as uuidv4 } from "uuid";

export default {
  props: {
    value: { type: Object, default: null },

    graphDialog: {
      type: Boolean,
      default: true,
    },
  },

  computed: {
    graph: {
      get() {
        return this.value;
      },
      set(v) {
        this.$emit("input", v);
      },
    },
  },

  methods: {
    save() {
      const currentDate = new Date();
      const formattedDate = currentDate
        .toISOString()
        .slice(0, 19)
        .replace("T", " ");
      if (this.graph.id) {
        // edit a graph

        const graphesLocalStorage =
          JSON.parse(localStorage.getItem("graphs")) || [];
        graphesLocalStorage.forEach((e) => {
          if (e.id == this.graph.id) {
            e.updated_at = formattedDate;
            e.name = this.graph.name;
            e.description = this.graph.description;
          }
        });
        localStorage.setItem("graphs", JSON.stringify(graphesLocalStorage));
      } else {
        // add new graph

        this.graph.id = uuidv4();
        this.graph.created_at = formattedDate;
        this.graph.updated_at = formattedDate;

        const graphesLocalStorage =
          JSON.parse(localStorage.getItem("graphs")) || [];
        graphesLocalStorage.push(this.graph);
        localStorage.setItem("graphs", JSON.stringify(graphesLocalStorage));
      }

      this.$emit("refetch");
      this.$emit("close");
    },
    cancel() {
      this.$emit("close");
    },
  },
};
</script>
