# Code Examples - Date Dimensions & Relationships

## 📝 เอกสารตัวอย่างโค้ด DAX สำหรับ Date Dimensions และ Relationships

ไฟล์นี้รวบรวมตัวอย่างโค้ด DAX สำหรับ Date Dimensions และ Relationships จากไฟล์ตัวอย่าง

> **ไฟล์ตัวอย่าง:**
> - `Data Model Conformed Date Dimension.SemanticModel`

---

## 🎯 Conformed Date Dimension Pattern

### ภาพรวม

**Conformed Date Dimension** คือ Date Dimension ที่ใช้ร่วมกันระหว่างหลาย Fact Tables เพื่อให้การวิเคราะห์ข้อมูลมีความสอดคล้องกัน

**โครงสร้าง:**
- ตาราง DimDate เดียว
- เชื่อมกับหลาย Fact Tables ผ่าน Date Keys ต่างๆ
- ใช้ Role-Playing Pattern สำหรับ Multiple Date Contexts

---

## 🎯 Multiple Date Relationships

### โครงสร้าง Relationships

**จากไฟล์ตัวอย่าง:**

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

---

### ตัวอย่างที่ 1: Measures ที่ใช้ Active Relationship

**Order Date Measures (ใช้ Active Relationship):**

```dax
// FactInternetSales
Ordered Internet Sale Revenue = SUM(FactInternetSales[SalesAmount])

// FactResellerSales
Orderd Reseller Sale Revenue = SUM(FactResellerSales[SalesAmount])
```

**อธิบาย:**
- ใช้ Active Relationship กับ OrderDateKey
- วัดยอดขายตามวันที่สั่งซื้อ

---

### ตัวอย่างที่ 2: Measures ที่ใช้ Inactive Relationship (USERELATIONSHIP)

**Ship Date Measures (ใช้ Inactive Relationship):**

```dax
// FactInternetSales - Ship Date
Shiped Internet Sales Revenue = 
CALCULATE(
    [Ordered Internet Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactInternetSales[ShipDateKey])
)

// FactResellerSales - Ship Date
Shiped Reseller Sales Revenue = 
CALCULATE(
    [Orderd Reseller Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)
```

**อธิบาย:**
- ใช้ USERELATIONSHIP() เพื่อเปิดใช้งาน Inactive Relationship
- วัดยอดขายตามวันที่จัดส่ง

---

### ตัวอย่างที่ 3: Measures ที่ใช้ Due Date Relationship

**Due Date Measures:**

```dax
// FactInternetSales - Due Date
Due Internet Sales Revenue = 
CALCULATE(
    [Ordered Internet Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactInternetSales[DueDateKey])
)

// FactResellerSales - Due Date
Due Reseller Sales Revenue = 
CALCULATE(
    [Orderd Reseller Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[DueDateKey])
)
```

**อธิบาย:**
- ใช้ USERELATIONSHIP() กับ DueDateKey
- วัดยอดขายตามวันที่ครบกำหนด

---

## 🎯 USERELATIONSHIP() Function

### Syntax

```dax
USERELATIONSHIP(<column1>, <column2>)
```

**คำอธิบาย:**
- เปิดใช้งาน Inactive Relationship ชั่วคราว
- ใช้ใน CALCULATE() เท่านั้น
- มีผลเฉพาะใน CALCULATE() ที่เรียกใช้

---

### ตัวอย่างการใช้งาน

**ตัวอย่างที่ 4: ใช้ USERELATIONSHIP() ใน CALCULATE()**

```dax
Sales by Ship Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)
```

**อธิบาย:**
- เปิดใช้งาน Relationship ระหว่าง DimDate และ ShipDateKey
- คำนวณยอดขายตามวันที่จัดส่ง

---

## 🎯 Time Intelligence ผ่าน Relationships

### ตัวอย่างที่ 5: Year-to-Date (YTD)

**YTD ที่ใช้ Active Relationship (Order Date):**

```dax
Sales YTD = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DATESYTD(DimDate[FullDateAlternateKey])
)
```

**อธิบาย:**
- ใช้ DATESYTD() เพื่อหาช่วง Year-to-Date
- ใช้ Active Relationship กับ OrderDateKey

---

**YTD ที่ใช้ Inactive Relationship (Ship Date):**

```dax
Sales YTD - Ship Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DATESYTD(DimDate[FullDateAlternateKey]),
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)
```

**อธิบาย:**
- ใช้ DATESYTD() ร่วมกับ USERELATIONSHIP()
- คำนวณ YTD ตามวันที่จัดส่ง

---

### ตัวอย่างที่ 6: Quarter-to-Date (QTD)

```dax
Sales QTD = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DATESQTD(DimDate[FullDateAlternateKey])
)
```

---

### ตัวอย่างที่ 7: Month-to-Date (MTD)

```dax
Sales MTD = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DATESMTD(DimDate[FullDateAlternateKey])
)
```

---

### ตัวอย่างที่ 8: Last Year Comparison

```dax
Sales LY = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    SAMEPERIODLASTYEAR(DimDate[FullDateAlternateKey])
)
```

---

## 🎯 การใช้หลาย Fact Tables ร่วมกัน

