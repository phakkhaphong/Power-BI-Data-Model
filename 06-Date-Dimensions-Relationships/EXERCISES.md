# แบบฝึกหัด - Date Dimensions & Relationships

## 📚 เอกสารแบบฝึกหัดสำหรับการฝึกปฏิบัติ

ไฟล์นี้รวบรวมแบบฝึกหัดแบบ Step-by-Step สำหรับ Date Dimensions และ Relationships โดยเฉพาะ Conformed Date Dimension Pattern โดยรวม Code Examples และคำอธิบายที่ละเอียด

> **ไฟล์ตัวอย่าง:**
> - `Data Model Conformed Date Dimension.SemanticModel`

---

## 🎯 แบบฝึกหัดที่ 1: เข้าใจโครงสร้าง Conformed Date Dimension

### วัตถุประสงค์

เข้าใจโครงสร้าง Conformed Date Dimension และ Relationships ระหว่าง Fact Tables และ DimDate

---

### ขั้นตอนที่ 1: เปิดไฟล์ตัวอย่าง

**Step 1.1:** เปิด Power BI Desktop

**Step 1.2:** เปิดไฟล์ `Data Model Conformed Date Dimension.SemanticModel`

**Step 1.3:** ไปที่ **Model View**

**Step 1.4:** สังเกตโครงสร้าง:
- มี DimDate อยู่ตรงกลาง
- มี FactInternetSales และ FactResellerSales เชื่อมกับ DimDate

---

### ขั้นตอนที่ 2: ตรวจสอบ Relationships

**Step 2.1:** ตรวจสอบ Relationships ระหว่าง FactInternetSales และ DimDate:

**Relationships:**
- `FactInternetSales[OrderDateKey]` → `DimDate[DateKey]` (Active) ✅
- `FactInternetSales[ShipDateKey]` → `DimDate[DateKey]` (Inactive) ⚠️
- `FactInternetSales[DueDateKey]` → `DimDate[DateKey]` (Inactive) ⚠️

**สังเกต:**
- มี 3 Relationships ระหว่าง FactInternetSales และ DimDate
- มีเพียง 1 Active Relationship (OrderDateKey)
- มี 2 Inactive Relationships (ShipDateKey, DueDateKey)

**Step 2.2:** ตรวจสอบ Relationships ระหว่าง FactResellerSales และ DimDate:

**Relationships:**
- `FactResellerSales[OrderDateKey]` → `DimDate[DateKey]` (Active) ✅
- `FactResellerSales[ShipDateKey]` → `DimDate[DateKey]` (Inactive) ⚠️
- `FactResellerSales[DueDateKey]` → `DimDate[DateKey]` (Inactive) ⚠️

---

### ขั้นตอนที่ 3: เข้าใจ Active vs Inactive Relationships

**Step 3.1:** เข้าใจ Active Relationship:

**Active Relationship:**
- `OrderDateKey` → `DimDate[DateKey]` (Active)
- ใช้โดยอัตโนมัติเมื่อไม่ระบุ Relationship
- ใช้บ่อยที่สุด

**Step 3.2:** เข้าใจ Inactive Relationships:

**Inactive Relationships:**
- `ShipDateKey` → `DimDate[DateKey]` (Inactive)
- `DueDateKey` → `DimDate[DateKey]` (Inactive)
- ใช้เฉพาะเมื่อระบุด้วย `USERELATIONSHIP()`

---

### ขั้นตอนที่ 4: เข้าใจ Conformed Date Dimension

**Step 4.1:** เข้าใจแนวคิด:

**Conformed Date Dimension** = DimDate เดียวที่ใช้ร่วมกันระหว่างหลาย Fact Tables

**ข้อดี:**
- ✅ ความสอดคล้องของข้อมูล
- ✅ ลดความซ้ำซ้อน
- ✅ ง่ายต่อการบำรุงรักษา
- ✅ สามารถดู Measures จากหลาย Fact Tables ร่วมกันได้

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ตาราง DimDate เชื่อมกับ Fact Tables ใดบ้าง?**
   - **คำตอบ:** FactInternetSales และ FactResellerSales

2. **Relationships ระหว่าง FactInternetSales กับ DimDate มีอะไรบ้าง?**
   - **คำตอบ:** OrderDateKey (Active), ShipDateKey (Inactive), DueDateKey (Inactive)

