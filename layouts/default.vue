<template>
  <v-app dark>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer app class="pa-1">
      <v-avatar size="26">
        <img src="/icon.png" />
      </v-avatar>
      <small class="pl-1"
        >By IT Somdejprasangkharach XVII Hospital &copy;
        {{ new Date().getFullYear() }}
      </small>
      <v-spacer></v-spacer>
      <span class="pl-1 yellow--text">{{ name_login }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data: () => ({
    name_login: "-"
  }),
  mounted() {
    window.setInterval(() => {
      this.name_login = sessionStorage.getItem("name_login");
    }, 2000);
  }
};
</script>
<style>
.v-btn--transition {
  bottom: 0;
  right: 0;
  position: absolute;
  margin: 0px 10px 30px 0px;
}
</style>
