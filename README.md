# excle-cvp-moph

## Build Setup

```bash
# 1.ติดตั้ง package
$ npm install

# สำหรับพัฒนา
$ npm run dev

# สำหรับ node run
$ npm run build
$ npm run start

# ***แนะนำ generate static สำหรับ run แบบ static เอาไฟล์ใน /dist ไปวางใน webserver
$ npm run generate
```

# ปรับตั้งค่า ไฟล์ nuxt.config.js url ที่เป็น run[api] https://github.com/nirawit-git/autocheck_cid_moph_cvp
```bash
 env: {
        baseURL: "http://127.0.0.1:4601",
        apiCIDReader: "http://localhost:8189/api/smartcard"
  }
```
