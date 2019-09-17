<template>
  <div v-if="value.color" @mouseover="$emit('changePixel',value)" class="background-dark pixel"> </div>
  <div v-else @mouseover="$emit('changePixel',value)" class="background-light pixel"> </div>
</template>

<script>
export default {
  name: "Pixel",
  props: {
    value: {}
  }
};
</script>

<style scoped>
.background-dark {
  background-color: #000;
}
.background-light {
  background-color: #999999;
}

</style>