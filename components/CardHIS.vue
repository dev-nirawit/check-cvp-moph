<template lang="html">
  <v-card class="mx-auto" color="primary" min-height="220" dark>
    <v-card-title>
      <v-avatar color="primary">
        <v-img class="elevation-6" src="./icon.png"></v-img>
      </v-avatar>
      <span class="text-h6 font-weight-light pl-2">ข้อมูลของโรงพยาบาล</span>
    </v-card-title>

    <v-card-text>
      <v-simple-table dense>
        <template v-slot:default>
          <tbody>
            <tr>
              <td>ชื่อ-นามสกุล :</td>
              <td>{{ data.pname }}{{ data.fname }} {{ data.lname }}</td>
            </tr>
            <tr>
              <td>ที่อยู่ :</td>
              <td>
                {{ data.addrpart }}
                {{ data.moopart ? `ม.${data.moopart}` : "" }}
                {{ data.full_name }}
              </td>
            </tr>
            <tr>
              <td>HN :</td>
              <td>{{ data.hn }}</td>
            </tr>
            <tr>
              <td>Passport No. :</td>
              <td>{{ data.passport_no || "" }}</td>
            </tr>
          </tbody>
        </template>
      </v-simple-table>
      <!-- {{ data }} -->
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
