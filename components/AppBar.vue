<template>
  <v-app-bar flat fixed app
    ><v-btn v-if="returnBtn" depressed small fab @click="goBack" class="mr-4"
      ><v-icon>mdi-arrow-left</v-icon></v-btn
    ><slot></slot>
  </v-app-bar>
</template>
<script>
export default {
  props: {
    returnBtn: { type: Boolean, default: false },
  },
  methods: {
    goBack() {
      window?.history.back();
    },
  },
};
</script>
