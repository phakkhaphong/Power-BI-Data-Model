# 07 - Date Dimensions & Relationships

## เนื้อหาหลักสูตร

โมดูลนี้เกี่ยวกับการสร้างและใช้ Date Dimensions ซึ่งเป็น Dimension ที่สำคัญที่สุดใน Semantic Model และการใช้ Relationships กับ Date Tables โดยเฉพาะ Conformed Date Dimension Pattern

> **Data Source:** ตัวอย่างทั้งหมดใช้ **AdventureWorksDW**

---

## 📋 หัวข้อการเรียนรู้

### 1. Conformed Date Dimension Pattern

**Conformed Date Dimension** คือ Date Dimension ที่ใช้ร่วมกันระหว่างหลาย Fact Tables เพื่อให้การวิเคราะห์ข้อมูลมีความสอดคล้องกัน

**โครงสร้าง:**
- ตาราง DimDate เดียว
- เชื่อมกับหลาย Fact Tables ผ่าน Date Keys ต่างๆ
- ใช้ Role-Playing Pattern สำหรับ Multiple Date Contexts

**ตัวอย่าง (AdventureWorksDW):**
- `DimDate` → Conformed Dimension
- `FactInternetSales` → เชื่อมผ่าน OrderDateKey, ShipDateKey, DueDateKey
- `FactResellerSales` → เชื่อมผ่าน OrderDateKey, ShipDateKey, DueDateKey

**ประโยชน์:**
- ✅ ความสอดคล้องของข้อมูล
- ✅ ลดความซ้ำซ้อน
- ✅ ง่ายต่อการบำรุงรักษา
- ✅ สามารถดู Measures จากหลาย Fact Tables ร่วมกันได้

**👉 ดูตัวอย่างที่สมบูรณ์:** `Data Model Conformed Date Dimension.SemanticModel`

---

### 2. Multiple Date Relationships

**โครงสร้าง Relationships:**

```
DimDate (Conformed Dimension)
│
├── FactInternetSales
│   ├── OrderDateKey → DimDate[DateKey] (Active)
│   ├── ShipDateKey → DimDate[DateKey] (Inactive)
│   └── DueDateKey → DimDate[DateKey] (Inactive)
│
└── FactResellerSales
    ├── OrderDateKey → DimDate[DateKey] (Active)
    ├── ShipDateKey → DimDate[DateKey] (Inactive)
    └── DueDateKey → DimDate[DateKey] (Inactive)
```

**Active vs Inactive:**
- **Active Relationship**: ใช้สำหรับวันที่ใช้บ่อยที่สุด (OrderDate)
- **Inactive Relationships**: ใช้สำหรับวันที่ใช้เฉพาะบาง Measures (ShipDate, DueDate)

---

### 3. USERELATIONSHIP() กับ Date Relationships

**USERELATIONSHIP()** ใช้เพื่อเปิดใช้งาน Inactive Relationship ชั่วคราว

**Syntax:**
```dax
CALCULATE(
    [Measure],
    USERELATIONSHIP(DimDate[DateKey], FactTable[DateKey])
)
```

**ตัวอย่าง:**
```dax
Sales by Ship Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)
```

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 4. การปิด Auto Date/Time

**ทำไมต้องปิด Auto Date/Time:**
- ถ้าเปิดไว้ จะมีการ Detect คอลัมน์ที่มี Type เป็น Date/Datetime แล้วสร้างเป็น Hierarchy ขึ้นมาอัตโนมัติ
- หากต้องการนำ Measure จากต่าง Fact Table มาดูร่วมกัน ในมิติเวลา จะไม่สามารถทำได้ หากแยกเป็น Date ใคร Date มัน

**วิธีแก้ไข:**
- สร้าง Conformed Date Dimension ขึ้นมาใหม่
- ยกเลิก Auto Date/Time

