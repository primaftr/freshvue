<template>
  <div class="home">
    <h1>This is Home Page {{ angka }}</h1>
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue'
const data = {
  angka: 3,
};
export default {
  name: "Home",
  components: {
    // HelloWorld
  },
  data: function () {
    return data;
  },
};
</script>
