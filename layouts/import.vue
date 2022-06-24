<template>
  <v-app dark>
    <Menu />
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
      <v-fab-transition>
        <!-- <v-btn color="info" large dark class="v-btn--transition">
          <v-icon class="pr-2">mdi-file-cog-outline</v-icon>
          ตั้งค่าข้อความแจ้งเตือน หมอพร้อม
        </v-btn> -->
        <v-badge
          bordered
          color="warning"
          icon="mdi-cog-sync"
          overlap
          class="v-btn--transition"
        >
          <v-btn
            class="white--text"
            color="info"
            depressed
            rounded
            @click="dialog = true"
          >
            ตั้งค่าข้อความแจ้งเตือน
          </v-btn>
        </v-badge>
      </v-fab-transition>
    </v-main>
    <Footer />

    <v-dialog v-model="dialog" persistent max-width="800px">
      <v-card>
        <v-card-title>
          <span class="text-h5">ตั้งค่าข้อความแจ้งเตือน หมอพร้อม</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-form ref="formSettingMassage" v-model="valid" lazy-validation>
              <v-card elevation="6">
                <v-card-text>
                  <v-text-field
                    v-model="massageStore.hospital.hospital_code"
                    label="hospital_code"
                    required
                  ></v-text-field>

                  <v-text-field
                    v-model="massageStore.hospital.hospital_name"
                    label="hospital_name"
                    required
                  ></v-text-field>

                  <v-text-field
                    v-model="massageStore.hospital.his_identifier"
                    label="his_identifier"
                    required
                  ></v-text-field>
                </v-card-text>
              </v-card>
              <v-col cols="12">
                <v-text-field
                  v-model="massageStore.message_title"
                  label="message_title"
                  required
                ></v-text-field>
                <v-textarea
                  v-model="massageStore.message_content_html"
                  outlined
                  label="ข้อความเนื้อหา ต้องเป็น Tag HTML"
                  :rules="[v => !!v || 'กรุณาใส่ข้อมูลด้วยค่ะ ']"
                  required
                ></v-textarea>
              </v-col>
            </v-form>
          </v-container>
        </v-card-text>
        <v-card-actions class="mt-n10">
          <v-btn color="blue darken-1" @click="cancelLocalStorage">
            ยกเลิก
          </v-btn>
          <v-spacer></v-spacer>
          <v-btn color="success darken-1" @click="setLocalStorage">
            บันทึกการตั้งค่า
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-app>
</template>

<script>
export default {
  data: () => ({
    valid: true,
    dialog: false,
    massageStore: {},
    massageStoreDefault: {
      hospital: {
        hospital_code: "10733",
        hospital_name: "โรงพยาบาลสมเด็จพระสังฆราช องค์ที่ 17",
        his_identifier: "HOSxP XE"
      },
      message_title: "หัวเรื่องข้อความ",
      message_content_html: "<h3>ทดสอบ</h3><br>ทดสอบระบบ</br>"
    }
  }),
  created() {
    this.setLocalStorageDefault();
  },
  mounted() {
    window.setInterval(() => {
      this.name_login = sessionStorage.getItem("name_login");
    }, 2000);
  },
  methods: {
    setLocalStorageDefault() {
      if (!localStorage.getItem("massageStore")) {
        localStorage.setItem(
          "massageStore",
          JSON.stringify(this.massageStoreDefault)
        );
        this.massageStore = this.massageStoreDefault;
      } else {
        this.massageStore = JSON.parse(localStorage.getItem("massageStore"));
      }

      if (!localStorage.getItem("setAutoPrint")) {
        localStorage.setItem("setAutoPrint", "false");
      }
    },
    setLocalStorage() {
      if (this.$refs.formSettingMassage.validate()) {
        localStorage.setItem("massageStore", JSON.stringify(this.massageStore));
        this.dialog = false;
        alert("บันทึกข้อมูลเรียบร้อยแล้ว");
      }
    },
    cancelLocalStorage() {
      this.massageStore = JSON.parse(localStorage.getItem("massageStore"));
      this.dialog = false;
    }
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
