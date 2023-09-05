<template>
  <div class="graph-wrapper">
    <div v-if="graph.nodes.length == 0" class="graphs-empty">
      Aucun noeud pour ce graphe
    </div>
    <div
      v-for="item in graph.relations"
      :key="'relation' + item.id"
      class="line"
      :id="'line' + item.id"
      @click="toggleSelectedRelation(item.id)"
    >
      <div class="line-delete" v-if="item.selected">
        <v-icon color="red" @click="deleteRelation(item.id)">
          mdi-close-circle-outline
        </v-icon>
      </div>
    </div>

    <div
      v-for="item in graph.nodes"
      :key="'nodes' + item.id"
      class="draggable"
      :style="{
        transform: 'translate(' + item.left + 'px, ' + item.top + 'px)',
      }"
      :data-x="item.left"
      :data-y="item.top"
      :id="'node-' + item.id"
    >
      <div class="node-element" @dblclick="toggleSelectedNode(item.id)">
        <v-tooltip attach content-class="node-tooltip">
          <template v-slot:activator="{ on, attrs }"
            ><div class="node-text" v-bind="attrs" v-on="on">
              {{ item.id }}
            </div></template
          >
          <span>{{ item.tooltip }}</span>
        </v-tooltip>
      </div>

      <div class="node-delete" v-if="item.selected">
        <v-icon color="red" @click="deleteNode(item.id)">
          mdi-close-circle-outline
        </v-icon>
      </div>
    </div>
  </div>
</template>

<script>
import interact from "interactjs";

export default {
  props: {
    id: { type: [Number, String], default: null },
    editable: { type: Boolean, default: false },
    value: { type: Object, default: null },
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

  mounted() {
    const graphsLocalStorage = localStorage.getItem("graphs");
    const graphsParsed = graphsLocalStorage
      ? JSON.parse(graphsLocalStorage)
      : [];
    let currentGraph = graphsParsed.find((e) => e.id == this.id);
    this.graph.nodes = currentGraph.nodes || [];
    this.graph.relations = currentGraph.relations || [];

    this.graph.nodes = this.graph.nodes.map((r) => ({ ...r, selected: false }));
    this.graph.relations = this.graph.relations.map((r) => ({
      ...r,
      selected: false,
    }));

    this.graph.relations.forEach((r) => {
      this.generateCoordinate(r.id);
    });

    if (this.editable) {
      interact(".draggable").draggable({
        // enable inertial throwing
        inertia: true,
        // keep the element within the area of it's parent
        modifiers: [
          interact.modifiers.restrictRect({
            restriction: "parent",
            endOnly: true,
          }),
        ],
        // enable autoScroll
        autoScroll: true,

        listeners: {
          // call this function on every dragmove event
          move: this.dragMoveListener,
        },
      });
    }
  },

  methods: {
    toggleSelectedRelation(id) {
      if (this.editable) {
        this.graph.relations.forEach((e) => {
          e.selected = e.id == id ? !e.selected : false;
        });
      }
    },
    toggleSelectedNode(id) {
      if (this.editable) {
        this.graph.nodes.forEach((e) => {
          e.selected = e.id == id ? !e.selected : false;
        });
      }
    },

    dragMoveListener(event) {
      let target = event.target;
      // keep the dragged position in the data-x/data-y attributes
      let x = (parseFloat(target.getAttribute("data-x")) || 0) + event.dx;
      let y = (parseFloat(target.getAttribute("data-y")) || 0) + event.dy;

      // translate the element
      target.style.transform = "translate(" + x + "px, " + y + "px)";

      // update the posiion attributes
      target.setAttribute("data-x", x);
      target.setAttribute("data-y", y);

      // update in vue element
      let elementId = target.id.replace("node-", "");
      this.graph.nodes.forEach((element) => {
        if (element.id == elementId) {
          element.left = x;
          element.top = y;
        }
      });

      //update relations coordinates
      this.graph.relations.forEach((relation) => {
        if (relation.start == elementId || relation.end == elementId) {
          this.generateCoordinate(relation.id);
        }
      });
    },
    generateCoordinate(id) {
      setTimeout(() => {
        let relation = this.graph.relations.find((e) => e.id == id);

        let startNode = this.graph.nodes.find((e) => e.id == relation.start);
        let endNode = this.graph.nodes.find((e) => e.id == relation.end);
        const startX = startNode.left;
        const startY = startNode.top;
        const endX = endNode.left;
        const endY = endNode.top;

        // Calculez la longueur de la ligne
        const length = Math.sqrt((endX - startX) ** 2 + (endY - startY) ** 2);

        // Calculez l'angle de la ligne
        const angle = Math.atan2(endY - startY, endX - startX);

        // Récupérez l'élément de ligne
        const lineElement = document.getElementById("line" + id);

        // Appliquez les propriétés CSS pour la ligne
        lineElement.style.left = startX + "px";
        lineElement.style.top = startY + "px";
        lineElement.style.width = length + "px";

        lineElement.style.transform = `rotate(${angle}rad)`;
      }, 100);
    },
    deleteRelation(id) {
      this.graph.relations = this.graph.relations.filter((e) => e.id != id);
    },
    deleteNode(id) {
      this.graph.nodes = this.graph.nodes.filter((e) => e.id != id);

      //delete relations of the nodes
      this.graph.relations = this.graph.relations.filter(
        (e) => e.start != id && e.end != id
      );
    },
  },
};
</script>

<style>
.draggable {
  width: 40px;
  height: 40px;
  margin: -16px 0 0 -19px;
  background-color: #29e;
  color: white;
  touch-action: none;
  user-select: none;
  transform: translate(0px, 0px);
  object-fit: cover;
  position: absolute;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.line {
  position: absolute;
  border-top: 5px solid #29e;
  transform-origin: 0% 0%;
  cursor: pointer;
  text-align: center;
  transition: 0.2s border;
}

.line:hover {
  border-top-color: #2584c7;
}
.graph-wrapper {
  position: relative;
  height: calc(100vh - 64px);
}

.node-element {
  position: relative;
  width: 100%;
  height: 100%;
  text-align: center;
  display: flex;
  justify-content: center;
}
.node-text {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
}
.node-tooltip {
  left: auto !important;
  top: auto !important;
  margin: auto;
  width: max-content;
  position: static;
  transform: translateY(-34px);
}

.node-delete {
  position: absolute;
  bottom: -28px;
}
</style>
