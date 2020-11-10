<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <HelloWorld :onSelect="() => {}" />
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
  },
};
</script>
