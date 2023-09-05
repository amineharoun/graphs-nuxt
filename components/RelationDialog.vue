<template>
  <v-dialog :value="value" @input="(v) => $emit('input', v)" max-width="500px">
    <v-card>
      <v-card-title>Ajouter une relation</v-card-title>
      <v-card-text>
        <v-select
          v-model="relation.startNode"
          :items="nodes"
          label="Noeud de départ"
          item-text="tooltip"
          item-value="id"
          outlined
          dense
        ></v-select>
        <v-select
          v-model="relation.endNode"
          :items="nodes"
          label="Noeud d'arrivé"
          item-text="tooltip"
          item-value="id"
          outlined
          dense
        ></v-select>
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
    nodes: Array,
    relations: Array,
  },
  data() {
    return {
      relation: {
        id: null,
        startNode: null,
        endNode: null,
      },
    };
  },
  watch: {
    value(v) {
      if (v) {
        this.relation.id = null;
        this.relation.startNode = null;
        this.relation.endNode = null;
      }
    },
  },
  methods: {
    save() {
      const maxId = this.relations.reduce((max, objet) => {
        return objet.id > max ? objet.id : max;
      }, 0);

      this.relation.id = maxId + 1;
      this.$emit("addRelation", this.relation);
      this.$emit("input", false);
    },
    cancel() {
      this.$emit("input", false);
    },
  },
};
</script>