**การปิด Auto Date/Time:**
- **ระดับ Global**: กำหนดให้ยกเลิก Auto date/time กับไฟล์ Power BI ที่สร้างขึ้นใหม่ทันที
- **ระดับ Current File**: หากไฟล์เคยเปิดคุณสมบัติ Auto date/time ไปแล้ว สามารถยกเลิกได้ในส่วนของ Current File

---

### 5. การ Mark as Date Table

**เมื่อไหร่ใช้:**
- เมื่อสร้าง DimDate ขึ้นมาเอง
- เมื่อตั้งใจใช้เป็น Conformed Dimension

**ประโยชน์:**
- รองรับ Time Intelligence Functions ต่างๆ ได้สมบูรณ์
- Power BI จะรู้ว่าตารางนี้เป็น Date Table
- คุณสมบัติ Auto date/time บนทุกตารางจะถูกยกเลิก

**ข้อกำหนด:**
- ตาราง DimDate ต้องมีคอลัมน์ที่มีชนิดข้อมูลเป็น Date หรือ Datetime
- ความละเอียดของคอลัมน์ที่สูงสุดต้องไม่เกินระดับ 1 วัน

---

### 6. Time Intelligence ผ่าน Relationships

**Time Intelligence Functions ที่ใช้ Relationships:**

- **DATESYTD()** - Year-to-Date ผ่าน Relationships
- **DATESQTD()** - Quarter-to-Date ผ่าน Relationships
- **DATESMTD()** - Month-to-Date ผ่าน Relationships
- **SAMEPERIODLASTYEAR()** - Last Year ผ่าน Relationships

**ตัวอย่าง:**
```dax
Sales YTD = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DATESYTD(DimDate[FullDateAlternateKey])
)

Sales YTD - Ship Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DATESYTD(DimDate[FullDateAlternateKey]),
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)
```

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

### 7. Multiple Hierarchies ใน Date Dimension

**ตัวอย่าง Hierarchies:**

**Calendar YSQMD Hierarchy:**
```
Year
└── Semester
    └── Quarter
        └── Month
            └── Day
```

**Calendar YQMD Hierarchy:**
```
Year
└── Quarter
    └── Month
        └── Day
```

**FiscalYear YQMD Hierarchy:**
```
Fiscal Year
└── Fiscal Quarter
    └── Month
        └── Day
```

**ประโยชน์:**
- สามารถดูข้อมูลหลายมุมมองได้
- รองรับทั้ง Calendar Year และ Fiscal Year

---

### 8. Dummy Table Pattern สำหรับรวม Measures

**สถานการณ์:** เมื่อต้องการรวม Measures จากหลาย Fact Tables ในตารางเดียวกัน

**ปัญหา:**
- Measures อยู่ใน Fact Tables ต่างกัน
- ไม่สามารถรวม Measures จากหลายตารางในตารางเดียวกันได้โดยตรง

**แนวทางแก้ไข:** สร้าง Dummy Table ด้วย `DATATABLE()`

#### 8.1 สร้าง Dummy Table

**สร้าง Calculated Table:**
```dax
All Measures = 
DATATABLE(
    "DummyColumn", INTEGER,
    {
        {1}
    }
)
```

**อธิบาย:**
- ใช้ `DATATABLE()` เพื่อสร้างตาราง Dummy
- ตารางนี้มี 1 แถว 1 คอลัมน์ (DummyColumn = 1)
- ใช้เป็นที่รวม Measures จากหลาย Fact Tables

#### 8.2 สร้าง Measures ในแต่ละ Fact Table

**ในตาราง FactInternetSales:**
```dax
Internet Sale Revenue = SUM(FactInternetSales[SalesAmount])
```

**ในตาราง FactResellerSales:**
```dax
Reseller Sale Revenue = SUM(FactResellerSales[SalesAmount])
```

#### 8.3 สร้าง Measures บน Dummy Table

**รวม Revenue จากทุก Fact Table:**
```dax
Revenue = [Internet Sale Revenue] + [Reseller Sale Revenue]
```

