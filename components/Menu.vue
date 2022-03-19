<template>
  <v-navigation-drawer v-model="drawer" app>
    <v-list>
      <v-list-item
        v-for="[icon, text, url, color] in links"
        :key="icon"
        :to="url"
      >
        <v-list-item-icon>
          <v-icon :color="color">{{ icon }}</v-icon>
        </v-list-item-icon>

        <v-list-item-content>
          <v-list-item-title class="color">{{ text }}</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
    </v-list>
  </v-navigation-drawer>
</template>

<script>
export default {
  data: () => ({
    drawer: null,
    links: [
      ["mdi-hospital-box-outline", "ตรวจสอบวัคซีน", "/check", "primary"],
      ["mdi-table-arrow-left", "นำเข้าข้อมูล", "/import", "success"],
      ["mdi-alert", "ลบ QR-CODE วัคซีนซ้ำ", "/duplicate", "error"],
    ],
  }),
};
</script>