3. **Relationships ไหนเป็น Active และไหนเป็น Inactive?**
   - **คำตอบ:** Active = OrderDateKey (ใช้บ่อยที่สุด), Inactive = ShipDateKey, DueDateKey (ใช้เฉพาะบาง Measures)

4. **ทำไมต้องใช้ Conformed Date Dimension?**
   - **คำตอบ:** เพื่อความสอดคล้องของข้อมูล, ลดความซ้ำซ้อน, ง่ายต่อการบำรุงรักษา, และสามารถดู Measures จากหลาย Fact Tables ร่วมกันได้

---

## 🎯 แบบฝึกหัดที่ 2: เข้าใจ USERELATIONSHIP()

### วัตถุประสงค์

เข้าใจวิธีใช้ USERELATIONSHIP() เพื่อเปิดใช้งาน Inactive Relationships

---

### ขั้นตอนที่ 1: เข้าใจ USERELATIONSHIP()

**Step 1.1:** เข้าใจว่า USERELATIONSHIP() คืออะไร:

**USERELATIONSHIP()** = Function ที่ใช้เปิดใช้งาน Inactive Relationship ชั่วคราว

**Syntax:**
```dax
USERELATIONSHIP(<column1>, <column2>)
```

**ลักษณะ:**
- ใช้ใน CALCULATE() เท่านั้น
- มีผลเฉพาะใน CALCULATE() ที่เรียกใช้
- ไม่เปลี่ยนแปลง Active Relationship ที่ Model Level

---

### ขั้นตอนที่ 2: เข้าใจข้อจำกัด

**Step 2.1:** เข้าใจข้อจำกัด:

**Power BI อนุญาตให้มี Active Relationship เพียง 1 ตัวระหว่าง 2 ตาราง**

**ปัญหาที่เกิดขึ้น:**
- ถ้ามีหลาย Date Keys (OrderDate, ShipDate, DueDate)
- ต้องเลือก Active Relationship เพียง 1 ตัว (OrderDateKey)
- Date Keys อื่นๆ ต้องเป็น Inactive Relationships

**Step 2.2:** วิธีแก้ไข:

**ใช้ Inactive Relationships:**
- สร้าง Inactive Relationships สำหรับ ShipDateKey และ DueDateKey
- ใช้ USERELATIONSHIP() ใน Measures เมื่อต้องการใช้ Inactive Relationships

---

### ขั้นตอนที่ 3: ตัวอย่างการใช้งาน

**Step 3.1:** ตัวอย่าง: ใช้ Active Relationship

```dax
Sales by Order Date = SUM(FactResellerSales[SalesAmount])
```

**อธิบาย:**
- ใช้ Active Relationship (OrderDateKey) โดยอัตโนมัติ
- ไม่ต้องใช้ USERELATIONSHIP()

**Step 3.2:** ตัวอย่าง: ใช้ Inactive Relationship

```dax
Sales by Ship Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)
```

**อธิบาย:**
- ใช้ USERELATIONSHIP() เพื่อเปิดใช้งาน Inactive Relationship
- คำนวณยอดขายตามวันที่จัดส่ง

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **USERELATIONSHIP() ใช้ทำอะไร?**
   - **คำตอบ:** เปิดใช้งาน Inactive Relationship ชั่วคราวใน CALCULATE()

2. **USERELATIONSHIP() ใช้ที่ไหนได้บ้าง?**
   - **คำตอบ:** ใช้ใน CALCULATE() เท่านั้น

3. **ทำไมต้องมี Active และ Inactive Relationships?**
   - **คำตอบ:** เพราะ Power BI อนุญาตให้มี Active Relationship เพียง 1 ตัวระหว่าง 2 ตาราง, ต้องใช้ Inactive Relationships สำหรับ Date Keys อื่นๆ, USERELATIONSHIP() ช่วยให้สามารถใช้ Inactive Relationships ได้

---

## 🎯 แบบฝึกหัดที่ 3: สร้าง Measures สำหรับ Ship Date

### วัตถุประสงค์

เรียนรู้วิธีสร้าง Measures ที่ใช้ Inactive Relationship กับ ShipDateKey

---

### ขั้นตอนที่ 1: เข้าใจสถานการณ์

**Step 1.1:** ข้อมูลที่มี:

- ตาราง `FactResellerSales` มี `ShipDateKey`
- มี Relationship ระหว่าง `FactResellerSales[ShipDateKey]` และ `DimDate[DateKey]` (Inactive)
- มี Measure `Total Sales` ที่ใช้ Active Relationship

---

