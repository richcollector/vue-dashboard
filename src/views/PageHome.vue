<!-- PageHome.vue -->
<template>
  <div class="home">
    <img alt="hangjung logo" src="../assets/hang.png" />
    <PageIndex msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
// @ is an alias to /src
import PageIndex from "@/components/layout/PageIndex.vue";

export default {
  name: "PageHome",
  components: {
    PageIndex,
  },
};
</script>
