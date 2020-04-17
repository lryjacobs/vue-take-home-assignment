<template>
  <div class="grid-container">
    <div v-for="index in 10" :key="index" :class="`row row-${index}`">
      <div
        v-for="index in itemPerRow"
        :key="index"
        :class="`item item-${index}`"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Grid",
  data() {
    return {
      itemPerRow: 30,
      itemWidth: "40px",
      itemHeight: "50px",
    };
  },
};
</script>

<style style="scss">
.grid-container {
  display: flex;
  flex-direction: column;
  position: absolute;
  top: 0;
  left: 0;
}

.row {
  flex: 1;
  display: flex;
}

.item {
  width: 40px;
  height: 40px;
  flex: 1;
  background-color: #eee;
  margin: 10px;
}
</style>