### ขั้นตอนที่ 2: สร้าง Base Measure

**Step 2.1:** สร้าง Measure `Total Sales`:

1. เลือก Table `FactResellerSales`
2. ไปที่ **Table tools** → **New measure**
3. พิมพ์สูตร:

```dax
Total Sales = SUM(FactResellerSales[SalesAmount])
```

**อธิบาย:**
- ใช้ Active Relationship (OrderDateKey) โดยอัตโนมัติ
- คำนวณยอดขายตามวันที่สั่งซื้อ

---

### ขั้นตอนที่ 3: สร้าง Measure สำหรับ Ship Date

**Step 3.1:** สร้าง Measure `Sales by Ship Date`:

1. เลือก Table `FactResellerSales`
2. ไปที่ **Table tools** → **New measure**
3. พิมพ์สูตร:

```dax
Sales by Ship Date = 
CALCULATE(
    [Total Sales],
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)
```

**อธิบายสูตร:**
- ใช้ CALCULATE() เพื่อเปลี่ยน Filter Context
- ใช้ USERELATIONSHIP() เพื่อเปิดใช้งาน Inactive Relationship กับ ShipDateKey
- คำนวณยอดขายตามวันที่จัดส่ง

**Step 3.2:** ทดสอบ Measure:
- สร้าง Visual ที่แสดง Sales by Ship Date
- ตรวจสอบว่าผลลัพธ์แตกต่างจาก Total Sales

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้องใช้ USERELATIONSHIP() ใน Sales by Ship Date?**
   - **คำตอบ:** เพราะ ShipDateKey มี Inactive Relationship ต้องใช้ USERELATIONSHIP() เพื่อเปิดใช้งานชั่วคราว

2. **Sales by Ship Date แตกต่างจาก Total Sales อย่างไร?**
   - **คำตอบ:** Total Sales คำนวณตามวันที่สั่งซื้อ (OrderDateKey), Sales by Ship Date คำนวณตามวันที่จัดส่ง (ShipDateKey)

---

## 🎯 แบบฝึกหัดที่ 4: สร้าง Measures สำหรับ Due Date

### วัตถุประสงค์

เรียนรู้วิธีสร้าง Measures ที่ใช้ Inactive Relationship กับ DueDateKey

---

### ขั้นตอนที่ 1: สร้าง Measure

**Step 1.1:** สร้าง Measure `Sales by Due Date`:

1. เลือก Table `FactResellerSales`
2. ไปที่ **Table tools** → **New measure**
3. พิมพ์สูตร:

```dax
Sales by Due Date = 
CALCULATE(
    [Total Sales],
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[DueDateKey])
)
```

**อธิบายสูตร:**
- ใช้ USERELATIONSHIP() เพื่อเปิดใช้งาน Inactive Relationship กับ DueDateKey
- คำนวณยอดขายตามวันที่ครบกำหนด

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Sales by Due Date คำนวณอะไร?**
   - **คำตอบ:** คำนวณยอดขายตามวันที่ครบกำหนด (DueDateKey)

---

## 🎯 แบบฝึกหัดที่ 5: สร้าง Time Intelligence Measures

### วัตถุประสงค์

เรียนรู้วิธีสร้าง Time Intelligence Measures ที่ใช้ผ่าน Relationships

---

### ขั้นตอนที่ 1: สร้าง Sales YTD (Year-to-Date)

**Step 1.1:** สร้าง Measure `Sales YTD`:

```dax
Sales YTD = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DATESYTD(DimDate[FullDateAlternateKey])
)
```

**อธิบายสูตร:**
- `DATESYTD(...)` หาช่วง Year-to-Date
- ใช้ Active Relationship กับ OrderDateKey

**Step 1.2:** ทดสอบ Measure:
- สร้าง Visual ที่แสดง Sales YTD
- ตรวจสอบว่าผลลัพธ์ถูกต้อง

---

### ขั้นตอนที่ 2: สร้าง Sales YTD - Ship Date

**Step 2.1:** สร้าง Measure `Sales YTD - Ship Date`:

```dax
Sales YTD - Ship Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DATESYTD(DimDate[FullDateAlternateKey]),
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)
```

**อธิบายสูตร:**
- `DATESYTD(...)` หาช่วง Year-to-Date
- `USERELATIONSHIP(...)` ใช้ Inactive Relationship กับ ShipDateKey
- คำนวณ YTD ตามวันที่จัดส่ง

---

### ขั้นตอนที่ 3: สร้าง Sales LY (Last Year)

