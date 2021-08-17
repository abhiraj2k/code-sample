<template>
  <drawer-opener />
</template>

<script>
import DrawerOpener from "./components/DrawerOpener.vue";
import "light-icons/dist/light-icon.css";
export default {
  name: "App",

  components: {
    DrawerOpener
  },
  data: () => ({})
};
</script>
<style lang="scss" scoped>
@import url("https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,300;0,400;0,500;0,700;1,300;1,400;1,500&display=swap");
* {
  font-family: "Roboto", sans-serif;
  box-sizing: border-box;
}
body {
  margin: 0;
  padding: 0;
}
</style>