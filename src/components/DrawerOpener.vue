<template>
  <div class="drawer-wrapper">
    <div class="drawer-wrapper__card">
      <div class="drawer-wrapper__header">
        <div
          class="header-item --left"
          @click="drawer = !drawer"
        >
          <i class="light-icon-menu-2"></i>
        </div>
        <div
          class="header-item --right"
          @click="drawer = !drawer"
        >
          <i class="light-icon-menu-2"></i>
        </div>
      </div>
    </div>

    <drawer
      v-model="drawer"
      left
      headerTitle="Title Of Header"
      close
      shadow
      background="#fff"
      :percentWidth="50"
      :percentHeight="90"
      :maxSpan="95"
    >
      <template>
        <div class="list">
          <div
            class="list-item"
            v-for="(item,index) in items"
            :key="index"
          >
            {{item.name}}
          </div>
        </div>
      </template>
    </drawer>
  </div>
</template>

<script>
import Drawer from "./Drawer.vue";
export default {
  components: { Drawer },
  data() {
    return {
      items: [
        { name: "Item 1" },
        { name: "Item 2" },
        { name: "Item 3" },
        { name: "Item 4" },
        { name: "Item 5" },
        { name: "Item 6" },
        { name: "Item 7" },
        { name: "Item 8" },
        { name: "Item 9" },
        { name: "Item 10" }
      ],
      right: null,
      drawer: false,
      group: null
    };
  },
  watch: {
    group() {
      this.drawer = false;
    }
  }
};
</script>

<style lang="scss" scoped>
.drawer-wrapper {
  height: 100%;
  width: 100%;
  .list {
    display: flex;
    flex-direction: column;
    & > div {
      padding: 10px 15px;
      color: black;
      &:active {
        background-color: #762cff5b;
        color: white;
      }
    }
  }
  .drawer-wrapper__card {
    height: 100%;
    width: 100%;
    overflow: hidden;
    // padding: 15px;
    .drawer-wrapper__header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      height: 60px;
      background-image: linear-gradient(to bottom right, #2c4bff, #762cff);
      //   padding: 15px;
      .header-item {
        background-color: rgba(0, 0, 0, 0.13);
        padding: 15px;
        cursor: pointer;
        height: 100%;
        color: white;
        font-size: 25px;
        i {
          &::before,
          &::after {
            vertical-align: initial;
          }
        }
        &:hover {
          background-color: rgba(0, 0, 0, 0.459);
        }
      }
    }
  }
}
</style>