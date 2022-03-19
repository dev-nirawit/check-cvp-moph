<template>
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
    <div class="pl-3" v-show="name_login">
      <v-btn color="secondary" small rounded dark @click="logout">
        <v-icon>mdi-exit-to-app</v-icon> ออกจากระบบ
      </v-btn>
    </div>
  </v-footer>
</template>

<script>
export default {
  data: () => ({
    name_login: "",
  }),
  mounted() {
    window.setInterval(() => {
      this.name_login = sessionStorage.getItem("name_login");
    }, 2000);
  },
  methods: {
    async logout() {
      this.$swal({
        title: "คุณแน่ใจหรือไม่?",
        html: `ต้องการ ออกจากระบบ หรือไม่!`,
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "ตกลง",
        cancelButtonText: "ยกเลิก",
        allowOutsideClick: false,
      }).then(async (result) => {
        if (result.isConfirmed) {
          await sessionStorage.clear();
          window.location.href = "/";
        }
      });
    },
  },
};
</script>