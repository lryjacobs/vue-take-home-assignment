<template>
  <div ref="myDraggable" class="draggable"></div>
</template>

<script>
import interact from "interactjs";

export default {
  data() {
    return {
      screenX: 0,
      screenY: 0,
    };
  },
  mounted: function() {
    let myDraggable = this.$refs.myDraggable;
    this.initInteract(myDraggable);
  },
  methods: {
    initInteract: function(selector) {
      interact(selector)
        .draggable({
          // enable inertial throwing
          inertia: true,
          // keep the element within the area of it's parent
          restrict: {
            restriction: "parent",
            endOnly: true,
            elementRect: { top: 0, left: 0, bottom: 1, right: 1 },
          },
          // enable autoScroll
          autoScroll: true,

          // call this function on every dragmove event
          onmove: this.dragMoveListener,
          // call this function on every dragend event
          onend: this.onDragEnd,

          modifiers: [
            interact.modifiers.snap({
              targets: [interact.createSnapGrid({ x: 50, y: 50 })],
              range: Infinity,
              relativePoints: [{ x: 0, y: 0 }],
            }),
            interact.modifiers.restrict({
              restriction: selector.parentNode,
              elementRect: { top: 0, left: 0, bottom: 1, right: 1 },
              endOnly: true,
            }),
          ],
        })
        .resizable({
          edges: { left: true, right: true, bottom: true, top: true },
          modifiers: [
            interact.modifiers.snap({
              targets: [interact.createSnapGrid({ x: 50, y: 50 })],
              range: Infinity,
              relativePoints: [{ x: 0, y: 0 }],
            }),
            interact.modifiers.restrict({
              restriction: selector.parentNode,
              elementRect: { top: 0, left: 0, bottom: 1, right: 1 },
              endOnly: true,
            }),
          ],
          listeners: {
            move(event) {
              var target = event.target;
              var x = parseFloat(target.getAttribute("data-x")) || 0;
              var y = parseFloat(target.getAttribute("data-y")) || 0;

              // update the element's style
              target.style.width = event.rect.width + "px";
              target.style.height = event.rect.height + "px";

              // translate when resizing from top or left edges
              x += event.deltaRect.left;
              y += event.deltaRect.top;

              target.style.webkitTransform = target.style.transform =
                "translate(" + x + "px," + y + "px)";

              target.setAttribute("data-x", x);
              target.setAttribute("data-y", y);
              // target.textContent =
              //   Math.round(event.rect.width) +
              //   "\u00D7" +
              //   Math.round(event.rect.height);
            },
          },
        });
    },
    dragMoveListener: function(event) {
      var target = event.target,
        // keep the dragged position in the data-x/data-y attributes
        x =
          (parseFloat(target.getAttribute("data-x")) || this.screenX) +
          event.dx,
        y =
          (parseFloat(target.getAttribute("data-y")) || this.screenY) +
          event.dy;

      // translate the element
      target.style.webkitTransform = target.style.transform =
        "translate(" + x + "px, " + y + "px)";

      // update the posiion attributes
      target.setAttribute("data-x", x);
      target.setAttribute("data-y", y);
    },
    onDragEnd: function(event) {
      var target = event.target;
      // update the state
      this.screenX = target.getBoundingClientRect().left;
      this.screenY = target.getBoundingClientRect().top;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.draggable {
  width: 100px;
  height: 100px;
  background-color: lightgray;
  color: #fff;
  padding: 5px;
  position: absolute;
}
</style>
