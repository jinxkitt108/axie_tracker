<template>
  <q-page>
    <div style="max-width: 650px" class="q-mx-auto q-gutter-sm q-my-md">
      <div class="text-center">
        <p class="text-h6 q-ma-md">
          Powered by
          <span v-if="$q.platform.is.electron">
            CC Gamers
          </span>
          <q-avatar v-else square>
            <img src="icons/favicon-128x128.png" />
          </q-avatar>
        </p>
      </div>
      <div style="width: 350px" class="q-mx-auto">
        <q-form
          class="q-gutter-sm q-pa-sm"
          ref="form"
          @reset="onReset"
          @submit="addData"
        >
          <q-input
            v-model="form.name"
            dense
            required
            label="Name"
            outlined
            rounded
            hint="Use unique name for scholar."
          />
          <q-input
            v-model="form.eth"
            dense
            required
            label="Eth"
            outlined
            rounded
            hint="Ex. 0x3de46d00....."
          />
          <div class="text-center">
            <q-btn type="submit" color="dark" label="Add Scholar" />
            <q-btn
              label="Reset"
              type="reset"
              color="negative"
              flat
              class="q-ml-sm"
            />
          </div>
        </q-form>
      </div>

      <div class="q-mt-lg">
        <div class="text-center q-gutter-lg q-mb-sm q-mt-sm">
          <span class="text-subtitle2 text-weight-bold"
            >No. of Scholars:
            <span class="text-indigo q-ml-xs">{{ ethArray.length }}</span></span
          >
          <!-- <span class="text-caption text-weight-bold"
            >Total SLP: <span class="text-indigo">{{ total_slp }}</span></span
          > -->
        </div>
        <div class="text-center q-gutter-lg q-mb-sm q-mt-sm">
          <!-- <div class="col-5 self-center">
            <q-btn icon="recommend" rounded outline size="sm" label="Donate !" color="primary" @click="donate = true" />
          </div> -->
          <!-- <div class="col-7 q-gutter-lg self-center"> -->
          <span class="text-caption text-weight-bold"
            >Total Ronin Bal:
            <!-- <span class="text-indigo q-ml-xs">{{ total_claimable }}</span> -->
          </span>
          <span class="text-caption text-weight-bold"
            >Total SLP: <span class="text-indigo">{{ total_slp }}</span></span
          >
          <!-- </div> -->
        </div>
        <div class="row q-mt-sm q-gutter-sm">
          <q-btn
            color="indigo"
            icon-right="archive"
            label="Excel"
            rounded
            size="sm"
            no-caps
            @click="exportTable"
          />
          <q-btn
            color="primary"
            label="Copy Scholars"
            size="sm"
            @click="exportScholars()"
            rounded
            no-caps
          />
          <q-btn
            color="teal"
            label="Import Scholars"
            size="sm"
            @click="importScholars"
            rounded
            no-caps
          />
          <q-btn
            color="negative"
            label="Clear Scholars"
            size="sm"
            @click="clearData"
            rounded
            no-caps
          />
        </div>
        <q-table
          class="q-mt-md"
          :dense="$q.screen.lt.md"
          :filter="search"
          title="Scholars"
          :columns="columns"
          :data="scholarData"
          :row-key="row => row.id"
          :loading="loading"
          :pagination="pagination"
          no-data-label="No scholars data available. Add one!"
          no-results-label="Search not found. . ."
        >
          <template v-slot:top-right>
            <q-input
              class="q-mb-sm"
              dense
              rounded
              outlined
              debounce="300"
              v-model="search"
              placeholder="Search"
            >
              <template v-slot:append>
                <q-icon name="search" />
              </template>
            </q-input>
          </template>
          <template v-slot:body="props">
            <q-tr :props="props">
              <q-td class="text-weight-bold" key="name" :props="props">
                {{ props.row.name }}
              </q-td>
              <q-td class="text-weight-bold" key="mmr" :props="props">
                {{ props.row.mmr }}
              </q-td>
              <q-td key="ronin_slp" :props="props">
                {{ props.row.ronin_slp }}
              </q-td>
              <q-td key="days" :props="props">
                {{ props.row.days }}
              </q-td>
              <q-td key="average" :props="props">
                {{ props.row.average }}
              </q-td>
              <q-td key="in_game_slp" :props="props">
                <q-avatar size="18px">
                  <img
                    src="data:image/jpeg;base64,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"
                    alt=""
                  />
                </q-avatar>
                {{ props.row.in_game_slp }}
                <q-badge
                  v-if="props.row.sync_ready"
                  rounded
                  color="green"
                  floating
                  >Claimable</q-badge
                >
              </q-td>
              <q-td key="action" :props="props" class="text-center q-gutter-sm">
                <!-- <q-btn flat @click="copyEth(props.row.eth)" round size="xs">
                  <q-tooltip anchor="top middle" self="bottom middle">
                    <span class="text-caption">{{ props.row.eth }}</span>
                  </q-tooltip>
                  <q-avatar size="sm">
                    <img
                      src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/6f/Ethereum-icon-purple.svg/480px-Ethereum-icon-purple.svg.png"
                    />
                  </q-avatar>
                </q-btn> -->
                <q-btn flat @click="copyRonin(props.row.ronin)" round size="xs">
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
          <template v-slot:no-data="{ message, filter }">
            <div
              class="full-width row flex-center text-indigo q-gutter-sm q-pa-lg"
            >
              <q-avatar size="60px">
                <img
                  v-if="filter"
                  src="https://cdn.substack.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2F63617aa3-f2a9-4daa-9865-e5b92b2ec6f5_1456x1340.png"
                />
                <img
                  v-else
                  src="https://www.clipartkey.com/mpngs/m/183-1839175_axie-infinity-png.png"
                />
              </q-avatar>
              <span class="text-subtitle2">{{ message }} </span>
            </div>
          </template>
        </q-table>
      </div>
    </div>
  </q-page>
