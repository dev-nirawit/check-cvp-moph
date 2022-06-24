<template>
  <v-row justify="center" align="center">
    <v-col cols="12" justify="center" align="center" class="pt-0">
      <v-col cols="12" class="pt-0">
        <v-card class="logo d-flex justify-center">
          <VuetifyLogo />
        </v-card>
        <v-card>
          <v-card-title class="headline">
            {{
              dose_sum > 0
                ? `ได้รับวัคซีนแล้ว ${dose_sum} DOSE`
                : "UPDATE CID รายบุคคล"
            }}
            <v-chip
              class="ma-2"
              :color="status_readcid_in ? 'success' : 'primary'"
              >{{ status_readcid_in ? "เสียบบัตรแล้ว" : "รออ่านบัตร" }}
              <v-icon right>
                {{
                  status_readcid_in
                    ? "mdi-checkbox-marked-circle"
                    : "mdi-cached"
                }}
              </v-icon></v-chip
            >

            <v-btn
              rounded
              color="pink"
              href="http://61.19.87.252:10733/down/l4tWLL3nrq8d"
              target="_blank"
            >
              <v-icon left>
                mdi-cloud-download
              </v-icon>
              โปรแกรมติดตั้ง อ่านบัตร
            </v-btn>
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
            <v-row class="d-flex justify-center mt-0">
              <v-col lg="3" xs="12">
                <CardSticker :data="cardStickerData" :patient="cardHISData" />
              </v-col>
            </v-row>
            <!-- <div v-if="checkDataStatus && readCardData">
                <p class="headline">
                  {{ readCardData.titleName }}{{ readCardData.fname }}
                  {{ readCardData.lname }}
                </p>
              </div> -->
            <!-- <v-row v-if="checkDataStatus && patient && !readCardData">
                <v-col cols="12" class="text-h5 pb-0 mt-n10">
                  {{ patient.pname }}{{ patient.fname }}
                  {{ patient.lname }}</v-col
                >
                <v-col cols="12" class="text-h5 mt-n2"
                  >HN : {{ patient.hn }}</v-col
                >
              </v-row> -->
            <v-card v-if="checkDataStatus">
              <v-card-text>
                <v-data-table
                  v-if="displayDataApiManualRes.vaccine_certificate[0]"
                  dense
                  :headers="headers"
                  :items="
                    displayDataApiManualRes.vaccine_certificate[0]
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
            <!-- <form @submit="validateManual">
              <v-text-field
                v-model="manualCID"
                :counter="13"
                :rules="[
                  v => !!v || 'กรุณาระบุ เลขบัตรประชาชน ด้วยค่ะ!',
                  v =>
                    (v && v.length == 13) || 'กรุณาระบุ เลขบัตรประชาชน 13 หลัก'
                ]"
                label="เลขบัตรประชาชน 13 หลัก"
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

              <div v-for="v in displayTextApiManualRes">
                <v-alert v-if="v.ok" dense text :type="v.status_color">
                  {{ v.Message }}
                </v-alert>
                <v-alert v-else v-show="v.Message" dense text type="error">
                  {{ v.Message }}
                </v-alert>
              </div>
              <v-divider class="ma-2"></v-divider>
              <v-btn
                block
                :loading="sendApiManualMessage"
                :disabled="!manualValid"
                color="primary"
                class="mr-4"
                @click="validateManualMessage"
              >
                ส่งข้อมูลแจ้งเตือน หมอพร้อม
                <template v-slot:loader>
                  <span>กำลังส่งข้อมูลแจ้งเตือน...</span>
                </template>
              </v-btn>
            </form> -->
            <!-- </v-form> -->
            <v-tabs slider-size="5" color="info" centered>
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
                      <v-btn
                        block
                        class="mr-4"
                        color="error"
                        @click="clearInput"
                      >
                        ล้างค่า
                      </v-btn>
                      <v-divider class="ma-2"></v-divider>
                      <v-btn
                        block
                        small
                        :loading="sendApiManualMessage"
                        :disabled="!manualValid"
                        color="primary"
                        @click="validateManualMessage"
                      >
                        ส่งข้อมูลแจ้งเตือน หมอพร้อม
                        <template v-slot:loader>
                          <span>กำลังส่งข้อมูลแจ้งเตือน...</span>
                        </template>
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
                      <v-divider class="ma-2"></v-divider>
                      <v-btn block class="mr-4" @click="clearInput">
                        ล้างค่า
                      </v-btn>
                    </form>
                  </v-card-text>
                </v-card>
              </v-tab-item>
            </v-tabs>
            <div v-for="v in displayTextApiManualRes">
              <v-alert v-if="v.ok" dense text :type="v.status_color">
                {{ v.Message }}
              </v-alert>
              <v-alert v-else v-show="v.Message" dense text type="error">
                {{ v.Message }}
              </v-alert>
            </div>
          </v-card-text>
        </v-card>
      </v-col>
    </v-col>

    <v-col cols="12" justify="center" align="center">
      <v-col cols="12">
        <v-card>
          <v-card-title class="headline">
            Upload ไฟล์ Excel ของท่าน
          </v-card-title>
          <v-card-text>
            <!-- <input type="file" accept="xlsx/*" @change="inputChanged" /> -->
            <v-file-input
              accept=".xlsx"
              label="เลือกไฟล์ (.xlsx)"
              v-model="ffile"
              @change="inputChanged"
            ></v-file-input>
            <p v-show="filsUploadStatus != null" class="error--text mt-n3 pt-0">
              {{ filsUploadStatus ? "" : "กรุณาเลือกไฟล์ สกุล .xlsx เท่านั้น" }}
            </p>
            <v-card-actions>
              <v-btn
                block
                color="success"
                :disabled="!filsUploadStatus"
                @click="dialog = true"
              >
                ดูข้อมูล
              </v-btn>
            </v-card-actions>
          </v-card-text>
        </v-card>
      </v-col>
    </v-col>

    <v-dialog
      v-model="dialog"
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
    >
      <v-card>
        <v-toolbar dark color="primary">
          <v-btn icon dark @click="closeExcle">
            <v-icon>mdi-close</v-icon>
          </v-btn>
          <v-toolbar-title>ตารางข้อมูล</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-toolbar-items>
            <v-btn dark text @click="closeExcle"> ปิด </v-btn>
          </v-toolbar-items>
        </v-toolbar>
        <v-card-text>
          <v-row>
            <v-col cols="12">
              <v-data-table
                :headers="dataHeader"
                :items="dataList"
                :items-per-page="-1"
                class="elevation-12 mt-4"
                dense
              >
                <template v-slot:item="{ item }">
                  <tr>
                    <td
                      v-for="(v, i) in item"
                      :key="i"
                      v-html="displayTextApiRes(v)"
                      class="text-body-2"
                    ></td>
                  </tr>
                </template>
              </v-data-table>
            </v-col>
            <!-- <v-col cols="4">
              <h3>Log</h3>
              <v-virtual-scroll height="100" item-height="20"
                >
              </v-virtual-scroll>
            </v-col> -->
          </v-row>
          <v-row align="center" class="mt-4">
            <v-col cols="12">
              <div class="text-center">
                <v-chip class="ma-2" color="success" outlined>
                  <v-icon left>
                    mdi-database
                  </v-icon>
                  รวม : {{ sum_excle.raw_total }}
                </v-chip>

                <v-chip class="ma-2" color="warning" outlined pill>
                  <v-icon left>
                    mdi-database-eye
                  </v-icon>
                  พบข้อมูลวัคซีน : {{ sum_excle.cvp_have_data }}
                </v-chip>

                <v-chip class="ma-2" color="grey" outlined>
                  <v-icon left>
                    mdi-database-eye-off-outline
                  </v-icon>
                  ไม่พบข้อมูลวัคซีน : {{ sum_excle.cvp_no_vaccine }}
                </v-chip>

                <v-chip class="ma-2" color="pink darken-3" outlined>
                  <v-icon left>
                    mdi-null
                  </v-icon>
                  cid ไม่ถูกต้อง หรือ ไม่มีข้อมูล:
                  {{ sum_excle.cvp_null }}
                </v-chip>
              </div>
            </v-col>

            <v-col cols="12">
              <v-select
                v-model="select"
                :items="dataHeader"
                label="กรุณาเลือก Fild เลขบัตรประชาชน หรือ cid"
                persistent-hint
                return-object
                single-line
                dense
                outlined
              ></v-select>
              <!-- {{ dataHeader.indexOf(select) }} -->
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-row>
            <v-btn
              block
              :disabled="checkSelect()"
              color="success"
              @click="sendAPI()"
            >
              ตรวจสอบ [{{ select.text }}] TO API CVP MOPH
            </v-btn>
            <v-divider class="ma-2"></v-divider>
            <v-btn block color="primary" @click="sendApiExcleMessage()">
              ส่งข้อมูลแจ้งเตือน หมอพร้อม
            </v-btn>
          </v-row>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>
