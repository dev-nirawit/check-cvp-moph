<template>
  <v-row justify="center" align="center">
    <v-col cols="12" justify="center" align="center">
      <!-- <v-col cols="12"> -->
      <v-card class="logo d-flex justify-center">
        <VuetifyLogo />
      </v-card>
      <v-card>
        <v-card-title class="headline">
          {{
            dose_sum > 0
              ? `ได้รับวัคซีนแล้ว ${dose_sum} DOSE`
              : "ตรวจสอบข้อมูล ได้รับวัคซีน"
          }}
        </v-card-title>
        <v-card-text>
          <!-- <v-form
            ref="form"
            class="text-center"
            v-model="manualValid"
            lazy-validation
          > -->
          <v-row>
            <v-col lg="6" xs="12">
              <CardCVP :data="cardCVPData" />
            </v-col>
            <v-col lg="6" xs="12">
              <CardHIS :data="cardHISData" />
            </v-col>
          </v-row>

          <v-card v-if="checkDataStatus">
            <v-card-text>
              <v-data-table
                v-if="displayTextApiManualRes.vaccine_certificate[0]"
                dense
                :headers="headers"
                :items="
                  displayTextApiManualRes.vaccine_certificate[0]
                    .vaccination_list
                "
                item-key="vaccine_dose_no"
                class="elevation-1"
                hide-default-footer
              >
                <template v-slot:item.vaccine_dose_no="{ item }">
                  <span class="text-h5">{{ item.vaccine_dose_no }}</span>
                </template>

                <template v-slot:item.vaccine_date="{ item }">
                  <span class="text-h5">{{
                    $moment(item.vaccine_date)
                      .add(543, "Y")
                      .format("LL")
                  }}</span>
                </template>

                <template v-slot:item.vaccine_manufacturer_name="{ item }">
                  <span class="text-h5">{{
                    item.vaccine_manufacturer_name
                  }}</span>
                </template>

                <template v-slot:item.vaccine_lot_number="{ item }">
                  <span class="text-h5">{{ item.vaccine_lot_number }}</span>
                </template>

                <template v-slot:item.vaccine_place="{ item }">
                  <span class="text-h5">{{ item.vaccine_place }}</span>
                </template>
              </v-data-table>
            </v-card-text>
          </v-card>

          <v-alert
            v-if="checkDataErrorStatus"
            border="right"
            colored-border
            type="error"
            elevation="2"
          >
            ไม่พบข้อมูลได้รับวัคซีน
          </v-alert>

          <!-- <div v-for="v in displayTextApiManualRes">
                <v-alert v-if="v.ok" dense text :type="v.status_color">
                  {{ v.text }}
                </v-alert>
                <v-alert v-else v-show="v.Message" dense text type="error">
                  {{ v.Message }}
                </v-alert>
              </div> -->
          <v-tabs slider-size="5" min-width="30" centered color="info">
            <v-tab>
              <v-icon left>
                mdi-badge-account-horizontal-outline
              </v-icon>
              เลขบัตรประชาชน 13 หลัก
            </v-tab>
            <v-tab>
              <v-icon left>
                mdi-passport
              </v-icon>
              Passport
            </v-tab>

            <v-tab-item>
              <v-card flat>
                <v-card-text>
                  <form @submit="validateManual">
                    <v-text-field
                      v-model="manualCID"
                      :counter="13"
                      :rules="[
                        v => !!v || 'กรุณาระบุ เลขบัตรประชาชน ด้วยค่ะ!',
                        v =>
                          (v && v.length == 13) ||
                          'กรุณาระบุ เลขบัตรประชาชน 13 หลัก'
                      ]"
                      label="เลขบัตรประชาชน 13 หลัก"
                      ref="inputCid"
                      required
                    ></v-text-field>
                    <v-btn
                      block
                      :loading="sendApiManual"
                      :disabled="!manualValid"
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
                    <v-btn block class="mr-4" @click="clearInput">
                      ล้างค่า
                    </v-btn>
                  </form>
                </v-card-text>
              </v-card>
            </v-tab-item>
            <v-tab-item>
              <v-card flat>
                <v-card-text>
                  <form @submit="validateManualPassport">
                    <v-row>
                      <v-col lg="6">
                        <v-text-field
                          v-model="manualPassport"
                          :rules="[v => !!v || 'กรุณาระบุ พาสปอร์ต ด้วยค่ะ!']"
                          label="Passport No."
                          counter
                          required
                          ref="inputPassport"
                        ></v-text-field>
                      </v-col>
                      <v-col lg="6">
                        <v-autocomplete
                          v-model="selectNationality"
                          :items="nationality"
                          color="white"
                          item-text="name"
                          item-value="nhso_code"
                          label="กรุณาเลือกสัญชาติ"
                          :hint="
                            selectNationality
                              ? `nationality code : ${selectNationality}`
                              : null
                          "
                          required
                        ></v-autocomplete>
                      </v-col>
                    </v-row>
                    <v-col v-if="datacheckPasstportAPI"
                      >เลขบัตรประชาชน CVP :
                      {{ datacheckPasstportAPI.cid }}</v-col
                    >
                    <v-btn block type="submit" color="success">
                      ตรวจสอบข้อมูล
                      <template v-slot:loader>
                        <span>กำลังส่งข้อมูลตรวจสอบ...</span>
                      </template>
                    </v-btn>

                    <!-- <v-btn
                      block
                      :loading="sendApiManualPassport"
                      :disabled="!manualValidPassport"
                      color="success"
                      class="mr-4"
                      @click="validateManualPassport"
                      type="submit"
                    >
                      ตรวจสอบข้อมูล
                      <template v-slot:loader>
                        <span>กำลังส่งข้อมูลตรวจสอบ...</span>
                      </template>
                    </v-btn> -->
                    <v-divider class="ma-2"></v-divider>
                    <v-btn block class="mr-4" @click="clearInput">
                      ล้างค่า
                    </v-btn>
                  </form>
                </v-card-text>
              </v-card>
            </v-tab-item>
          </v-tabs>
          <!-- </v-form> -->
          <v-chip
            class="ma-2"
            :color="status_readcid_in ? 'success' : 'primary'"
            >{{ status_readcid_in ? "เสียบบัตรแล้ว" : "รออ่านบัตร" }}
            <v-icon right>
              {{
                status_readcid_in ? "mdi-checkbox-marked-circle" : "mdi-cached"
              }}
            </v-icon></v-chip
          >
          <p>
            สามารถเสียบบัตรประชาชนเพื่อนอ่านบัตรได้ โดยใช้ โปรแกรมอ่านบัตรที่นี้
            =>
            <a href="http://61.19.87.252:10733/down/l4tWLL3nrq8d">ดาวน์โหลด</a>
          </p>
        </v-card-text>
      </v-card>
      <!-- </v-col> -->
    </v-col>
  </v-row>
