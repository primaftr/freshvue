<template>
  <div class="div">
    <button class="mx-3" v-on:click="test">Populasi awal</button>
    <button class="mx-3" v-on:click="checkM">Calculate</button>
    <button class="mx-3" v-on:click="calcFitness">Hitung Fitness</button>
    <button class="mx-3" v-on:click="ga">GA</button>
    <div>{{ bestPop }} = {{ bestDist }}</div>
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
      fitness: [],
      savedMiniRoute: [],
      request: [],
      newPop: [],
      generasi: [],
      savedPop: [],
      bestPop: [],
      bestDist: Infinity,
    };
  },

  created() {
    if (localStorage.savedMiniRoute) {
      var isi = JSON.parse(localStorage.savedMiniRoute);
      this.savedMiniRoute = isi;
    }
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
    shuffle: function (x, num) {
      for (var s = 0; s < num; s++) {
        var IndexA = Math.floor(Math.random(x.length) * x.length + 1) - 1;
        var IndexB = Math.floor(Math.random(x.length) * x.length + 1) - 1;
        this.swap(x, IndexA, IndexB);
      }
    },
    swap: function (a, i, j) {
      var temp = a[i];
      a[i] = a[j];
      a[j] = temp;
    },
    //Method ini membangkitkan rute/order populasi awal secara random
    test: function () {
      for (var i = 0; i < Object.keys(this.gen).length; i++) {
        this.order[i] = i + 1;
      }
      for (var n = 0; n < 10; n++) {
        this.population[n] = this.order.slice();
        let pop = this.population[n];
        this.shuffle(pop, 10);
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

    cekData: function (o, d, value) {
      var data = JSON.parse(localStorage.savedMiniRoute);
      data[o][d] = value;
      localStorage.savedMiniRoute = JSON.stringify(data);
    },
    pickOne: function (list, prob) {
      let index = 0;
      var r = Math.random(1);
      while (r > 0) {
        r -= prob[index];
        index++;
      }
      index--;
      return list[index].slice();
    },

    normalizeFitness: function () {
      let sum = 0;
      for (let i = 0; i < this.fitness.length; i++) {
        sum += this.fitness[i];
      }
      for (let i = 0; i < this.fitness.length; i++) {
        this.fitness[i] = this.fitness[i] / sum;
      }
    },

    nextGen: function () {
      for (let i = 0; i < this.population.length; i++) {
        let orderA = this.pickOne(this.population, this.fitness);
        let orderB = this.pickOne(this.population, this.fitness);
        let order = this.crossOver(orderA, orderB);
        let mutationRate = 10 / 100;
        this.mutate(order, mutationRate);
        this.newPop[i] = order;
        this.savedPop.push(order.slice());
        this.$set(this.population, i, order);
      }
      this.generasi++;
    },
    mutate: function (x, mutationRate) {
      if (Math.random(1) < mutationRate) {
        var IndexA = Math.floor(Math.random(x.length) * x.length + 1) - 1;
        var IndexB = Math.floor(Math.random(x.length) * x.length + 1) - 1;
        this.swap(x, IndexA, IndexB);
      }
    },

    crossOver: function (a, b) {
      let start = Math.floor(Math.random(a.length) * a.length + 1) - 1;
      let end = Math.floor(Math.random() * (b.length - start + 1)) + start;
      let newOrder = a.slice(start, end);
      for (let i = 0; i < b.length; i++) {
        let isiB = b[i];
        if (!newOrder.includes(isiB)) {
          newOrder.push(isiB);
        }
      }
      return newOrder;
    },

    ga: async function () {
      for (let i = 0; i < 1000; i++) {
        await this.calcFitness().then(() => {
          this.nextGen();
        });
      }
    },
    calcFitness: async function () {
      let sum = 0;
      // let d = new Date();
      for (const i in this.population) {
        for (let s = 1; s < this.population[i].length; s++) {
          let o = this.population[i][s - 1];
          let d = this.population[i][s];
          let jarak = await this.checkMiniRoute(o, d).then((r) => {
            return r;
          });
          sum += jarak;
        }
        if (sum < this.bestDist) {
          this.bestDist = sum;
          // this.bestPop = this.population[i];
          this.$set(this.bestPop, 1, this.population[i]);
        }
        let fitness = 1 / (sum + 1);
        this.$set(this.distance, i, sum);
        this.$set(this.fitness, i, fitness);
        this.normalizeFitness();

        // console.log(d.getMilliseconds());
      }
    },

    //Check apakah calcDistance a ke b pernah dipanggil
    checkMiniRoute: async function (o, d) {
      if (d === undefined) {
        return;
      } else {
        //mengambil promise calcDistance

        //jika rute dari node a belum ada sama sekali
        if (this.savedMiniRoute[o] === undefined) {
          let jara = await this.calcDistance(o, d).then((res) => {
            return res.rows[0].elements[0].distance.value;
          });
          // console.log(`Rute ${o}${d} diinput! Value = ${jara} `);
          this.savedMiniRoute[o] = { [d]: jara };
          this.$set(this.savedMiniRoute, o, this.savedMiniRoute[o]);
          this.cekData(o, d, jara);
          return this.savedMiniRoute[o][d];
        }
        //jika node sudah pernah di panggil dan tersimpan
        else if (this.savedMiniRoute[o][d]) {
          // console.log(`Rute ${o}${d} sudah ada, api tidak dipanggil`);
          return this.savedMiniRoute[o][d];
        }
        //jika node dari a sudah ada, ke b belum ada.
        else if (this.savedMiniRoute[o]) {
          let jara = await this.calcDistance(o, d).then((res) => {
            return res.rows[0].elements[0].distance.value;
          });
          // console.log(`Rute ${o}${d} ditambahkan! Value ${jara}`);
          this.savedMiniRoute[o][d] = jara;
          this.$set(this.savedMiniRoute[o], d, jara);
          this.cekData(o, d, jara);
          return this.savedMiniRoute[o][d];
        }
      }
    },

    //method ini untuk menghitung jarak dari rute populasi
    calcDistance: function (o, d) {
      this.request.push(1);
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
