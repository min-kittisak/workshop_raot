# 🚀 Workshop: Deploy .NET + Vue บน Linux VM

---
### FYI
หลังจากจบกิจกรรม Workshop (16:00 - 16:30 น.) ข้อมูลทั้งหมด รวมถึง VM จะถูกลบทิ้งทันที
---

### Users สำหรับใช้เข้า VM
👤 [Workshop Users](https://docs.google.com/spreadsheets/d/1e1bCiAIanqBuPEPmLy0a--kssAINRyEUJ7oKzu6RJ7E/edit?gid=18468552#gid=18468552)

---
### แก้ไขปัญหากรณี Remote SSH เข้า VM ไม่ได้
เปลี่ยน DNS เป็น Cloudflare `1.1.1.1`
Windows

---

### วิธีการ Copy & Paste บน CMD / Console
```bash
Copy => คลุมดำที่ข้อความที่ต้องการแล้วกดคลิกขวา (Right-Click)
Paste => คลิกขวา (Right-Click) หรือ Shift+Insert
```
---

### แก้ไข Code ของ Frontend
#### .env โดย XX แทนด้วยเลข User ของเรา
```bash
VITE_API_URL=https://app.workshop-deploy.site/user999/api/api
VITE_BASE_PATH=/userXX/app/
```

#### src/router/index.ts
เพิ่ม import.meta.env.BASE_URL
```ts
import.meta.env.BASE_URL
```
---
```ts
import {createRouter , createWebHistory} from 'vue-router'
import UserManagement from '../views/UserManagement.vue'

const router = createRouter({
    history: createWebHistory(import.meta.env.BASE_URL), // <-- แก้ไขบรรทัดนี้
    routes:[{
        path: '/',
        name: 'Users',
        component: UserManagement
    }]
})
export default router
```
---

## ข้อมูลที่ต้องรู้ก่อน

| | ค่า |
|---|---|
| Server | `app.workshop-deploy.site` |
| Username | `user01` – `user20` |
| Password | จาก Sheet Workshop User |
| API URL | `https://app.workshop-deploy.site/userXX/api/` |
| Swagger | `https://app.workshop-deploy.site/userXX/api/swagger` |
| Frontend URL | `https://app.workshop-deploy.site/userXX/app/` |
| Database | `userXX_db` |

> แทน `userXX` ด้วย username ของคุณตลอดทั้งเอกสารนี้

---

## ขั้นตอน 1 — เปิด Terminal

**Windows:** กด `Win + R` พิมพ์ `cmd` แล้วกด Enter  
**Mac / Linux:** เปิด Terminal ได้เลย

---

## ขั้นตอน 2 — SSH เข้า Server

```bash
ssh userXX@79.108.225.69
```

ระบบจะถามรหัสผ่าน — พิมพ์แล้วกด Enter (ตัวอักษรจะไม่แสดง)

เมื่อเข้าสำเร็จจะเห็นข้อมูลของผู้ใช้นั้น ๆ

```
══════════════════════════════════════════════════════════
  Welcome to Workshop! 🚀
  Username    : userXX
  API URL     : https://app.workshop-deploy.site/userXX/api/
  Swagger     : https://app.workshop-deploy.site/userXX/api/swagger
  Frontend URL: https://app.workshop-deploy.site/userXX/app/
  Database    : user01_db
  DB ConnStr  : Host=localhost;Port=5432;Database=userXX_db;
                Username=userXX;Password=Workshop@2025
══════════════════════════════════════════════════════════
```

ดูข้อมูลนี้ซ้ำได้ตลอดด้วย
```bash
cat ~/.welcome
```

---

## ขั้นตอน 3 — Clone Project

### Clone ทั้ง API และ Frontend

```bash
git clone <URL-ของ-API-repo>
git clone <URL-ของ-Frontend-repo>
```

ตรวจสอบว่า clone มาแล้ว
```bash
ls
```

จะเห็น folder ของทั้งสอง project ขึ้นมา เช่น
```
my-dotnet-api   my-vue-app
```

---

## ขั้นตอน 4 — ตั้งค่า Connection String ใน API

ก่อน deploy ต้องให้ .NET รู้ว่าจะเชื่อมต่อ database ไหน แก้ไฟล์ `appsettings.json` ใน project API

```bash
cd my-dotnet-api
nano appsettings.json
```

หาบรรทัด `ConnectionStrings` แล้วแก้เป็น

```json
"ConnectionStrings": {
  "Default": "Host=localhost;Port=5432;Database=userXX_db;Username=userXX;Password=Workshop@2025"
}
```

กด `Ctrl+O` เพื่อบันทึก แล้ว `Ctrl+X` เพื่อออก

> ดู connection string ที่ถูกต้องของคุณได้จาก `cat ~/.db_connection`

---

## ขั้นตอน 5 — Deploy API (.NET)

```bash
cd ~/my-dotnet-api
deploy
```

รอสักครู่ระบบจะ build และ start ให้อัตโนมัติ เมื่อสำเร็จ

```
🌐 URL : https://app.workshop-deploy.site/userXX/api/
📝 Log : tail -f /tmp/api_userXX.log
```

ทดสอบเปิด Swagger ใน browser
```
https://app.workshop-deploy.site/userXX/api/swagger
```

---

## ขั้นตอน 6 — Deploy Frontend (Vue)

```bash
cd ~/my-vue-app
deploy
```

ครั้งแรกอาจใช้เวลา 1–2 นาที เพราะต้อง `npm install` ก่อน เมื่อเสร็จแล้ว

```
✅  Frontend
🌐 URL : https://app.workshop-deploy.site/userXX/app/
📝 Log : tail -f /tmp/fe_userXX.log
```

เปิด URL ใน browser ได้เลย

---

## ขั้นตอน 7 — ตรวจสอบสถานะ

```bash
mystatus
```

```
  .NET API : ● RUNNING  →  https://app.workshop-deploy.site/userXX/api/
  Frontend : ● RUNNING  →  https://app.workshop-deploy.site/userXX/app/
```

---

## อัปเดต Code ใหม่

เมื่อแก้ code และ push ขึ้น Git แล้ว ทำแบบนี้เพื่ออัปเดตบน server

```bash
# อัปเดต API
cd ~/my-dotnet-api
git pull
deploy

# อัปเดต Frontend
cd ~/my-vue-app
git pull
deploy
```

---

## คำสั่งทั้งหมด

| คำสั่ง | ความหมาย |
|--------|----------|
| `deploy` | Build + Start app (auto-detect .NET / Vue) |
| `stopapp` | หยุด app ทั้งหมด |
| `mystatus` | ดูสถานะ app |
| `cat ~/.welcome` | ดูข้อมูล URL และ port ของตัวเอง |
| `cat ~/.db_connection` | ดู database connection string |

---

## ดู Log เมื่อมีปัญหา

```bash
# Log ของ .NET API
tail -f /tmp/api_userXX.log

# Log ของ Frontend
tail -f /tmp/fe_userXX.log
```

กด `Ctrl+C` เพื่อหยุดดู log

---

## Error ที่พบบ่อย

| อาการ | วิธีแก้ |
|-------|--------|
| `Build failed!` | ดู error ด้านบน แล้วแจ้งผู้บรรยาย |
| `Start ไม่ได้` | `tail -f /tmp/api_userXX.log` ดู error |
| หน้าเว็บขาว (blank page) | `tail -f /tmp/fe_userXX.log` ดู error |
| ข้อมูลไม่ขึ้นใน Vue | ตรวจ API URL ใน config ของ frontend |
| `permission denied` | แจ้ง ผู้บรรยาย |
| `could not find .csproj` | ลองเช็คว่าเข้า folder project ถูกไหม |

---

## สรุป Workflow ทั้งหมด

```
เปิด Terminal (cmd)
       ↓
ssh userXX@app.workshop-deploy.site
       ↓
git clone <api-repo>
git clone <frontend-repo>
       ↓
cd my-dotnet-api → แก้ appsettings.json ใส่ connection string
       ↓
cd ~/my-dotnet-api && deploy   → ได้ API URL
       ↓
cd ~/my-vue-app && deploy      → ได้ Frontend URL
       ↓
mystatus → ตรวจสอบ RUNNING ทั้งคู่
       ↓
เปิด browser ทดสอบ 🎉
```
