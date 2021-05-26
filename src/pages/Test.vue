<template>
  <q-page>
    <div class="q-pa-md q-mx-auto" style="width: 650px">
      <p class="text-h6 q-mx-auto">Powered by NGG</p>
      <div class="q-gutter-sm q-ma-md row items-start content-start">
        <q-input
          class="inset"
          dense
          rounded
          outlined
          v-model="scholar.eth"
          label="Eth Address"
        />
        <q-input
          class="inset"
          dense
          rounded
          outlined
          v-model="scholar.name"
          label="Scholar Name"
        />
        <q-btn
          @click="addScholar"
          color="primary"
          icon="add"
          rounded
          label="Add"
        />
      </div>
      <table class="q-table">
        <thead>
          <tr>
            <th>Name</th>
            <th>Days</th>
            <th>Average</th>
            <th>Total</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, i) in ethArray" :key="i">
            <th>{{item.name}}</th>
            <th></th>
            <th></th>
            <th></th>
          </tr>
        </tbody>
      </table>
    </div>
  </q-page>
</template>

<script>
import axios from "app/node_modules/axios";
export default {
  data() {
    return {
      //Scholar Details
      scholar: new Form({
        id: "",
        eth: "",
        name: ""
      }),

      ethArray: [],
      columns: [
        {
          name: "name",
          required: true,
          label: "Name",
          align: "left",
          field: row => row.name,
          format: val => `${val}`,
          sortable: true
        },
        {
          name: "days",
          required: true,
          label: "Days",
          align: "left",
          sortable: true
        },
        {
          name: "average",
          required: true,
          label: "Average",
          align: "left",
          sortable: true
        },
        {
          name: "total",
          required: true,
          label: "Total",
          align: "left",
          sortable: true
        }
      ]
    };
  },

  methods: {
    async addScholar() {
      this.scholar.id = this.ethArray.length + 1;
      this.ethArray.push(this.scholar);
      // await this.$q.localStorage.set("ethArray", this.ethArray);
      this.scholar.reset();
    },

    getScholars() {
      // if (localStorage.length) {
      //   this.ethArray = this.$q.localStorage.getItem("ethArray");
      //   this.ethArray.forEach(item => {
      //     // axios.get("https://lunacia.skymavis.com/game-api/clients/" + item.eth_add + "/items?offset=0&limit=1")
      //     // console.log(item);
      //     //  this.ethArray = this.$q.localStorage.getItem("ethArray")
      //   });
      // }
    }
  },

  created() {
    this.getScholars();
  }
};
</script>
