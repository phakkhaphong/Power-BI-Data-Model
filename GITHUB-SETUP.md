# คู่มือการ Setup GitHub Repository

## ✅ สิ่งที่เตรียมเสร็จแล้ว

1. ✅ Git repository ได้ถูก initialize แล้ว
2. ✅ `.gitignore` file ได้ถูกสร้างแล้ว (เพื่อไม่ให้ commit `.pbix`, `.pbip` และไฟล์อื่นๆ ที่ไม่จำเป็น)
3. ✅ ไฟล์ทั้งหมดได้ถูก stage แล้ว
4. ✅ Initial commit ได้ถูกสร้างแล้ว

## 📋 ขั้นตอนการสร้าง GitHub Repository

### วิธีที่ 1: สร้างผ่าน GitHub Website (แนะนำ)

**Step 1:** ไปที่ GitHub
- เปิดเว็บไซต์: https://github.com
- Login เข้าสู่ระบบ

**Step 2:** สร้าง Repository ใหม่
- คลิกที่ **+** (มุมบนขวา) → เลือก **New repository**
- ตั้งค่า:
  - **Repository name**: `Power-BI-Data-Model` หรือชื่อที่ต้องการ
  - **Description**: `Complete self-learning course on Power BI Semantic Models, Relationships, and VertiPaq Engine`
  - **Visibility**: เลือก **Public** (แนะนำ) หรือ **Private**
  - **อย่า** ติ๊ก "Add a README file" (เพราะเรามี README.md อยู่แล้ว)
  - **อย่า** เพิ่ม .gitignore หรือ license

**Step 3:** คลิก **Create repository**

**Step 4:** Copy URL ของ repository
- จะได้ URL ประมาณ: `https://github.com/YOUR_USERNAME/Power-BI-Data-Model.git`

**Step 5:** เพิ่ม Remote และ Push

กลับมาที่ Terminal/PowerShell ในโฟลเดอร์โปรเจคนี้ และรันคำสั่ง:

```powershell
# เพิ่ม remote repository (แทน YOUR_USERNAME ด้วย username ของคุณ)
git remote add origin https://github.com/YOUR_USERNAME/Power-BI-Data-Model.git

# Push code ขึ้น GitHub
git branch -M main
git push -u origin main
```

---

### วิธีที่ 2: ใช้ GitHub CLI (ถ้ามีติดตั้ง)

```powershell
# สร้าง repository บน GitHub (ต้อง login ก่อนด้วย `gh auth login`)
gh repo create Power-BI-Data-Model --public --source=. --remote=origin --push
```

---

## 🔐 การ Setup Authentication

### Option 1: Personal Access Token (PAT) - แนะนำ

1. ไปที่ GitHub → **Settings** → **Developer settings** → **Personal access tokens** → **Tokens (classic)**
2. คลิก **Generate new token (classic)**
3. ตั้งค่า:
   - **Note**: `Power BI Data Model Repo`
   - **Expiration**: เลือกตามต้องการ
   - **Scopes**: ติ๊ก `repo` (full control)
4. คลิก **Generate token**
5. **Copy token ทันที** (จะไม่เห็นอีก)

เมื่อ push code จะถูกถาม username และ password:
- **Username**: GitHub username ของคุณ
- **Password**: ใช้ Personal Access Token แทน password

### Option 2: SSH Keys

1. Generate SSH key (ถ้ายังไม่มี):
```powershell
ssh-keygen -t ed25519 -C "your_email@example.com"
```

2. เพิ่ม SSH key ไปยัง GitHub:
   - Copy public key: `cat ~/.ssh/id_ed25519.pub`
   - ไปที่ GitHub → **Settings** → **SSH and GPG keys** → **New SSH key**
   - Paste key และ save

3. ใช้ SSH URL แทน HTTPS:
```powershell
git remote set-url origin git@github.com:YOUR_USERNAME/Power-BI-Data-Model.git
```

---

## 📝 คำสั่งที่ต้องรันหลังจากสร้าง Repository

```powershell
# ตรวจสอบ remote
git remote -v

# ถ้ายังไม่มี remote ให้เพิ่ม (ใช้ URL จาก GitHub)
git remote add origin https://github.com/YOUR_USERNAME/Power-BI-Data-Model.git

# เปลี่ยน branch เป็น main (ถ้ายังไม่ใช่)
git branch -M main

# Push ขึ้น GitHub
git push -u origin main
```

---

## 🎯 หลังจาก Push สำเร็จ

1. ไปดูที่ GitHub repository ของคุณ
2. ตรวจสอบว่าไฟล์ทั้งหมดถูกอัพโหลดแล้ว
3. README.md จะแสดงอัตโนมัติในหน้าแรกของ repository

---

## 📚 ข้อมูล Repository

**Repository Structure:**
```
Power-BI-Data-Model/
├── README.md                          # เอกสารหลักของหลักสูตร
├── .gitignore                         # Git ignore rules
├── 01-Introduction/
│   ├── README.md
│   ├── CODE-EXAMPLES.md
│   └── EXERCISES.md
├── 02-Data-Sources/
│   ├── README.md
│   ├── CODE-EXAMPLES.md
│   └── EXERCISES.md
├── ... (13 modules total)
└── 13-Incremental-Refresh-Partitioning/
    ├── README.md
    ├── CODE-EXAMPLES.md
    └── EXERCISES.md
```

**Total Modules:** 13 modules
- 01-Introduction & VertiPaq Engine
- 02-Data-Sources
- 03-Data-Modeling-Basics
- 04-Relationships
- 05-Dimension-Table-Design
- 06-Date-Dimensions-Relationships
- 07-Fact-Tables-Design
- 08-Performance-Optimization
- 09-Best-Practices
- 10-Advanced-Modeling
- 11-Case-Studies
- 12-Security-RLS
- 13-Incremental-Refresh-Partitioning

---

## 🔄 คำสั่งสำหรับการอัพเดทในอนาคต

```powershell
# เพิ่มไฟล์ที่เปลี่ยนแปลง
git add .

# Commit การเปลี่ยนแปลง
git commit -m "คำอธิบายการเปลี่ยนแปลง"

# Push ขึ้น GitHub
git push
```

---

## ❓ ปัญหาที่อาจพบ

### ปัญหา: Authentication failed
**วิธีแก้:**
- ใช้ Personal Access Token แทน password
- หรือ setup SSH keys

### ปัญหา: Permission denied
**วิธีแก้:**
- ตรวจสอบว่า repository URL ถูกต้อง
- ตรวจสอบว่า username และ token/SSH key ถูกต้อง

---

## 📞 ต้องการความช่วยเหลือ?

- GitHub Documentation: https://docs.github.com
- Git Documentation: https://git-scm.com/doc

