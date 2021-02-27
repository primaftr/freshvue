<template>
  <div class="div">
    <button class="mx-3" v-on:click="test">test</button>
    <button class="mx-3" v-on:click="checkM">Calculate</button>
    <button class="mx-3" v-on:click="calcFitness">Hitung Fitness</button>
    <div class="map">
      <gmap-map
        :center="{ lat: 10, lng: 10 }"
        :zoom="14"
        style="width: 50%; height: 320px"
      ></gmap-map>
    </div>
    <div class="container">
      <div class="row mt-4">
        <div class="col-md-6">
          <div v-for="(population, id) in population" :key="id">
            Rute {{ id + 1 }}= {{ population }} fitness = {{ distance[id] }}
          </div>
        </div>
        <div class="col-md-6">
          <div v-for="(g, id) in gen" :key="id">Gen ke {{ id }} = {{ g }}</div>
        </div>
      </div>
      <div class="div">
        <table class="table">
          <tr>
            <th v-for="(e, id) in savedMiniRoute" :key="id">
              {{ id }}
            </th>
          </tr>
          <template v-for="(e, id) in savedMiniRoute">
            <tr v-if="renderNode(id, id)" v-bind:key="id">
              <th>{{ id }}</th>
              <template v-for="(r, i) in savedMiniRoute">
                <td v-if="renderNode(id, i)" :key="i">
                  {{ renderNode(id, i) }}
                </td>
              </template>
            </tr>
          </template>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.css";
export default {
  data() {
    return {
      gen: {
        1: { lat: 0.4773651, lng: 101.4048186 },
        2: { lat: 0.5117239, lng: 101.4397909 },
        3: { lat: 0.4505657, lng: 101.4011792 },
        4: { lat: 0.4698786, lng: 101.3811158 },
        5: { lat: 0.4737488, lng: 101.472948 },
        6: { lat: 0.536217, lng: 101.4496793 },
        7: { lat: 0.5701812, lng: 101.4233154 },
      },
      population: [],
      order: [],
      distance: [],
      savedMiniRoute: [],
    };
  },
  methods: {
    renderNode: function (i, e) {
      if (i === 0 || e === 0) {
        return false;
      } else if (this.savedMiniRoute[i] === undefined) {
        return false;
      } else if (this.savedMiniRoute[e] === undefined) {
        return "belum diisi";
      } else if (this.savedMiniRoute[i][e]) {
        return this.savedMiniRoute[i][e];
      } else {
        return "belum diisi";
      }
    },
    //Method ini membangkitkan rute/order populasi awal secara random
    test: function () {
      function shuffle(x, num) {
        for (var s = 0; s < num; s++) {
          var IndexA = Math.floor(Math.random(x.length) * x.length + 1) - 1;
          var IndexB = Math.floor(Math.random(x.length) * x.length + 1) - 1;
          swap(x, IndexA, IndexB);
        }
      }
      function swap(a, i, j) {
        var temp = a[i];
        a[i] = a[j];
        a[j] = temp;
      }

      for (var i = 0; i < Object.keys(this.gen).length; i++) {
        this.order[i] = i + 1;
      }
      for (var n = 0; n < 2; n++) {
        this.population[n] = this.order.slice();
        let pop = this.population[n];
        shuffle(pop, 10);
        this.$set(this.population, n, pop);
      }
    },

    checkM: function () {
      this.population.forEach((i) => {
        // let sum = 0;
        for (let s = 1; s < i.length; s++) {
          let o = i[s - 1];
          let d = i[s];
          this.checkMiniRoute(o, d);
        }
      });
    },

    calcFitness: function () {
      let sum = 0;
      this.population.forEach((i) => {
        for (let s = 1; s < i.length; s++) {
          let o = i[s - 1];
          let d = i[s];
          sum += this.savedMiniRoute[o][d];
        }
        this.distance.push(sum);
      });
    },

    //Check apakah calcDistance a ke b pernah dipanggil
    checkMiniRoute: function (o, d) {
      if (d === undefined) {
        return;
      } else {
        //mengambil promise calcDistance
        let jara = this.calcDistance(o, d).then((res) => {
          return res.rows[0].elements[0].distance.value;
        });
        //jika rute dari node a belum ada sama sekali
        if (this.savedMiniRoute[o] === undefined) {
          console.log(`Rute ${o}${d} diinput! Value = ${jara} `);
          this.savedMiniRoute[o] = { [d]: jara };
          this.$set(this.savedMiniRoute, o, this.savedMiniRoute[o]);
          return this.savedMiniRoute[o][d];
        }
        //jika node dari a sudah ada, ke b belum ada.
        else if (this.savedMiniRoute[o]) {
          this.savedMiniRoute[o][d] = jara;
          this.$set(this.savedMiniRoute[o], d, jara);
          return this.savedMiniRoute[o][d];
        }
        //jika node sudah pernah di panggil dan tersimpan
        else if (this.savedMiniRoute[o][d]) {
          return this.savedMiniRoute[o][d];
        }
      }
    },

    //method ini untuk menghitung jarak dari rute populasi
    calcDistance: function (o, d) {
      // let hasil;
      let origin = { lat: this.gen[o].lat, lng: this.gen[o].lng };
      let destination = { lat: this.gen[d].lat, lng: this.gen[d].lng };
      const service = new window.google.maps.DistanceMatrixService();
      return new Promise((resolve, reject) => {
        service.getDistanceMatrix(
          {
            origins: [origin],
            destinations: [destination],
            travelMode: window.google.maps.TravelMode.DRIVING,
            unitSystem: window.google.maps.UnitSystem.METRIC,
          },
          (response, status) => {
            if (status !== "OK") {
              reject(status);
            } else {
              resolve(response);
            }
          }
        );
      });
    },
  },
};
</script>
