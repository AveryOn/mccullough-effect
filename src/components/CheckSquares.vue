<script setup lang="ts">

</script>

<template>
  <div class="check-squares-block">
    <div class="striped vertical"></div>
    <div class="striped horizontal"></div>
    <div class="striped horizontal"></div>
    <div class="striped vertical"></div>
  </div>
</template>


<style scoped>
.check-squares-block {
  width: 100%;
  height: 100%;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}

.striped {
  width: 50%;
  height: 50%;
}

.vertical {
  background: repeating-linear-gradient(
    to right,
    rgb(218, 218, 218) 0px,
    rgb(218, 218, 218) 18px,
    rgb(0, 0, 0) 10px,
    rgb(0, 0, 0) 30px
  );
}

.horizontal {
  background: repeating-linear-gradient(
    to bottom,
    rgb(218, 218, 218) 0px,
    rgb(218, 218, 218) 18px,
    rgb(0, 0, 0) 10px,
    rgb(0, 0, 0) 30px
  );
}

</style>
