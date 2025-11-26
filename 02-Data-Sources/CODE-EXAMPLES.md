# Code Examples - Data Sources

## 📋 ไฟล์รวบรวม Code Examples สำหรับโมดูล Data Sources

ไฟล์นี้รวบรวมตัวอย่างการเชื่อมต่อแหล่งข้อมูลและการเตรียมข้อมูลสำหรับ Power BI Semantic Model

---

## 1. การเชื่อมต่อ AdventureWorksDW

### ข้อมูลการเชื่อมต่อ Azure SQL Database

```
Server Name: xxx.database.windows.net
Database Name: AdventureworksDW
Authentication: Database Authentication
Login Name: student
Password: Pa55w.rd
```

### ขั้นตอนการเชื่อมต่อ

1. ใน Power BI Desktop เลือก **Get Data** > **Azure** > **Azure SQL Database**
2. กรอกข้อมูลการเชื่อมต่อ:
   - Server: `ake.database.windows.net`
   - Database: `AdventureworksDW`
3. เลือก **Database Authentication**
4. กรอก Login Name และ Password
5. กด **Connect**

---

## 2. การเตรียมข้อมูลเพื่อลด Cardinality

### ตัวอย่างที่ 1: แยก DateTime เป็น Date และ Time

**Power Query M:**

```m
let
    Source = Table.FromRows(...),
    #"Changed Type" = Table.TransformColumnTypes(Source, {{"OrderDateTime", type datetime}}),
    #"Added Date Column" = Table.AddColumn(#"Changed Type", "OrderDate", each Date.From([OrderDateTime])),
    #"Added Time Column" = Table.AddColumn(#"Added Date Column", "OrderTime", each Time.From([OrderDateTime])),
    #"Removed Columns" = Table.RemoveColumns(#"Added Time Column", {"OrderDateTime"})
in
    #"Removed Columns"
```

**อธิบาย:**
- แยก DateTime ออกเป็น Date และ Time
- ลด Cardinality ของคอลัมน์ DateTime
- เพิ่มประสิทธิภาพ VertiPaq Engine

---

### ตัวอย่างที่ 2: สร้าง Surrogate Key

**Power Query M:**

```m
let
    Source = Table.FromRows(...),
    #"Added Index" = Table.AddIndexColumn(Source, "ProductKey", 1, 1),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Index", {{"ProductKey", Int64.Type}})
in
    #"Changed Type"
```

**อธิบาย:**
- สร้าง Surrogate Key ด้วย Table.AddIndexColumn()
- ใช้แทน Natural Key ที่มี Cardinality สูง
- เพิ่มประสิทธิภาพ Relationships

---

## 3. การเรียงข้อมูล (Sorting)

### ตัวอย่างที่ 1: เรียงข้อมูลตาม Foreign Key

**Power Query M:**

```m
let
    Source = Table.FromRows(...),
    #"Sorted Rows" = Table.Sort(Source, {{"ProductKey", Order.Ascending}, {"OrderDateKey", Order.Ascending}})
in
    #"Sorted Rows"
```

**อธิบาย:**
- เรียงข้อมูลตาม ProductKey และ OrderDateKey
- เพิ่มประสิทธิภาพ RLE Encoding
- ควรเรียงก่อน Import

---

### ตัวอย่างที่ 2: เรียงข้อมูลตาม Date Dimension

**Power Query M:**

```m
let
    Source = Table.FromRows(...),
    #"Sorted Rows" = Table.Sort(Source, {{"OrderDateKey", Order.Ascending}})
in
    #"Sorted Rows"
```

**อธิบาย:**
- เรียงข้อมูลตาม Date Key
- ช่วยเพิ่มประสิทธิภาพ Time Intelligence Queries

---

## 4. Import Mode vs DirectQuery Mode vs Direct Lake Mode

### Import Mode

**ข้อดี:**
- Performance ดีมาก (In-Memory)
- ใช้ VertiPaq Engine เต็มที่
- รองรับ DAX Functions ทั้งหมด

**ข้อเสีย:**
- ต้อง Refresh ข้อมูล
- ใช้ Memory มาก (โหลดข้อมูลทั้งหมด)
- **ไม่มี On Demand Loading**: ต้องโหลดข้อมูลทั้งหมดก่อน

**ใช้เมื่อ:**
- ข้อมูลไม่ต้องการ Real-time
- ต้องการ Performance สูงสุด
- ข้อมูลไม่ใหญ่มาก

---

### DirectQuery Mode

**ข้อดี:**
- ข้อมูล Real-time
- ไม่ต้อง Refresh
- ไม่ใช้ Memory มาก (**On Demand Loading** - Query เฉพาะที่ต้องการ)
- **On Demand Loading**: Query เฉพาะข้อมูลที่จำเป็นเท่านั้น

