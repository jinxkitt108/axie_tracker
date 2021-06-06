<template>
  <q-layout view="hHh lpR fFf">

    <q-header elevated class="bg-dark text-white">
      <q-toolbar>
        <q-toolbar-title>
          <q-avatar v-if="!$q.platform.is.electron" square>
            <img src="icons/favicon-128x128.png">
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
        <q-toolbar-title class="text-center">
          <p class="text-caption">Play to Earn. <a class="link" href="https://axieinfinity.com/">Axie Infinity</a>  gaming!</p>
        </q-toolbar-title>
      </q-toolbar>
    </q-footer>

  </q-layout>
</template>

<script>
export default {
  data () {
    return {
    }
  }
}
</script>

<style>
  .link {
    text-decoration: none;
    color: darkorange;
  }
</style>