<script>
import readXlsxFile from "read-excel-file";
import * as DataApi from "@/utils/dataApi";
import axios from "axios";
export default {
  layout: "import",
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
    displayDataApiManualRes: [],
    checkDataStatus: null,
    checkDataErrorStatus: false,
    sendApiManual: false,
    sendApiManualMessage: false,
    status_readcid_in: false,
    status_readcid_out: false,
    readCid: null,
    readCardData: null,
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
    patient: {},
    cardCVPData: {},
    cardHISData: {},
    cardStickerData: {},
    manualPassport: "",
    sendApiManualPassport: false,
    manualValidPassport: true,
    nationality: [],
    selectNationality: null,
    datacheckPasstportAPI: null,
    sum_excle: {
      raw_total: 0,
      cvp_have_data: 0,
      cvp_no_vaccine: 0,
      cvp_null: 0
    }
  }),
  created() {
    if (!sessionStorage.getItem("auth_token")) {
      window.location.href = "/";
    } else {
      this.getDataNationality();
      // this.initSmartCard();
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
    async inputChanged() {
      this.sum_excle.raw_total = 0;
      this.sum_excle.cvp_have_data = 0;
      this.sum_excle.cvp_no_vaccine = 0;
      this.sum_excle.cvp_null = 0;
      // console.log("e", e);
      // console.log("file type=", this.ffile.type);
      const xlsxfile = event.target.files ? event.target.files[0] : null;
      console.log("#xlsxfile", xlsxfile);
      try {
        this.select = {};
        this.result_count_success = 0;
        this.result_count_warning = 0;
        this.result_count_non_check = 0;
        if (
          this.ffile.type.match(
            "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
          )
        ) {
          console.log("xlsx matched");
          this.filsUploadStatus = true;

          // console.log("xlsxfile", xlsxfile);
          // readXlsxFile(xlsxfile).then(rows => {
          //   console.log("rows:", rows);
          //   this.dataXlsx = rows;
          // });
          const data = await readXlsxFile(xlsxfile);

          console.log("dataXlsx", data);
          await this.converDataForTable(data);
          // this.dialog = true;
        } else {
          console.log("not matched");
          this.filsUploadStatus = false;
        }
      } catch (e) {
        console.log("error", e);
      }
    },
    onFileChange(event) {
      let xlsxfile = event.target.files ? event.target.files[0] : null;
      readXlsxFile(xlsxfile).then(rows => {
        console.log("rows:", rows);
      });
    },
    converDataForTable(data) {
      this.sum_excle.raw_total = data.length - 1;
      this.dataHeader = [];
      data[0].forEach(v => {
        // console.log("v: " + v);
        this.dataHeader.push({
          text: v,
          align: "start",
          sortable: false,
          class: "text-body-1"
        });
      });
      this.dataHeader.push({
        text: "ส่งข้อมูล API",
        align: "start",
        sortable: false,
        class: "text-body-1"
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
    async sendAPI() {
      // console.log("sendAPI", this.dataHeader.indexOf(this.select));
      // this.dataList.forEach(async v => {
      //   // console.log(v[this.dataHeader.indexOf(this.select)]);
      //   const cid = v[this.dataHeader.indexOf(this.select)];
      //   const d = await DataApi.checkDataToDB(cid);
      //   console.log("checkDataToDB", d);
      // });

      let i = 0;
      for await (const v of this.dataList) {
        const cid = v[this.dataHeader.indexOf(this.select)];
        await this.dataList[i].push("in process...");
        const data = await DataApi.checkDataToDB(cid);
        console.log("checkDataToDB", data);
        let dataStatus = [];

        if (
          data.data.data_rs[0].MessageCode == 200 ||
          data.data.data_rs[0].MessageCode == 201
        ) {
          // พบข้อมูลวัคซีน
          this.sum_excle.cvp_have_data++;
        } else if (data.data.data_rs[0].MessageCode == 501) {
          // ไม่พบข้อมูลวัคซีน
          this.sum_excle.cvp_no_vaccine++;
        } else {
          // {} เลขบัตรประชาชน ไม่ถูกต้อง หรือ ไม่่พบข้อมูล
          this.sum_excle.cvp_null++;
        }
        // for await (const d of data.data) {
        //   if (d.data_rs) {
        //     if()
        //   }
        // }

        await this.dataList[i].pop();
        await this.dataList[i].push({ rs_api: data.data.data_rs });
        console.log("dataList[i]", this.dataList[i]);

        i = i + 1;
      }
    },
    async sendApiExcleMessage() {
      let i = 0;
      for await (const v of this.dataList) {
        const cid = v[this.dataHeader.indexOf(this.select)];
        await this.dataList[i].push("in process...");
        const data = await this.sendMessage(cid);
        // console.log("sendApiExcleMessage", data);
        let dataStatus = [];
        await this.dataList[i].pop();
        await this.dataList[i].push({ rs_api: data.data });
        // console.log("dataList[i]", this.dataList[i]);
        i = i + 1;
      }
    },
    displayTextApiRes(v) {
      // console.log("v", v);
      let text_html = "";
      if (v != undefined) {
        console.log(v);
        if (v.rs_api) {
          if (v.rs_api.MessageCode == 501) {
            text_html += `<span style="color:white;background-color: red;padding: 4px 5px;margin-right: 5px;border-radius: 25px;">${v.rs_api.Message}</span>`;
          } else {
            v.rs_api.forEach((d, i) => {
              if (d.MessageCode == 200) {
                text_html += `<span style="color:green;background-color: white;padding: 4px 5px;margin-right: 5px;border-radius: 25px;">${d.Message}</span>`;
              } else if (d.MessageCode == 201) {
                text_html += `<span style="color:orange;background-color: white;padding: 4px 5px;margin-right: 5px;border-radius: 25px;">${d.Message}</span>`;
              } else {
                text_html += `<span style="color:white;background-color: red;padding: 4px 5px;margin-right: 5px;border-radius: 25px;">${d.Message}</span>`;
              }
            });
          }
          return text_html;
        } else {
          return v;
        }
      } else {
        return;
      }
    },
    async validateManual(e) {
      // if (this.$refs.form.validate()) {
      e.preventDefault();
      // console.log("ok");
      // console.log("manualCID", this.manualCID);
      this.sendApiManual = true;
      // const data = await DataApi.checkDataToDB(this.manualCID);
      // // console.log("checkDataToDB", data);
      // if (data.data) {
      //   this.displayTextApiManualRes = data.data;
      // }
      // this.sendApiManual = false;
      this.checkDatAPI(this.manualCID);
      // }
    },
    async validateManualMessage() {
      if (this.$refs.form.validate()) {
        this.sendApiManualMessage = true;
        const data = await this.sendMessage(this.manualCID);
        console.log(data);
        this.sendApiManualMessage = false;
      }
    },
    async sendMessage(cid) {
      const localMassageStore = JSON.parse(
        localStorage.getItem("massageStore")
      );
      const payload = {
        hospital: {
          hospital_code: localMassageStore.hospital.hospital_code,
          hospital_name: localMassageStore.hospital.hospital_name,
          his_identifier: localMassageStore.hospital.his_identifier
        },
        message_ref_code: cid,
        message_title: localMassageStore.message_title,
        message_content_html: localMassageStore.message_content_html,
        target: [cid]
      };
      const data = await DataApi.sendAPIMessageMoph(payload);
      console.log("sendAPIMessageMoph", data);
    },
    goodEmptyCheck(value) {
      Object.keys(value).length === 0 && value.constructor === Object; // 👈 constructor check
    },
    async checkDatAPI(cid) {
      this.dose_sum = 0;
      this.patient = {};
      this.displayTextApiManualRes = [];
      const data = await DataApi.checkDataToDB(cid);
      if (
        (data.data.MessageCode !== undefined) == true &&
        data.data.MessageCode != 200
      ) {
        this.$swal({
          icon: "error",
          title: `เกิดข้อผิดพลาด ${data.data.MessageCode || "ไม่พบข้อมูล!"}`,
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
        if (data.data.ok == false) {
          this.$swal({
            icon: "error",
            title: `เกิดข้อผิดพลาด ไม่พบข้อมูล!`,
            footer:
              '<span style="color: red;">หรือกรุณาติดต่อ เจ้าหน้าที่ หรือผู้ดูแลระบบ</span>'
          });
          this.clearInput();
        } else {
          if (data.data.patient) {
            console.log("data.data.patient", data.data.patient);
            this.cardHISData = data.data.patient;
          } else {
            this.cardHISData = {};
            // this.cardStickerData = {};
          }
          const res_data = data.data.api_data.result;
          this.cardCVPData = res_data;
          this.patient = data.data.patient;
          this.cardStickerData = data.data.data_sticker;

          // this.cardHISData = data.data.patient;
          if (res_data.vaccine_certificate) {
            this.checkDataErrorStatus = false;
            this.checkDataStatus = true;
            this.displayDataApiManualRes = res_data;
            this.displayTextApiManualRes = data.data.data_rs;
            this.sendApiManual = false;
            this.dose_sum =
              res_data.vaccine_certificate[0].vaccination_list.length || 0;
            if (!data.data.patient) {
              this.cardHISData = {
                pname: res_data.patient.prefix,
                fname: res_data.patient.first_name,
                lname: res_data.patient.last_name,
                birthday: res_data.patient.birth_date
              };
            }
            console.log(this.cardHISData);
          } else {
            this.checkDataErrorStatus = true;
            this.checkDataStatus = false;
            this.sendApiManual = false;
          }
          // console.log("setAutoPrint", localStorage.getItem("setAutoPrint"));
          if (localStorage.getItem("setAutoPrint").toLowerCase() === "true") {
            setTimeout(() => {
              this.$nuxt.$emit("printAuto");
            }, 500);
          }
        }
      }
    },
    async readSmartcard() {
      var config = {
        method: "get",
        url: process.env.apiCIDReader,
        headers: {}
      };
      axios(config)
        .then(v => {
          // console.log(v);
          if (this.status_readcid_in == false) {
            console.log("บัตรใหม่ครั้งแรก");
            this.status_readcid_in = true;
            this.status_readcid_out = false;
            this.readCid = v.data.pid;
            this.readCardData = v.data;
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
              this.readCardData = null;
              this.clearInput();
            }
          }
        });
    },
    closeExcle() {
      this.$swal({
        title: "คุณแน่ใจหรือไม่?",
        html: `ต้องการ ปิด หรือไม่!`,
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "ตกลง",
        cancelButtonText: "ยกเลิก",
        allowOutsideClick: false
      }).then(async result => {
        if (result.isConfirmed) {
          await this.clearInput();
          this.dialog = false;
          this.sum_excle.raw_total = 0;
          this.sum_excle.cvp_have_data = 0;
          this.sum_excle.cvp_no_vaccine = 0;
          this.sum_excle.cvp_null = 0;
        }
      });
    },
    clearInput() {
      this.manualCID = null;
      this.sendApiManual = null;
      this.checkDataStatus = null;
      this.displayTextApiManualRes = [];
      this.dose_sum = 0;
      this.ffile = [];
      this.dataList = [];
      this.patient = {};
      this.checkDataErrorStatus = false;
      this.cardStickerData = {};
      this.cardCVPData = {};
      this.cardHISData = {};
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