**Step 3.1:** สร้าง Measure `Sales LY`:

```dax
Sales LY = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    SAMEPERIODLASTYEAR(DimDate[FullDateAlternateKey])
)
```

**อธิบายสูตร:**
- `SAMEPERIODLASTYEAR(...)` หาช่วงเวลาเดียวกันในปีที่แล้ว
- ใช้ Active Relationship กับ OrderDateKey

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Sales YTD และ Sales YTD - Ship Date ต่างกันอย่างไร?**
   - **คำตอบ:** Sales YTD คำนวณตามวันที่สั่งซื้อ, Sales YTD - Ship Date คำนวณตามวันที่จัดส่ง

2. **ทำไมต้องใช้ USERELATIONSHIP() ใน Sales YTD - Ship Date?**
   - **คำตอบ:** เพราะ ShipDateKey มี Inactive Relationship ต้องใช้ USERELATIONSHIP() เพื่อเปิดใช้งาน

---

## 🎯 แบบฝึกหัดที่ 6: รวม Measures จากหลาย Fact Tables

### วัตถุประสงค์

เรียนรู้วิธีรวม Measures จากหลาย Fact Tables เพื่อดูร่วมกันใน DimDate เดียวกัน

---

### ขั้นตอนที่ 1: เข้าใจสถานการณ์

**Step 1.1:** ข้อมูลที่มี:

- มี FactInternetSales และ FactResellerSales
- ทั้งสองมี Measures สำหรับ Order Date และ Ship Date
- ต้องการรวมยอดขายจากทั้งสอง Fact Tables

---

### ขั้นตอนที่ 2: สร้าง Measures ในแต่ละ Fact Table

**Step 2.1:** สร้าง Measures ใน FactInternetSales:

```dax
// Order Date
Ordered Internet Sale Revenue = SUM(FactInternetSales[SalesAmount])

// Ship Date
Shiped Internet Sales Revenue = 
CALCULATE(
    [Ordered Internet Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactInternetSales[ShipDateKey])
)
```

**Step 2.2:** สร้าง Measures ใน FactResellerSales:

```dax
// Order Date
Orderd Reseller Sale Revenue = SUM(FactResellerSales[SalesAmount])

// Ship Date
Shiped Reseller Sales Revenue = 
CALCULATE(
    [Orderd Reseller Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)
```

---

### ขั้นตอนที่ 3: สร้าง Dummy Table สำหรับรวม Measures

**Step 3.1:** สร้าง Calculated Table ชื่อ `All Measures`:

1. ไปที่ **Table tools** → **New table**
2. พิมพ์สูตร:

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
- สร้างตาราง Dummy ที่มี 1 แถว 1 คอลัมน์
- ใช้เป็นที่รวม Measures จากหลาย Fact Tables

---

### ขั้นตอนที่ 4: สร้าง Measures ใน Dummy Table

**Step 4.1:** สร้าง Measure `Total Order Revenue`:

1. เลือก Table `All Measures`
2. ไปที่ **Table tools** → **New measure**
3. พิมพ์สูตร:

```dax
Total Order Revenue = 
[Ordered Internet Sale Revenue] + [Orderd Reseller Sale Revenue]
```

**อธิบาย:**
- รวมยอดขายจากทั้งสอง Fact Tables
- ทั้งสองใช้ Active Relationship กับ OrderDateKey

**Step 4.2:** สร้าง Measure `Total Ship Revenue`:

```dax
Total Ship Revenue = 
[Shiped Internet Sales Revenue] + [Shiped Reseller Sales Revenue]
```

**อธิบาย:**
- รวมยอดขายที่จัดส่งแล้วจากทั้งสอง Fact Tables
- ทั้งสองใช้ USERELATIONSHIP() กับ ShipDateKey

---

### ขั้นตอนที่ 5: ทดสอบ Measures

**Step 5.1:** สร้าง Visual:
- **Axis**: DimDate[CalendarYear]
- **Values**: Total Order Revenue, Total Ship Revenue

**Step 5.2:** ตรวจสอบผลลัพธ์:
- ทั้งสอง Measures ควรแสดงผลใน DimDate เดียวกันได้
- Filter จาก DimDate จะส่งผลต่อทั้งสอง Measures

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้องสร้าง Dummy Table?**
   - **คำตอบ:** เพื่อรวม Measures จากหลาย Fact Tables ในที่เดียว ทำให้สามารถดูร่วมกันใน DimDate เดียวกันได้

