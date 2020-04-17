<template>
  <div class="grid-container">
    <div v-for="index in 3" :key="index" :class="`row row-${index}`">
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
      itemPerRow: 12,
      itemWidth: "40px",
      itemHeight: "40px",
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
  border: 1px solid orange;
  padding: 0 4rem;
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