**อธิบาย:**
- Measures นี้อยู่ในตาราง "All Measures"
- สามารถ Reference Measures จากตารางอื่นได้โดยตรง
- ผลลัพธ์คือผลรวมของ Revenue จากทุก Fact Table

#### 8.4 สร้าง Measures สำหรับ Role Playing

**ในตาราง FactInternetSales:**
```dax
Shiped Internet Sales Revenue = 
CALCULATE(
    [Internet Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactInternetSales[ShipDateKey])
)

Due Internet Sales Revenue = 
CALCULATE(
    [Internet Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactInternetSales[DueDateKey])
)
```

**ในตาราง FactResellerSales:**
```dax
Shiped Reseller Sales Revenue = 
CALCULATE(
    [Reseller Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)

Due Reseller Sales Revenue = 
CALCULATE(
    [Reseller Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[DueDateKey])
)
```

**ในตาราง All Measures:**
```dax
Shiped Revenue = 
[Shiped Internet Sales Revenue] + [Shiped Reseller Sales Revenue]

Due Revenue = 
[Due Internet Sales Revenue] + [Due Reseller Sales Revenue]
```

**ประโยชน์:**
- จัดระเบียบ Measures ที่รวมจากหลาย Fact Tables
- สามารถสร้าง Measures สำหรับ Role Playing ที่รวมทุก Fact Table ได้
- ทำให้ Model มีโครงสร้างที่ชัดเจนขึ้น

**👉 ดูเพิ่มเติม**: [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)

---

## 🎯 วัตถุประสงค์

หลังจากจบโมดูลนี้ ผู้เรียนจะสามารถ:
- ✅ เข้าใจ Conformed Date Dimension Pattern
- ✅ สร้าง Multiple Date Relationships ได้
- ✅ ใช้ USERELATIONSHIP() กับ Date Relationships ได้
- ✅ สร้าง Time Intelligence Measures ผ่าน Relationships ได้
- ✅ รวม Measures จากหลาย Fact Tables ได้
- ✅ สร้าง Dummy Table เพื่อรวม Measures จากหลาย Fact Tables ได้

---

## 📚 เอกสารที่เกี่ยวข้อง

- **04-Relationships**: พื้นฐานของ Relationships และ USERELATIONSHIP()
- **05-Dimension-Table-Design**: การออกแบบ Date Dimension
- **[CODE-EXAMPLES.md](./CODE-EXAMPLES.md)** - ตัวอย่างโค้ด DAX
- **[EXERCISES.md](./EXERCISES.md)** - แบบฝึกหัด

---

## 📝 สรุป

### Best Practices

1. **ใช้ Conformed Date Dimension**
   - ใช้ DimDate เดียวสำหรับทุก Fact Table
   - สร้าง Multiple Relationships กับ Date Keys ต่างๆ

2. **Mark as Date Table**
   - Mark as Date Table เมื่อสร้าง DimDate ขึ้นมาเอง
   - ปิด Auto Date/Time

3. **Active vs Inactive Relationships**
   - Active: สำหรับวันที่ใช้บ่อยที่สุด (OrderDate)
   - Inactive: สำหรับวันที่ใช้เฉพาะบาง Measures (ShipDate, DueDate)

---

### ไฟล์ตัวอย่างที่แนะนำ

**หมายเหตุ:** ตัวอย่างในโมดูลนี้ใช้ AdventureWorksDW เป็น Data Source

---

**🎉 ขอแสดงความยินดี! คุณได้เรียนจบโมดูล Date Dimensions & Relationships แล้ว!**

**ขั้นตอนต่อไป:**
- ฝึกปฏิบัติตาม [EXERCISES.md](./EXERCISES.md)
- ดูตัวอย่างโค้ดใน [CODE-EXAMPLES.md](./CODE-EXAMPLES.md)
- เรียนโมดูล 07-Fact-Tables-Design ✅