2. **Total Order Revenue และ Total Ship Revenue ต่างกันอย่างไร?**
   - **คำตอบ:** Total Order Revenue คำนวณตามวันที่สั่งซื้อ, Total Ship Revenue คำนวณตามวันที่จัดส่ง

---

## 🎯 แบบฝึกหัดที่ 7: เข้าใจการทำงานของ Relationships

### วัตถุประสงค์

เข้าใจการทำงานของ Relationships ใน Conformed Date Dimension Pattern

---

### ขั้นตอนที่ 1: เข้าใจ Active Relationships

**Step 1.1:** ถามตัวเอง:

**คำถาม:** ถ้ามี Measures จากหลาย Fact Tables ที่ใช้ Active Relationship กับ OrderDateKey จะสามารถดูร่วมกันใน DimDate ได้ไหม?

**คำตอบ:** **ได้** ✅

**เหตุผล:**
- เพราะทั้งสองใช้ Active Relationship กับ OrderDateKey ใน DimDate เดียวกัน
- สามารถดูร่วมกันใน Visual เดียวได้
- Filter จาก DimDate จะส่งผลต่อทั้งสอง Measures

---

### ขั้นตอนที่ 2: เข้าใจ Inactive Relationships

**Step 2.1:** ถามตัวเอง:

**คำถาม:** ถ้ามี Measures จากหลาย Fact Tables ที่ใช้ USERELATIONSHIP() กับ ShipDateKey จะสามารถดูร่วมกันใน DimDate ได้ไหม?

**คำตอบ:** **ได้** ✅

**เหตุผล:**
- เพราะทั้งสองใช้ USERELATIONSHIP() กับ ShipDateKey ใน DimDate เดียวกัน
- สามารถดูร่วมกันใน Visual เดียวได้
- Filter จาก DimDate จะส่งผลต่อทั้งสอง Measures

---

### ขั้นตอนที่ 3: เข้าใจความสำคัญของ Conformed Date Dimension

**Step 3.1:** ถามตัวเอง:

**คำถาม:** ทำไม Conformed Date Dimension ถึงสำคัญ?

**คำตอบ:**

1. **ความสอดคล้อง**: ใช้ DimDate เดียวกัน ทำให้การวิเคราะห์มีความสอดคล้อง
2. **ลดความซ้ำซ้อน**: ไม่ต้องสร้าง DimDate หลายตัว
3. **ง่ายต่อการบำรุงรักษา**: แก้ไข DimDate เดียว มีผลต่อทุก Fact Table
4. **ดู Measures ร่วมกันได้**: สามารถดู Measures จากหลาย Fact Tables ใน DimDate เดียวกัน

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ถ้ามี Measures จากหลาย Fact Tables ที่ใช้ Active Relationship กับ OrderDateKey จะสามารถดูร่วมกันใน DimDate ได้ไหม?**
   - **คำตอบ:** ได้ - เพราะทั้งสองใช้ Active Relationship กับ OrderDateKey ใน DimDate เดียวกัน

2. **ถ้ามี Measures จากหลาย Fact Tables ที่ใช้ USERELATIONSHIP() กับ ShipDateKey จะสามารถดูร่วมกันใน DimDate ได้ไหม?**
   - **คำตอบ:** ได้ - เพราะทั้งสองใช้ USERELATIONSHIP() กับ ShipDateKey ใน DimDate เดียวกัน

3. **ทำไม Conformed Date Dimension ถึงสำคัญ?**
   - **คำตอบ:** เพราะความสอดคล้องของข้อมูล, ลดความซ้ำซ้อน, ง่ายต่อการบำรุงรักษา, และสามารถดู Measures จากหลาย Fact Tables ร่วมกันได้

---

## 🎯 แบบฝึกหัดที่ 8: Dummy Table Pattern สำหรับรวม Measures

### วัตถุประสงค์

เรียนรู้วิธีใช้ Dummy Table Pattern เพื่อรวม Measures จากหลาย Fact Tables รวมถึง Role-Playing Measures

---

### ขั้นตอนที่ 1: เข้าใจ Dummy Table Pattern

**Step 1.1:** เข้าใจปัญหา:

**ปัญหา:** 
- มีหลาย Fact Tables (FactInternetSales, FactResellerSales)
- ต้องการรวม Measures จากทุก Fact Table
- ต้องการรวม Role-Playing Measures (Order Date, Ship Date, Due Date)

