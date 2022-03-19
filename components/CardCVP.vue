<template lang="html">
  <v-card class="mx-auto" color="info" min-height="220" dark>
    <v-card-title>
      <v-avatar color="primary">
        <v-img class="elevation-6" src="./logo_moph.png"></v-img>
      </v-avatar>
      <span class="text-h6 font-weight-light pl-2"
        >ข้อมูลของกระทรวง(MOPH CVP)</span
      >
    </v-card-title>

    <v-card-text>
      <v-simple-table dense>
        <template v-slot:default>
          <tbody>
            <tr>
              <td>ชื่อ-นามสกุล :</td>
              <td>
                {{
                  data.patient
                    ? `${data.patient.prefix}${data.patient.first_name} ${data.patient.last_name}`
                    : ""
                }}
              </td>
            </tr>
            <tr>
              <td>ที่อยู่ :</td>
              <td v-if="data.patient">
                {{ data.patient.Address[0].Address }}
                {{
                  data.patient.Address[0].moo
                    ? `ม.${data.patient.Address[0].moo}`
                    : ""
                }}
                {{
                  data.patient.Address[0].tmb_name
                    ? `ต.${data.patient.Address[0].tmb_name}`
                    : ""
                }}
                {{
                  data.patient.Address[0].amp_name
                    ? `อ.${data.patient.Address[0].amp_name}`
                    : ""
                }}
                {{
                  data.patient.Address[0].chw_name
                    ? `จ.${data.patient.Address[0].chw_name}`
                    : ""
                }}
              </td>
            </tr>
          </tbody>
        </template>
      </v-simple-table>

      <v-dialog min-width="700" light>
        <template v-slot:activator="{ on, attrs }">
          JSON DATA <v-icon v-bind="attrs" v-on="on">mdi-code-json</v-icon>
        </template>

        <v-card min-width="500">
          <v-card-title class="text-h5 dark lighten-2">
            JSON DATA
          </v-card-title>
          <v-card-text>
            <vue-json-pretty :data="data"> </vue-json-pretty>
          </v-card-text>
        </v-card>
      </v-dialog>
    </v-card-text>
  </v-card>
</template>

<script>
export default {
  props: {
    data: {
      type: Object,
      default() {
        return {};
      }
    }
  }
};
</script>

<style lang="css" scoped></style>
