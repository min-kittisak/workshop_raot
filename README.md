# 🚀 Workshop: Deploy .NET + Vue บน Linux VM

---

## สารบัญ

- [ข้อมูลที่ต้องรู้ก่อน](#ข้อมูลที่ต้องรู้ก่อน)
- [ขั้นตอน 1 — เปิด Terminal](#ขั้นตอน-1--เปิด-terminal)
- [ขั้นตอน 2 — เข้า Terminal](#ขั้นตอน-2--เข้า-terminal)
- [ขั้นตอน 3 — Clone Project](#ขั้นตอน-3--clone-project)
- [ขั้นตอน 4 — ตั้งค่า Connection String ใน API](#ขั้นตอน-4--ตั้งค่า-connection-string-ใน-api)
- [ขั้นตอน 5 — Deploy API (.NET)](#ขั้นตอน-5--deploy-api-net)
- [ขั้นตอน 6 — Deploy Frontend (Vue)](#ขั้นตอน-6--deploy-frontend-vue)
- [ขั้นตอน 7 — ตรวจสอบสถานะ](#ขั้นตอน-7--ตรวจสอบสถานะ)
- [อัปเดต Code ใหม่](#อัปเดต-code-ใหม่)
- [คำสั่งทั้งหมด](#คำสั่งทั้งหมด)
- [ดู Log เมื่อมีปัญหา](#ดู-log-เมื่อมีปัญหา)
- [Error ที่พบบ่อย](#error-ที่พบบ่อย)
- [สรุป Workflow ทั้งหมด](#สรุป-workflow-ทั้งหมด)
- [แก้ไข Code Frontend](#แก้ไข-code-frontend)
- [วิธีการ Copy & Paste บน CMD / Terminal](#วิธีการ-copy--paste-บน-cmd--terminal)
- [Users และข้อมูล Workshop](#users-และข้อมูล-workshop)

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
| **Web Terminal** | `https://app.workshop-deploy.site/terminal/` |

> แทน `userXX` ด้วย username ของคุณตลอดทั้งเอกสารนี้

---

## ขั้นตอน 1 — เปิด Terminal

เลือกวิธีที่สะดวกได้เลย **แนะนำ Web Terminal** เพราะไม่ต้องติดตั้งอะไรเพิ่ม

### 🌐 วิธีที่ 1 — Web Terminal (แนะนำ)

เปิด browser แล้วไปที่

[https://app.workshop-deploy.site/terminal/](https://app.workshop-deploy.site/terminal/)

Login ด้วย username และ password ที่ได้รับจาก admin ได้เลย

### 💻 วิธีที่ 2 — SSH (สำหรับคนที่ใช้ได้)

**Windows:** กด `Win + R` พิมพ์ `cmd` แล้วกด Enter  
**Mac / Linux:** เปิด Terminal ได้เลย

```bash
ssh userXX@79.108.225.69
```

กรณีเข้าครั้งแรก จะถามว่าต้องการเชื่อมต่อไหม ให้ตอบ `yes`

```bash
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
```

ระบบจะถามรหัสผ่าน — พิมพ์แล้วกด Enter (ตัวอักษรจะไม่แสดง)

---

## ขั้นตอน 2 — เข้า Terminal

เมื่อเข้าสำเร็จจะเห็นข้อมูลของคุณขึ้นมาทันที

```
══════════════════════════════════════════════════════════
  Welcome to Workshop! 🚀
  Username    : userXX
  API URL     : https://app.workshop-deploy.site/userXX/api/
  Swagger     : https://app.workshop-deploy.site/userXX/api/swagger
  Frontend URL: https://app.workshop-deploy.site/userXX/app/
  Database    : userXX_db
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
✅  Frontend พร้อมแล้ว!
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
| `permission denied` | แจ้งผู้บรรยาย |
| `could not find .csproj` | ลองเช็คว่าเข้า folder project ถูกไหม |

---

## สรุป Workflow ทั้งหมด

```
เปิด https://app.workshop-deploy.site/terminal/
       ↓
Login ด้วย userXX / password
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

---

## แก้ไข Code Frontend

#### .env — แทน `XX` ด้วยเลข User ของคุณ

```bash
VITE_API_URL=https://app.workshop-deploy.site/user999/api/api
VITE_BASE_PATH=/userXX/app/
```

#### src/router/index.ts — เพิ่ม `import.meta.env.BASE_URL`

```ts
import { createRouter, createWebHistory } from 'vue-router'
import UserManagement from '../views/UserManagement.vue'

const router = createRouter({
    history: createWebHistory(import.meta.env.BASE_URL), // <-- แก้ไขบรรทัดนี้
    routes: [{
        path: '/',
        name: 'Users',
        component: UserManagement
    }]
})
export default router
```

---

## วิธีการ Copy & Paste บน CMD / Terminal

**CMD (Windows):**
```
Copy  → คลุมดำที่ข้อความที่ต้องการแล้วกดคลิกขวา (Right-Click)
Paste → คลิกขวา (Right-Click) หรือ Shift+Insert
```

**Web Terminal:**
```
Copy  → Ctrl+C หรือคลุมแล้ว Right-Click
Paste → Ctrl+V หรือ Right-Click
```

---

## Users และข้อมูล Workshop

👤 [Workshop Users](https://docs.google.com/spreadsheets/d/1e1bCiAIanqBuPEPmLy0a--kssAINRyEUJ7oKzu6RJ7E/edit?gid=18468552#gid=18468552)

> ⚠️ หลังจากจบกิจกรรม Workshop (16:00 - 16:30 น.) ข้อมูลทั้งหมด รวมถึง VM จะถูกลบทิ้งทันที
