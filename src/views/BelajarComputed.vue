<template>
  <div class="div">
    <div class="profile">
      <h1 class="mb-5">This is a Profile page</h1>
      <div class="container">
        <div class="row justify-content-center">
          <div class="col-lg-6">
            <div class="media text-left">
              <div class="media-body">
                <h5 class="mt-0"></h5>
                <p>Bilangan {{ angka }} adalah bilangan {{ cekAngka }}</p>
                <p><button v-on:click="handleClick">Tambah Bilangan</button></p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
const data = {
  angka: 3,
};
export default {
  name: "Computed",
  data: function () {
    return data;
  },
  methods: {
    handleClick: function () {
      this.angka++;
    },
  },
  components: {},
  computed: {
    cekAngka: function () {
      return this.angka % 2 === 0 ? "Genap" : "Ganjil";
    },
  },
};
</script>
