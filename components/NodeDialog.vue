<template>
  <v-dialog :value="value" @input="(v) => $emit('input', v)" max-width="500px">
    <v-card>
      <v-card-title>Ajouter un noeud</v-card-title>
      <v-card-text>
        <v-text-field
          outlined
          v-model="node.tooltip"
          label="Tooltip"
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
    </v-card></v-dialog
  >
</template>
<script>
export default {
  props: {
    value: { type: Boolean, default: false },
    nodes: { type: Array, default: [] },
  },
  data() {
    return {
      node: {
        id: null,
        tooltip: null,
      },
    };
  },
  watch: {
    value(v) {
      if (v) {
        this.node.id = null;
        this.node.tooltip = null;
      }
    },
  },
  methods: {
    save() {
      const maxId = this.nodes.reduce((max, objet) => {
        return objet.id > max ? objet.id : max;
      }, 0);

      this.node.id = maxId + 1;

      this.$emit("addNode", this.node);

      this.$emit("input", false);
    },
    cancel() {
      this.$emit("input", false);
    },
  },
};
</script>
