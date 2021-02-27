<template>
  <div class="profile">
    <h1 class="mb-5">This is a Profile page</h1>
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-lg-6">
          <div class="media text-left">
            <img src="../assets/logo.png" class="mr-3 user-pic" />
            <div class="media-body">
              <h5 class="mt-0">{{ namaLengkap }}</h5>
              <p>
                Will you do the same for me? It's time to face the music I'm no
                longer your muse. Heard it's beautiful, be the judge and my
                girls gonna take a vote.
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue'
import "bootstrap/dist/css/bootstrap.css";

const nama = {
  firstName: "Prima",
  lastName: "Fitra",
  qty: 1,
};
export default {
  name: "Profile",
  data: function () {
    return nama;
  },
  methods: {
    getFirstName: function () {
      return this.firstName;
    },
    getLastName: function () {
      return this.lastName;
    },
    alterName: function (namaBaru) {
      this.firstName = namaBaru;
    },
  },
  computed: {
    totalBayar: function () {
      return this.qty * 10000;
    },
    namaLengkap: function () {
      return this.getFirstName() + " " + this.getLastName();
    },
  },
  components: {
    // HelloWorld
  },
};
</script>

<style scoped>
.user-pic {
  height: 80px;
  width: 80px;
  border-radius: 200%;
}
</style>
