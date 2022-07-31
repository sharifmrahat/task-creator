<template>
  <button @click="handleClick" :style="{ background: color }" class="btn">
    {{ text }}
  </button>
</template>

<script>
export default {
  name: "Button",
  props: {
    text: String,
    color: String,
  },
  methods: {
    handleClick() {
      this.$emit("toggle-add-task");
    },
  },
};
</script>
