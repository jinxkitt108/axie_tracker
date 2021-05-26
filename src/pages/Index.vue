<template>
  <q-page>
    <div style="width: 650px" class="q-mx-auto q-gutter-sm q-my-md">
      <div class="text-center">
        <p class="text-h5 q-ma-md">
          Powered by
          <q-avatar square>
            <img src="icons/favicon-128x128.png" />
          </q-avatar>
        </p>
      </div>
      <div style="width: 350px" class="q-gutter-sm q-mx-auto">
        <q-input v-model="form.name" dense label="Name" outlined rounded />
        <q-input v-model="form.eth" dense label="Eth" outlined rounded />
        <div class="text-center">
          <q-btn @click="addData" color="dark" label="Add Scholar" />
        </div>
      </div>

      <div class="q-mt-lg">
        <q-table
          :filter="search"
          title="Details"
          :columns="columns"
          :data="scholarData"
          :row-key="row => row.id"
          dense
        >
          <template v-slot:top-right>
            <q-input class="q-mb-sm" dense debounce="300" v-model="search" placeholder="Search">
              <template v-slot:append>
                <q-icon name="search" />
              </template>
            </q-input>
          </template>
          <template v-slot:body="props">
            <q-tr :props="props">
              <q-td key="name" :props="props">
                {{ props.row.name }}
              </q-td>
              <q-td key="claimable" :props="props">
                {{ props.row.claimable }}
              </q-td>
              <q-td key="days" :props="props">
                {{ props.row.days }}
              </q-td>
              <q-td key="average" :props="props">
                {{ props.row.average }}
              </q-td>
              <q-td key="total" :props="props">
                {{ props.row.total }}
              </q-td>
              <q-td key="action" :props="props" class="text-center q-gutter-sm">
                <q-btn round size="xs">
                  <q-tooltip anchor="top middle" self="bottom middle">
                    <span class="text-caption">{{ props.row.ronin }}</span>
                  </q-tooltip>
                  <q-avatar size="sm">
                    <img
                      src="https://lh3.googleusercontent.com/u4w_pL6Yxc0Cv2Lj7gQhkhiowv5jD0ktFSMfiuZKdTPG5h5qVFmgbOTTxq-MmVPAlc4Zro2SE2c=w128-h128-e365-rj-sc0x00ffffff"
                    />
                  </q-avatar>
                </q-btn>
                <q-btn
                  @click="deleteData(props.row)"
                  round
                  color="red"
                  icon="delete"
                  size="xs"
                />
              </q-td>
            </q-tr>
          </template>
        </q-table>
      </div>
    </div>
  </q-page>
</template>

<script>
import { date } from "quasar";

export default {
  data() {
    return {
      search: "",
      form: {
        id: "",
        name: "",
        eth: ""
      },
      ethArray: [],
      scholarData: [],

      columns: [
        {
          name: "name",
          required: true,
          label: "Name",
          align: "left",
          field: row => row.name,
          sortable: true
        },
        {
          name: "claimable",
          required: true,
          label: "Claimable",
          align: "left",
          field: row => row.claimable,
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
          label: "Total SLP",
          align: "left",
          field: row => row.total,
          sortable: true
        },
        {
          name: "action",
          required: true,
          label: "Action",
          align: "center"
        }
      ]
    };
  },

  methods: {
    async deleteData(data) {
      const local = this.$q.localStorage;
      await local.remove(data.id);
      this.scholarData = this.scholarData.filter(item => item.id !== data.id);
    },

    async getData() {
      if (localStorage.length) {
        const obj = this.$q.localStorage.getAll();
        Object.keys(obj).forEach(key => {
          this.ethArray.push(obj[key]);
        });

        this.ethArray.forEach(item => {
          this.$axios
            .get(
              "https://lunacia.skymavis.com/game-api/clients/" +
                item.eth +
                "/items?offset=0&limit=1"
            )
            .then(res => {
              if (res) {
                const sch = res.data;
                const claimed_date = new Date(
                  sch.items[0].last_claimed_item_at * 1000
                );
                const today = new Date();
                const diff = date.getDateDiff(today, claimed_date, "days");

                const diffInTime = today.getTime() - claimed_date.getTime();
                // Calculating the no. of days between two dates
                const diffInDays = Math.round(diffInTime / 86400000);

                const ave = Math.round(sch.items[0].total / diffInDays);

                this.scholarData.push({
                  id: item.id,
                  name: item.name,
                  claimable: sch.items[0].claimable_total,
                  days: diffInDays,
                  average: ave,
                  total: sch.items[0].total,
                  ronin: sch.items[0].ronin_address
                });
              }
            })
            .catch(err => {
              console.log(err.data);
            });
        });
      }
    },

    async addData() {
      this.form.id = this.ethArray.length + 1;
      await this.$q.localStorage.set(this.form.id, this.form);
      this.newData({eth: this.form.eth, name: this.form.name});
      setTimeout(this.clear(), 3000);
    },

    newData(item) {
      this.$axios
        .get(
          "https://lunacia.skymavis.com/game-api/clients/" +
            item.eth +
            "/items?offset=0&limit=1"
        )
        .then(res => {
          if (res) {
            const sch = res.data;
            const claimed_date = new Date(
              sch.items[0].last_claimed_item_at * 1000
            );
            const today = new Date();
            const diff = date.getDateDiff(today, claimed_date, "days");

            const diffInTime = today.getTime() - claimed_date.getTime();
            // Calculating the no. of days between two dates
            const diffInDays = Math.round(diffInTime / 86400000);

            const ave = Math.round(sch.items[0].total / diffInDays);

            this.scholarData.push({
              id: item.id,
              name: item.name,
              claimable: sch.items[0].claimable_total,
              days: diffInDays,
              average: ave,
              total: sch.items[0].total,
              ronin: sch.items[0].ronin_address
            });
          }
        })
        .catch(err => {
          console.log(err);
        });
    },

    clear() {
      this.form.id = "";
      this.form.name = "";
      this.form.eth = "";
    }
  },

  created() {
    this.getData();
  }
};
</script>
