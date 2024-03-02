<template>
  <button @click="productAfterwards">Next product</button>
</template>

<script setup>
import { defineEmits } from "vue";

const emits = defineEmits(["nextProduct"]);

const productAfterwards = () => {
  emits("nextProduct");
};
</script>
