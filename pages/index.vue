<template lang="html">
  <v-row justify="center" align="center">
    <v-col cols="12" justify="center" align="center">
      <!-- <v-col cols="12"> -->
      <VuetifyLogo />
      <v-col lg="6" xs="12">
        <v-card class="justify-center">
          <v-card-title class="headline justify-center"
            >เข้าสู่ระบบ
          </v-card-title>
          <v-card-text>
            <form @submit="login">
              <v-text-field
                v-model="username"
                :rules="[v => !!v || 'กรุณาระบุ username']"
                label="username"
                required
              ></v-text-field>
              <v-text-field
                v-model="password"
                type="password"
                :rules="[v => !!v || 'กรุณาระบุ password']"
                label="password"
                required
              ></v-text-field>
              <v-btn type="submit" :loading="submitBtn" color="success">
                เข้าสู่ระบบ
              </v-btn>
            </form>
          </v-card-text>
          <p class="text-center yellow--text">
            ใช้ username , password ของระบบ HOSxP ของ รพ.
          </p>
        </v-card>
      </v-col>
    </v-col>
  </v-row>
</template>

<script>
import * as DataApi from "@/utils/dataApi";
export default {
  data: () => ({
    valid: true,
    username: "",
    password: "",
    submitBtn: false
  }),
  methods: {
    async login(e) {
      e.preventDefault();
      this.submitBtn = true;
      const payload = {
        username: this.username,
        password: this.password
      };
      // console.log(payload);
      let rs = await DataApi.login(payload);
      if (rs.data.ok) {
        sessionStorage.setItem("name_login", rs.data.data.name);
        sessionStorage.setItem("auth_token", rs.data.token);
        this.$router.push("/import");
      } else {
        this.submitBtn = false;
        sessionStorage.removeItem("name_login");
        sessionStorage.removeItem("auth_token");
        this.$swal({
          icon: "error",
          title: "เกิดข้อผิดพลาด",
          text: rs.data.message,
          footer:
            '<span style="color: red;">ใช้ username , password ของระบบ HOSxP ของ รพ.</span>'
        });
      }
    }
  }
};
</script>

<style lang="css" scoped></style>