### ตัวอย่างที่ 9: รวมยอดขายจากหลาย Fact Tables

**รวม Order Date Revenue:**

```dax
Ordered Revenue = 
[Ordered Internet Sale Revenue] + [Orderd Reseller Sale Revenue]
```

**อธิบาย:**
- รวมยอดขายจาก FactInternetSales และ FactResellerSales
- ทั้งสองใช้ Active Relationship กับ OrderDateKey
- สามารถดูร่วมกันในมิติเวลาเดียวกัน (DimDate)

---

**รวม Ship Date Revenue:**

```dax
Shiped Revenue = 
[Shiped Internet Sales Revenue] + [Shiped Reseller Sales Revenue]
```

**อธิบาย:**
- รวมยอดขายที่จัดส่งแล้ว
- ทั้งสองใช้ USERELATIONSHIP() กับ ShipDateKey
- สามารถดูร่วมกันใน DimDate เดียวกัน

---

**รวม Due Date Revenue:**

```dax
Due Revenue = 
[Due Internet Sales Revenue] + [Due Reseller Sales Revenue]
```

**อธิบาย:**
- รวมยอดขายตามวันที่ครบกำหนด
- ทั้งสองใช้ USERELATIONSHIP() กับ DueDateKey
- สามารถดูร่วมกันใน DimDate เดียวกัน

---

## 🎯 Dummy Table Pattern สำหรับรวม Measures

### ตัวอย่างที่ 7: สร้าง Dummy Table เพื่อรวม Measures

**สถานการณ์:** ต้องการรวม Measures จากหลาย Fact Tables (FactInternetSales และ FactResellerSales)

**ขั้นตอน:**

#### 1. สร้าง Measures ในแต่ละ Fact Table

**ในตาราง FactInternetSales:**
```dax
Internet Sale Revenue = SUM(FactInternetSales[SalesAmount])
```

**ในตาราง FactResellerSales:**
```dax
Reseller Sale Revenue = SUM(FactResellerSales[SalesAmount])
```

#### 2. สร้าง Dummy Table ด้วย DATATABLE()

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

#### 3. สร้าง Measures บน Dummy Table

**รวม Revenue จากทุก Fact Table:**
```dax
Revenue = [Internet Sale Revenue] + [Reseller Sale Revenue]
```

**อธิบาย:**
- Measures นี้อยู่ในตาราง "All Measures"
- สามารถ Reference Measures จากตารางอื่นได้โดยตรง
- ผลลัพธ์คือผลรวมของ Revenue จากทุก Fact Table

#### 4. สร้าง Measures สำหรับ Role Playing

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

**👉 ดูเพิ่มเติม:** ตัวอย่างเหล่านี้แสดงให้เห็นถึงพลังของ Conformed Date Dimension ที่สามารถรวม Measures จากหลาย Fact Tables ได้

## 🎯 Multiple Hierarchies ใน Date Dimension

### ตัวอย่าง Hierarchies

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

---

## 🎯 Best Practices

### 1. ใช้ Conformed Date Dimension

**✅ ดี:**
- ใช้ DimDate เดียวสำหรับทุก Fact Table
- สร้าง Multiple Relationships กับ Date Keys ต่างๆ

**❌ ไม่ดี:**
- สร้าง DimDate แยกสำหรับแต่ละ Fact Table
- ใช้ Auto Date/Time (สร้าง Hierarchies อัตโนมัติ)

---

### 2. Mark as Date Table

**ควรทำ:**
- Mark as Date Table เมื่อสร้าง DimDate ขึ้นมาเอง
- เลือกคอลัมน์ Date Key (FullDateAlternateKey)

**ประโยชน์:**
- รองรับ Time Intelligence Functions
- ปิด Auto Date/Time อัตโนมัติ

---

### 3. ใช้ Active Relationship สำหรับ Primary Date

**แนะนำ:**
- ใช้ Active Relationship สำหรับวันที่ใช้บ่อยที่สุด (เช่น OrderDate)
- ใช้ Inactive Relationships สำหรับวันที่ใช้เฉพาะบาง Measures (เช่น ShipDate, DueDate)

---

### 4. ซ่อน Date Keys

**ควรซ่อน:**
- DateKey (Surrogate Key)
- Date Keys ใน Fact Tables (OrderDateKey, ShipDateKey, DueDateKey)

**แสดง:**
- FullDateAlternateKey
- CalendarYear, CalendarQuarter, CalendarMonth
- Hierarchies

---

## 📚 สรุป

### ✅ Conformed Date Dimension Pattern

1. **ตาราง DimDate เดียว** - ใช้ร่วมกันทุก Fact Table
2. **Multiple Relationships** - Active และ Inactive Relationships
3. **USERELATIONSHIP()** - ใช้ Inactive Relationships ใน Measures
4. **Multiple Hierarchies** - Calendar และ Fiscal Year Hierarchies
5. **Time Intelligence** - ผ่าน Relationships

---

**📖 เอกสารที่เกี่ยวข้อง:**
- [README.md](./README.md) - เอกสารหลักของโมดูล
- [EXERCISES.md](./EXERCISES.md) - แบบฝึกหัด

