<template>
  <q-layout view="hHh lpR fFf">
    <q-header elevated class="bg-dark text-white">
      <q-toolbar>
        <q-toolbar-title>
          <q-avatar v-if="!$q.platform.is.electron" square>
            <img src="icons/favicon-128x128.png" />
          </q-avatar>
          AXIE SCHOLARSHIP TRACKER
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>

    <q-footer class="bg-grey-10 text-white">
      <q-toolbar>
        <q-toolbar-title class="text-center q-pa-sm">
          <span class="text-caption"
            >Play to Earn.
            <a class="link" href="https://axieinfinity.com/">Axie Infinity</a>
            gaming!</span
          >
          <q-btn
            class="float-right"
            icon="recommend"
            rounded
            outline
            size="sm"
            label="Donate !"
            color="yellow"
            @click="donate = true"
          />

          <q-dialog v-model="donate">
            <q-card style="width: 350px; max-width: 80vw;">
              <q-card-section>
                <div class="text-h6">Details</div>
              </q-card-section>
              <q-card-section class="q-gutter-sm">
                <q-input outlined v-model="cc_eth" label="ERC20" readonly />
                <q-btn
                  dense
                  class="full-width"
                  @click="copyEth(cc_eth)"
                  label="Copy"
                  color="primary"
                />
              </q-card-section>

              <q-card-actions align="right">
                <q-btn flat label="Close" color="primary" v-close-popup />
              </q-card-actions>
            </q-card>
          </q-dialog>
        </q-toolbar-title>
      </q-toolbar>
    </q-footer>
  </q-layout>
</template>

<script>
import { copyToClipboard } from "quasar";
export default {
  data() {
    return {
      donate: false,
      cc_eth: "0x1d799bda34cc88844e952f893c82e50b61dce71d"
    };
  },
  methods: {
    copyEth(addy) {
      copyToClipboard(addy)
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
    }
  }
};
</script>

<style>
.link {
  text-decoration: none;
  color: darkorange;
}
</style>