</template>
<script>
import * as DataApi from "@/utils/dataApi";
import axios from "axios";
export default {
  layout: "main",
  data: () => ({
    initing: null,
    dialog: false,
    filsUploadStatus: null,
    ffile: [],
    dataXlsx: [],
    message: "",
    dataHeader: [],
    dataList: [],
    select: {},
    result_count_success: 0,
    result_count_warning: 0,
    result_count_non_check: 0,
    manualValid: true,
    manualCID: "",
    displayTextApiManualRes: [],
    checkDataStatus: null,
    checkDataErrorStatus: false,
    sendApiManual: false,
    sendApiManualMessage: false,
    headers: [
      {
        text: "เข็มที่",
        value: "vaccine_dose_no",
        sortable: false,
        align: "center",
        width: "80",
        class: "text-h6"
      },
      {
        text: "วันที่",
        value: "vaccine_date",
        sortable: false,
        align: "center",
        width: "200",
        class: "text-h6"
      },
      {
        text: "ชื่อวัคซีน",
        value: "vaccine_manufacturer_name",
        sortable: false,
        align: "center",
        width: "200",
        class: "text-h6"
      },
      {
        text: "Lot Number",
        value: "vaccine_lot_number",
        sortable: false,
        align: "center",
        width: "150",
        class: "text-h6"
      },
      {
        text: "หน่วยบริการฉีดวัคซีน",
        value: "vaccine_place",
        sortable: false,
        align: "center",
        width: "300",
        class: "text-h6"
      }
    ],
    dose_sum: 0,
    status_readcid_in: false,
    status_readcid_out: false,
    readCid: null,
    cardCVPData: {},
    cardHISData: {},
    manualPassport: "",
    sendApiManualPassport: false,
    manualValidPassport: true,
    nationality: [],
    selectNationality: null,
    datacheckPasstportAPI: null
  }),
  created() {
    if (!sessionStorage.getItem("auth_token")) {
      window.location.href = "/";
    } else {
      this.getDataNationality();
      this.initSmartCard();
    }
  },
  beforeDestroy() {
    clearInterval(this.initing);
  },
  methods: {
    initSmartCard() {
      this.initing = setInterval(() => {
        this.readSmartcard();
      }, 1000);
    },
    converDataForTable(data) {
      this.dataHeader = [];
      data[0].forEach(v => {
        // console.log("v: " + v);
        this.dataHeader.push({ text: v, align: "start", sortable: false });
      });
      this.dataHeader.push({
        text: "ส่งข้อมูล API",
        align: "start",
        sortable: false
      });
      console.log("dataHeader", this.dataHeader);
      data.shift();
      this.dataList = data;
    },
    checkSelect() {
      if (this.dataHeader.indexOf(this.select) != "-1") {
        return false;
      } else {
        return true;
      }
    },
    async validateManual(e) {
      // if (this.$refs.form.validate()) {
      // console.log("ok");
      // console.log("manualCID", this.manualCID);
      e.preventDefault();
      if (this.manualCID) {
        this.sendApiManual = true;
        await this.checkDatAPI(this.manualCID);
      }
    },
    clearInput() {
      this.manualCID = null;
      this.sendApiManual = null;
      this.checkDataStatus = null;
      this.checkDataErrorStatus = false;
      this.dose_sum = 0;
      // Passport
      this.manualPassport = null;
      this.selectNationality = null;
      this.cardCVPData = {};
      this.cardHISData = {};
    },
    async checkDatAPI(cid) {
      this.dose_sum = 0;
      const data = await DataApi.checkData(cid);
      // console.log("checkDatAPI => ", data.data);

      if (
        (data.data.MessageCode !== undefined) == true &&
        data.data.MessageCode != 200
      ) {
        this.$swal({
          icon: "error",
          title: `เกิดข้อผิดพลาด ${data.data.MessageCode}`,
          html:
            data.data.MessageCode == 501
              ? '<span style="color: red;">เลขบัตรประชาชน ไม่ถูกต้อง!</span>'
              : data.data.Message,
          footer:
            '<span style="color: red;">หรือกรุณาติดต่อ เจ้าหน้าที่ หรือผู้ดูแลระบบ</span>'
          // timer: 2000,
          // timerProgressBar: true,
          // showConfirmButton: false,
        });
        this.clearInput();
      } else {
        if (data.data.patient) {
          console.log("data.data.patient", data.data.patient);
          this.cardHISData = data.data.patient;
        } else {
          this.cardHISData = {};
        }
        const res_data = data.data.api_data.result;
        this.cardCVPData = res_data;
        this.patient = data.data.patient;
        if (res_data.vaccine_certificate) {
          this.checkDataErrorStatus = false;
          this.checkDataStatus = true;
          this.displayTextApiManualRes = res_data;
          this.sendApiManual = false;
          this.dose_sum =
            res_data.vaccine_certificate[0].vaccination_list.length || 0;
        } else {
          this.checkDataErrorStatus = true;
          this.checkDataStatus = false;
          this.sendApiManual = false;
        }
      }
    },
    async readSmartcard() {
      axios
        .get(process.env.apiCIDReader)
        .then(v => {
          console.log(v);
          if (this.status_readcid_in == false) {
            console.log("บัตรใหม่ครั้งแรก");
            this.status_readcid_in = true;
            this.status_readcid_out = false;
            this.readCid = v.data.pid;
            this.manualCID = null;
            this.checkDatAPI(v.data.pid);
          } else if (
            (this.status_readcid_out == true) &
            (this.status_readcid_in == false)
          ) {
            console.log("บัตรใหม่");
            this.status_readcid_in = true;
            this.status_readcid_out = false;
            // this.readCid = v.data.pid;
          } else {
            console.log("บัตรยังเสียบอยู่");
          }
        })
        .catch(error => {
          if (error.response) {
            // console.log(error.response);
            // console.log(error.response.status);
            if (this.status_readcid_in == true) {
              console.log("ถอดบัตรออกแล้ว");
              this.status_readcid_out = true;
              this.status_readcid_in = false;
              this.readCid = null;
              this.clearInput();
            }
          }
        });
    },
    async getDataNationality() {
      const res = await DataApi.getNationality();
      if (res.data.ok) {
        // console.log(res.data.data);
        this.nationality = res.data.data;
      } else {
        console.log("getDataNationality : else");
      }
    },
    async validateManualPassport(e) {
      e.preventDefault();
      if (!this.manualPassport) {
        this.$refs.inputPassport.focus();
      }

      if (this.manualPassport && this.selectNationality) {
        this.sendApiManualPassport = true;
        const res = await DataApi.checkPasstportAPI(
          this.manualPassport,
          this.selectNationality
        );
        if (res.data.MessageCode == 200) {
          // console.log("result", res.data.result.passport);
          if (res.data.result.passport) {
            this.datacheckPasstportAPI = res.data.result.passport;
            await this.checkDatAPI(res.data.result.passport.cid);
          }
        } else {
          this.$swal({
            icon: "error",
            title: `เกิดข้อผิดพลาด ${data.data.MessageCode}`,
            html: data.data.Message
          });
        }
        // await this.checkPasstportAPI(this.manualCID);
      }
    }
  }
};
</script>

<style>
.v-data-table > .v-data-table__wrapper > table > thead > tr > th {
  /* font-size: 20px !important; */
  color: yellow !important;
}
</style>
