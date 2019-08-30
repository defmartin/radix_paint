<template>
  <!-- <span>
    <span @mouseover="$emit('changePixel',value)" v:bind:class="class">x</span>
  </span> -->
  <span v-if="value.color" @mouseover="$emit('changePixel',value)" class="background-light">x</span>
  <span v-else @mouseover="$emit('changePixel',value)" class="background-dark">x</span>
</template>

<!-- v-bind:class="{'colored': value.color,  'blank': !value.color}" -->
<!-- :class="[ !value.color ? 'background-dark' : 'background-light' ]" -->

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
  background-color: #fff;
}
</style>