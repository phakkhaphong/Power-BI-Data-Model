# Code Examples - Security & RLS

## 📋 ไฟล์รวบรวม Code Examples สำหรับโมดูล Security & RLS

ไฟล์นี้รวบรวมตัวอย่าง DAX สำหรับ Row-Level Security (RLS)

---

## 1. Static RLS

### ตัวอย่าง Filter Expression

```dax
[Region] = "North America"
```

**อธิบาย:**
- จำกัดให้เห็นข้อมูลเฉพาะ Region = "North America"

---

## 2. Dynamic RLS

### ตัวอย่าง Filter Expression

```dax
[SalesRep] = USERNAME()
```

**อธิบาย:**
- จำกัดให้เห็นข้อมูลเฉพาะ SalesRep = ผู้ใช้ปัจจุบัน

---

## 3. USERNAME() และ USERPRINCIPALNAME()

### USERNAME()

```dax
[EmployeeEmail] = USERNAME()
```

### USERPRINCIPALNAME()

```dax
[EmployeeEmail] = USERPRINCIPALNAME()
```

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลักของโมดูล
- [EXERCISES.md](./EXERCISES.md) - แบบฝึกหัด

