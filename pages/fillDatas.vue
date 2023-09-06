<template>
  <v-container>
    <AppBar returnBtn>
      <v-toolbar-title>Remplissage dans local storage</v-toolbar-title>
      <v-spacer />
    </AppBar>

    <div>Cette page est dédiée à remplir votre local storage de graphes</div>
  </v-container>
</template>

<script>
export default {
  mounted() {
    const graphs = [
      {
        id: 1,
        name: "John Doe",
        description: "Data analyst",
        created_at: "2021-12-28 11:57:53",
        updated_at: "2022-06-15 21:08:44",
        nodes: [
          {
            id: 1,
            tooltip: "DNA",
            left: 375,
            top: 211,
          },
          {
            id: 2,
            tooltip: "Cell",
            left: 850,
            top: 260,
          },
          {
            id: 3,
            tooltip: "Ecosynthesis",
            left: 133,
            top: 438,
          },
          {
            id: 4,
            tooltip: "Micro  organism",
            left: 450,
            top: 547,
          },
          {
            id: 5,
            tooltip: "Biodiversity",
            left: 558,
            top: 412,
          },
        ],
        relations: [
          {
            id: 1,
            start: 1,
            end: 2,
          },
          {
            id: 2,
            start: 2,
            end: 3,
          },
          {
            id: 3,
            start: 1,
            end: 3,
          },
          {
            id: 4,
            start: 3,
            end: 4,
          },
          {
            id: 5,
            start: 4,
            end: 5,
          },
        ],
      },
      {
        id: 2,
        name: "Jane Smith",
        description: "Software developer",
        created_at: "2021-06-18 14:23:51",
        updated_at: "2022-04-09 09:12:37",
        nodes: [
          { id: 1, tooltip: "Physiology", left: 375, top: 211 },
          { id: 2, tooltip: "Physiology", left: 850, top: 260 },
          { id: 3, tooltip: "Enzyme", left: 550, top: 460 },
        ],
        relations: [
          { id: 1, start: 1, end: 2 },
          { id: 2, start: 2, end: 3 },
        ],
      },
      {
        id: 3,
        name: "Isaac Clark",
        description: "Geneticist",
        created_at: "2021-09-30 16:45:29",
        updated_at: "2022-01-22 08:36:10",
      },
    ];
    localStorage.setItem("graphs", JSON.stringify(graphs));
  },
};
</script>
