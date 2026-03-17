<template>
  <div id="app">
    <RegistroHotel />
  </div>
</template>

<script>
import RegistroHotel from "./components/registrohotel.vue";

export default {
  name: "App",
  components: {
    RegistroHotel
  }
};
</script>

<style>
body {
  margin: 0;
  background: linear-gradient(135deg, #eef6fb, #f9fcff);
}
</style>