**ข้อเสีย:**
- Performance ช้ากว่า Import Mode
- ไม่รองรับ DAX Functions บางตัว
- ขึ้นอยู่กับ Performance ของ Source Database

**ใช้เมื่อ:**
- ต้องการข้อมูล Real-time
- ข้อมูลใหญ่มาก
- Source Database มี Performance ดี

**On Demand Loading:**
- Query เฉพาะข้อมูลที่ต้องการเมื่อมีการใช้งานจริง
- ไม่โหลดข้อมูลทั้งหมดก่อน
- ช่วยประหยัด Memory

---

### Direct Lake Mode ⭐ (Microsoft Fabric / Power BI Premium)

**ข้อดี:**
- ✅ Performance ดีมาก (ใกล้เคียง Import Mode)
- ✅ ข้อมูล Always Up-to-date (ไม่ต้อง Refresh)
- ✅ ใช้ Memory น้อยมาก (**On Demand Loading** - อ่านเฉพาะ Columns/Rows ที่ต้องการ)
- ✅ รองรับข้อมูลขนาดใหญ่ (ไม่มีขีดจำกัด)
- ✅ รองรับ DAX Functions ทั้งหมด
- ✅ **On Demand Loading**: อ่านเฉพาะส่วนที่จำเป็นจาก Parquet files

**On Demand Loading ใน Direct Lake:**
- อ่านเฉพาะ **Columns และ Rows ที่จำเป็น** จาก Parquet files
- ไม่โหลดข้อมูลทั้งหมดเข้า Memory (ต่างจาก Import Mode)
- ใช้ประโยชน์จาก **Columnar Storage** (Parquet) → อ่านเฉพาะ Columns ที่ต้องการ
- ช่วยประหยัด Memory และเพิ่ม Performance

**ข้อเสีย:**
- ❌ ต้องใช้ Microsoft Fabric หรือ Power BI Premium/PPU
- ❌ ข้อมูลต้องอยู่ใน OneLake (Delta Lake format)
- ❌ ต้องมี Lakehouse หรือ Data Warehouse ใน Fabric

**ใช้เมื่อ:**
- ✅ มี Microsoft Fabric หรือ Power BI Premium/PPU
- ✅ ข้อมูลอยู่ใน OneLake (Lakehouse หรือ Data Warehouse)
- ✅ ต้องการ Performance สูง + ข้อมูล Real-time
- ✅ ข้อมูลขนาดใหญ่

**ตัวอย่างการใช้งาน:**
```
1. สร้าง Lakehouse ใน Microsoft Fabric
2. อัพโหลดข้อมูลเป็น Delta Lake (Parquet format)
3. สร้าง Power BI Dataset เชื่อมต่อกับ Lakehouse
4. เลือก Direct Lake Mode
5. ไม่ต้อง Import หรือ DirectQuery
```

**เปรียบเทียบ:**

| ลักษณะ | Import | DirectQuery | Direct Lake |
|--------|--------|-------------|-------------|
| Performance | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ |
| Real-time | ❌ | ✅ | ✅ |
| Memory Usage | ❌ มาก (โหลดทั้งหมด) | ✅ น้อยมาก (On Demand) | ✅ น้อยมาก (On Demand) |
| On Demand Loading | ❌ ไม่ใช่ (Pre-loading) | ✅ Query เฉพาะที่ต้องการ | ✅ อ่านเฉพาะ Columns/Rows ที่ต้องการ |
| DAX Functions | ✅ ทั้งหมด | ❌ จำกัด | ✅ ทั้งหมด |
| ต้อง Premium | ❌ | ❌ | ✅ |

---

## 📝 สรุป

### Best Practices

1. **เตรียมข้อมูลก่อน Import**:
   - ลด Cardinality
   - เรียงข้อมูล
   - แยก DateTime เป็น Date และ Time

2. **เลือก Storage Mode**:
   - ใช้ Import Mode สำหรับ Data Analytics (ข้อมูลเล็ก-กลาง)
   - ใช้ DirectQuery Mode เมื่อต้องการ Real-time (ข้อมูลใหญ่)
   - ใช้ Direct Lake Mode เมื่อมี Fabric/Premium + ต้องการ Performance สูง + Real-time

3. **ใช้ AdventureWorksDW**:
   - ข้อมูลตัวอย่างที่สมบูรณ์แบบ
   - มี Star Schema Structure
   - เหมาะสำหรับการเรียนรู้

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลักของโมดูล
- [EXERCISES.md](./EXERCISES.md) - แบบฝึกหัด

