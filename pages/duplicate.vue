<template>
  <v-row justify="center" align="center">
    <v-col cols="12" justify="center" align="center">
      <!-- <v-col cols="12"> -->
      <v-card class="logo py-2 d-flex justify-center">
        <v-img
          max-height="250"
          max-width="250"
          src="./barcode-reader.jpg"
        ></v-img>
      </v-card>
      <v-card>
        <v-card-title class="headline">
          ตรวจสอบข้อมูล QR-CODE วัคซีนซ้ำ
        </v-card-title>
        <v-card-text>
          <!-- <v-form
            ref="form"
            class="text-center"
            v-model="manualValid"
            lazy-validation
          > -->
          <form @submit="validateManual">
            <v-card>
              <v-card-text>
                <v-data-table
                  dense
                  :headers="headers"
                  :items="items"
                  item-key="vaccine_dose_no"
                  class="elevation-1"
                  :mobile-breakpoint="0"
                  hide-default-footer
                >
                  <template v-slot:item.datetime="{ item }">
                    <span>{{
                      $moment(item.datetime)
                        .add(543, "Y")
                        .format("Do MMM YYYY H:MM")
                    }}</span>
                  </template>
                </v-data-table>
                <v-btn
                  v-if="items.length"
                  color="error"
                  class="mt-3"
                  large
                  dark
                  @click="delLabelCode"
                >
                  ลบ Label Code
                </v-btn>
              </v-card-text>
            </v-card>

            <v-alert
              v-if="checkDataErrorStatus"
              border="right"
              colored-border
              type="error"
              elevation="2"
            >
              ไม่พบข้อมูล
            </v-alert>

            <v-text-field
              v-model="label_code"
              :rules="[v => !!v || 'กรุณาระบุ label_code ด้วยค่ะ!']"
              label="label_code จาก QR-CODE วัคซีน"
              ref="inputfocus"
              required
            ></v-text-field>
            <v-btn
              block
              :loading="sendApiManual"
              :disabled="sendApiManual"
              color="success"
              class="mr-4"
              @click="validateManual"
              type="submit"
            >
              ตรวจสอบข้อมูล
              <template v-slot:loader>
                <span>กำลังส่งข้อมูลตรวจสอบ...</span>
              </template>
            </v-btn>
            <v-divider class="ma-2"></v-divider>
            <v-btn block class="mr-4" @click="clearInput"> ล้างค่า </v-btn>
          </form>
        </v-card-text>
      </v-card>
      <!-- </v-col> -->
    </v-col>
  </v-row>
</template>

<script>
import * as DataApi from "@/utils/dataApi";
export default {
  layout: "main",
  data: () => ({
    label_code: null,
    headers: [
      {
        text: "ชื่อ-สกุล",
        value: "fullname",
        sortable: false,
        align: "center",
        width: "200",
        class: "text-h6"
      },
      {
        text: "วันที่ - เวลา",
        value: "datetime",
        sortable: false,
        align: "center",
        width: "150",
        class: "text-h6"
      },
      {
        text: "HN",
        value: "hn",
        sortable: false,
        align: "center",
        width: "100",
        class: "text-h6"
      },
      {
        text: "VN",
        value: "vn",
        sortable: false,
        align: "center",
        width: "100",
        class: "text-h6"
      },
      {
        text: "Label Code",
        value: "label_code",
        sortable: false,
        align: "center",
        width: "150",
        class: "text-h6"
      }
    ],
    checkDataStatus: false,
    checkDataErrorStatus: false,
    sendApiManual: false,
    items: []
  }),
  mounted() {
    this.$refs.inputfocus.focus();
  },
  methods: {
    async validateManual(e) {
      e.preventDefault();
      if (this.label_code) {
        // alert('label_code'+this.label_code);
        let rs = await DataApi.checkLabelCode(this.label_code);
        if (rs.data.ok) {
          // console.log(rs.data.result);
          this.items = rs.data.result;
        } else {
          this.$swal({
            icon: "error",
            title: `เกิดข้อผิดพลาด`,
            html: rs.data.message
          });
        }
      }
    },
    delLabelCode() {
      this.$swal({
        title: "คุณแน่ใจหรือไม่?",
        html: `ต้องการ ลบ[${this.label_code}] หรือไม่!`,
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "ตกลง",
        cancelButtonText: "ยกเลิก",
        allowOutsideClick: false
      }).then(async result => {
        if (result.isConfirmed) {
          const rs = await DataApi.delLabelCode(this.label_code);
          if (rs.data.ok) {
            this.$swal({
              icon: "success",
              title: `ทำรายการสำเร็จ`,
              html: "บันทึกข้อมูลเรียบร้อยแล้ว"
            });
            this.clearInput();
          } else {
            this.$swal({
              icon: "error",
              title: `เกิดข้อผิดพลาด`,
              html: rs.data.result.message
            });
          }
        }
      });
    },
    clearInput() {
      this.sendApiManual = null;
      this.checkDataStatus = null;
      this.checkDataErrorStatus = false;
      this.label_code = null;
      this.items = [];
    }
  }
};
</script>

<style>
.v-data-table > .v-data-table__wrapper > table > thead > tr > th {
  color: yellow !important;
}
</style>