</template>

<script>
import { date } from "quasar";
import { copyToClipboard } from "quasar";
import { exportFile } from "quasar";

function wrapCsvValue(val, formatFn) {
  let formatted = formatFn !== void 0 ? formatFn(val) : val;

  formatted =
    formatted === void 0 || formatted === null ? "" : String(formatted);

  formatted = formatted.split('"').join('""');
  /**
   * Excel accepts \n and \r in strings, but some other CSV parsers do not
   * Uncomment the next two lines to escape new lines
   */
  // .split('\n').join('\\n')
  // .split('\r').join('\\r')

  return `"${formatted}"`;
}

export default {
  data() {
    return {
      scholars: [],
      pagination: {
        sortBy: "desc",
        descending: false,
        page: 1,
        rowsPerPage: 20
        // rowsNumber: xx if getting data from a server
      },
      donate: false,
      loading: false,
      total_slp: 0,
      total_claimable: 0,
      search: "",
      form: new Form({
        id: null,
        name: null,
        eth: null
      }),
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
          name: "mmr",
          required: true,
          label: "MMR",
          align: "center",
          field: row => row.mmr,
          sortable: true
        },
        {
          name: "ronin_slp",
          required: true,
          label: "Ronin SLP",
          align: "center",
          field: row => row.ronin_slp
        },
        {
          name: "days",
          required: true,
          label: "Days",
          align: "left",
          field: row => row.days,
          sortable: true
        },
        {
          name: "average",
          required: true,
          label: "Average",
          align: "left",
          field: row => row.average,
          sortable: true
        },
        {
          name: "in_game_slp",
          required: true,
          label: "In-Game SLP",
          align: "left",
          field: row => row.in_game_slp,
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
    clearData() {
      if (this.ethArray.length) {
        this.$q
          .dialog({
            dark: true,
            title: "Confirm",
            message:
              "Are you sure you want to <span class='text-red-6 text-weight-bold'>DELETE</span> all scholars?",
            cancel: true,
            persistent: true,
            html: true
          })
          .onOk(() => {
            localStorage.clear();
            this.ethArray = [];
            this.scholarData = [];
            this.total_slp = 0;
            this.total_claimable = 0;
          });
      } else {
        this.$q.dialog({
          dark: true,
          title: "Alert",
          message: "No available data for scholars!"
        });
      }
    },

    async importScholars() {
      try {
        const ethArray = await navigator.clipboard.readText();
        var data = JSON.parse(ethArray);
        data.forEach(eth => {
          if (eth.eth) {
            this.$q.localStorage.set(eth.id, {
              id: eth.id,
              eth: eth.eth,
              name: eth.name
            });
          }
        });

        setTimeout(this.getData(), 4500);
      } catch (e) {
        this.$q.dialog({
          dark: true,
          title: "Alert",
          message: "No available data for scholars! Copy it again!"
        });
      }
    },

    exportScholars() {
      if (this.ethArray.length) {
        copyToClipboard(JSON.stringify(this.ethArray))
          .then(() => {
            this.$q.notify({
              message: "Copied!",
              icon: "thumb_up",
              color: "dark",
              position: "center",
              timeout: 600
            });
          })
          .catch(() => {
            // fail
          });
      } else {
        this.$q.dialog({
          dark: true,
          title: "Alert",
          message: "No available data for scholars!"
        });
      }
    },

    exportTable() {
      if (this.ethArray.length) {
        // naive encoding to csv format
        const content = [this.columns.map(col => wrapCsvValue(col.label))]
          .concat(
            this.scholarData.map(row =>
              this.columns
                .map(col =>
                  wrapCsvValue(
                    typeof col.field === "function"
                      ? col.field(row)
                      : row[col.field === void 0 ? col.name : col.field],
                    col.format
                  )
                )
                .join(",")
            )
          )
          .join("\r\n");

        const status = exportFile(
          "axie-scholars-table.csv",
          content,
          "text/csv"
        );

        if (status !== true) {
          this.$q.notify({
            message: "Browser denied file download...",
            color: "negative",
            icon: "warning"
          });
        }
      } else {
        this.$q.dialog({
          dark: true,
          title: "Alert",
          message: "No available data for scholars!"
        });
      }
    },

    copyRonin(addy) {
      copyToClipboard(addy)
        .then(() => {
          this.$q.notify({
            message: "Copied Ronin Address!",
            icon: "thumb_up",
            color: "dark",
            position: "center",
            timeout: 600
          });
        })
        .catch(() => {
          // fail
        });
    },

    copyEth(addy) {
      copyToClipboard(addy)
        .then(() => {
          this.$q.notify({
            message: "Copied Eth Address!",
            icon: "thumb_up",
            color: "dark",
            position: "center",
            timeout: 600
          });
        })
        .catch(() => {
          // fail
        });
    },

    deleteData(data) {
      this.$q
        .dialog({
          dark: true,
          title: "Confirm",
          message:
            'Are you sure you want to remove <span class="text-yellow text-subtitle2">"' +
            data.name +
            '"</span> as a scholar?',
          cancel: true,
          html: true,
          persistent: true
        })
        .onOk(() => {
          const local = this.$q.localStorage;
          local.remove(data.id);
          this.scholarData = this.scholarData.filter(
            item => item.id !== data.id
          );
          this.ethArray = this.ethArray.filter(item => item.id !== data.id);
          this.total_claimable = this.total_claimable - data.claimable;
          this.total_slp = this.total_slp - data.total;
        });
    },

    async getData() {
      if (localStorage.length) {
        //Clear Data before syncing new scholars
        this.ethArray = [];
        this.scholarData = [];

        this.loading = true;
        const obj = this.$q.localStorage.getAll();
        Object.keys(obj).forEach(key => {
          this.ethArray.push({
            id: obj[key].id,
            name: obj[key].name,
            eth: obj[key].eth
          });
        });
        this.ethArray.sort();

        this.ethArray.forEach(item => {
          let ronin = item.eth.split(":").pop();

          this.$axios
            .get("https://api.lunaciarover.com/stats/" + "0x" + ronin)
            .then(res => {
              if (res) {
                const sch = res.data;
                const claimed_date = new Date(sch.last_claim_timestamp * 1000);
                const today = new Date();

                const diffInTime = today.getTime() - claimed_date.getTime();
                // Calculating the no. of days between two dates
                const diffInDays = Math.round(diffInTime / 86400000);
                // const inv_slp = sch.items[0].total - sch.items[0].claimable_total;

                const ave = Math.round(sch.in_game_slp / diffInDays);

                this.scholarData.push({
                  id: item.id,
                  name: item.name,
                  days: diffInDays,
                  average: ave,
                  mmr: sch.mmr,
                  in_game_slp: sch.in_game_slp,
                  ronin_slp: sch.ronin_slp,
                  ronin: sch.ronin_address.replace("0x", "ronin:"),
                  sync_ready: diffInDays >= 14 ? true : false
                });
                this.total_slp = this.total_slp + sch.total_slp;
                this.loading = false;
              }
            })
            .catch(err => {
              console.log(err.data);
            });
        });
      }
    },

    async addData() {
      this.loading = true;
      this.form.id = Date.now();
      if (this.form.name && this.form.eth) {
        await this.$q.localStorage.set(this.form.id, {
          id: this.form.id,
          eth: this.form.eth,
          name: this.form.name
        });
        this.newData({ eth: this.form.eth, name: this.form.name });
        setTimeout(this.onReset(), 3000);
      }
    },

    newData(item) {
      let ronin = item.eth.split(":").pop();

      this.$axios
        .get("https://api.lunaciarover.com/stats/" + "0x" + ronin)
        .then(res => {
          if (res) {
            const sch = res.data;
            const claimed_date = new Date(sch.last_claim_timestamp * 1000);
            const today = new Date();

            const diffInTime = today.getTime() - claimed_date.getTime();
            // Calculating the no. of days between two dates
            const diffInDays = Math.round(diffInTime / 86400000);
            // const inv_slp = sch.items[0].total - sch.items[0].claimable_total;

            const ave = Math.round(sch.in_game_slp / diffInDays);

            this.scholarData.push({
              id: item.id,
              name: item.name,
              days: diffInDays,
              average: ave,
              mmr: sch.mmr,
              in_game_slp: sch.in_game_slp,
              ronin_slp: sch.ronin_slp,
              ronin: sch.ronin_address.replace("0x", "ronin:"),
              sync_ready: diffInDays >= 14 ? true : false
            });
            this.total_slp = this.total_slp + sch.total_slp;
            this.loading = false;
          }
        })
        .catch(err => {
          console.log(err);
        });
    },

    onReset() {
      this.form.eth = null;
      this.form.name = null;
    }
  },

  created() {
    this.getData();
  }
};
</script>