**วิธีแก้ไข:**
- สร้าง Dummy Table ด้วย DATATABLE()
- สร้าง Measures ใน Dummy Table ที่ Reference Measures จาก Fact Tables อื่น

---

### ขั้นตอนที่ 2: สร้าง Dummy Table

**Step 2.1:** สร้าง Calculated Table:

1. ไปที่ **Table tools** → **New table**
2. พิมพ์สูตร:

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
- สร้างตาราง Dummy ที่มี 1 แถว 1 คอลัมน์ (DummyColumn = 1)
- ใช้เป็นที่รวม Measures จากหลาย Fact Tables

---

### ขั้นตอนที่ 3: สร้าง Measures ในแต่ละ Fact Table

**Step 3.1:** สร้าง Measures ใน FactInternetSales:

```dax
// Order Date
Ordered Internet Sale Revenue = SUM(FactInternetSales[SalesAmount])

// Ship Date
Shiped Internet Sales Revenue = 
CALCULATE(
    [Ordered Internet Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactInternetSales[ShipDateKey])
)

// Due Date
Due Internet Sales Revenue = 
CALCULATE(
    [Ordered Internet Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactInternetSales[DueDateKey])
)
```

**Step 3.2:** สร้าง Measures ใน FactResellerSales:

```dax
// Order Date
Orderd Reseller Sale Revenue = SUM(FactResellerSales[SalesAmount])

// Ship Date
Shiped Reseller Sales Revenue = 
CALCULATE(
    [Orderd Reseller Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[ShipDateKey])
)

// Due Date
Due Reseller Sales Revenue = 
CALCULATE(
    [Orderd Reseller Sale Revenue],
    USERELATIONSHIP(DimDate[DateKey], FactResellerSales[DueDateKey])
)
```

---

### ขั้นตอนที่ 4: สร้าง Measures ใน Dummy Table

**Step 4.1:** สร้าง Measure `Total Order Revenue`:

```dax
Total Order Revenue = 
[Ordered Internet Sale Revenue] + [Orderd Reseller Sale Revenue]
```

**Step 4.2:** สร้าง Measure `Total Ship Revenue`:

```dax
Total Ship Revenue = 
[Shiped Internet Sales Revenue] + [Shiped Reseller Sales Revenue]
```

**Step 4.3:** สร้าง Measure `Total Due Revenue`:

```dax
Total Due Revenue = 
[Due Internet Sales Revenue] + [Due Reseller Sales Revenue]
```

---

### ขั้นตอนที่ 5: ทดสอบ Dummy Table Pattern

**Step 5.1:** สร้าง Visual:
- **Axis**: DimDate[CalendarYear]
- **Values**: Total Order Revenue, Total Ship Revenue, Total Due Revenue

**Step 5.2:** ตรวจสอบผลลัพธ์:
- ทั้งสาม Measures ควรแสดงผลใน DimDate เดียวกันได้
- Filter จาก DimDate จะส่งผลต่อทั้งสาม Measures

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Dummy Table Pattern ใช้ทำอะไร?**
   - **คำตอบ:** ใช้เพื่อรวม Measures จากหลาย Fact Tables ในที่เดียว ทำให้สามารถดูร่วมกันใน DimDate เดียวกันได้

2. **ประโยชน์ของ Dummy Table Pattern คืออะไร?**
   - **คำตอบ:** จัดระเบียบ Measures ที่รวมจากหลาย Fact Tables, สามารถสร้าง Measures สำหรับ Role Playing ที่รวมทุก Fact Table ได้, และทำให้ Model มีโครงสร้างที่ชัดเจนขึ้น

---

## 📝 สรุป

### ✅ สิ่งที่ควรจำ

1. **Conformed Date Dimension** - ใช้ DimDate เดียวสำหรับทุก Fact Table
2. **Active Relationship** - สำหรับวันที่ใช้บ่อยที่สุด (OrderDateKey)
3. **Inactive Relationships** - สำหรับวันที่ใช้เฉพาะบาง Measures (ShipDateKey, DueDateKey)
4. **USERELATIONSHIP()** - ใช้เพื่อเปิดใช้งาน Inactive Relationships
5. **Time Intelligence** - ทำงานผ่าน Relationships
6. **Dummy Table Pattern** - ใช้เพื่อรวม Measures จากหลาย Fact Tables

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลักของโมดูล
- [CODE-EXAMPLES.md](./CODE-EXAMPLES.md) - Code Examples เพิ่มเติม
