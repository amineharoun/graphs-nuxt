<template>
  <v-dialog :value="value" @input="(v) => $emit('input', v)" max-width="500px">
    <v-card>
      <v-card-text class="text-center pt-5">
        <v-icon ref="icon" color="red" size="80"> mdi-delete-alert </v-icon>
      </v-card-text>
      <v-card-text class="text-h6 text-center justify-center">
        Êtes-vous sûr de vouloir supprimer ?
      </v-card-text>

      <v-card-actions>
        <v-btn @click="cancel" color="background" class="mr-2" depressed
          >Annuler</v-btn
        >
        <v-spacer />
        <v-btn @click="save" color="green" class="white--text" depressed
          >Confirmer</v-btn
        >
      </v-card-actions>
    </v-card></v-dialog
  >
</template>
<script>
export default {
  props: {
    value: { type: Boolean, default: false },
  },

  methods: {
    save() {
      this.$emit("confirm");

      this.$emit("input", false);
    },
    cancel() {
      this.$emit("input", false);
    },
  },
};
</script>
