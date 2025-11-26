# แบบฝึกหัด - Relationships

## 📚 เอกสารแบบฝึกหัดสำหรับการฝึกปฏิบัติ

ไฟล์นี้รวบรวมแบบฝึกหัดแบบ Step-by-Step สำหรับโมดูล Relationships รวมถึงการสร้าง Relationships และ DAX Functions ที่เกี่ยวข้อง โดยรวม Code Examples และคำอธิบายที่ละเอียด

---

## Part 1: การสร้าง Relationships

## 🎯 แบบฝึกหัดที่ 1: ตรวจสอบ Relationships

### วัตถุประสงค์

เรียนรู้วิธีตรวจสอบ Relationships ที่มีอยู่ใน Semantic Model และเข้าใจ Properties ของแต่ละ Relationship

---

### ขั้นตอนที่ 1: เปิด Model View

**Step 1.1:** เปิด Power BI Desktop

**Step 1.2:** เปิดไฟล์ตัวอย่าง `Data Model - Reseller Sales.pbix` (หรือไฟล์ที่มี Semantic Model)

**Step 1.3:** ไปที่ **Model View** (ไอคอนรูปแผนผังด้านซ้าย)

**Step 1.4:** สังเกตว่า Tables ทั้งหมดแสดงผลเป็น Shapes และ Relationships แสดงเป็นเส้นเชื่อมระหว่าง Tables

---

### ขั้นตอนที่ 2: ตรวจสอบ Relationships

**Step 2.1:** คลิกที่เส้น Relationship ระหว่าง `FactResellerSales` และ `DimProduct`

**Step 2.2:** สังเกต Properties ที่แสดง:
- **Cardinality**: One-to-Many (1:*)
- **Cross Filter Direction**: Single Direction
- **Active**: Yes (เส้นทึบ) หรือ No (เส้นประ)

**Step 2.3:** ตรวจสอบ Keys ที่ใช้:
- **Foreign Key**: FactResellerSales[ProductKey]
- **Primary Key**: DimProduct[ProductKey]

---

### ขั้นตอนที่ 3: ตรวจสอบ Relationships ทั้งหมด

**Step 3.1:** ตรวจสอบ Relationship: **FactResellerSales → DimProduct**
- **Cardinality**: One-to-Many (1:*)
- **Cross Filter Direction**: Single Direction
- **Active**: Yes
- **Foreign Key**: FactResellerSales[ProductKey]
- **Primary Key**: DimProduct[ProductKey]

**Step 3.2:** ตรวจสอบ Relationship: **FactResellerSales → DimDate**
- **Cardinality**: One-to-Many (1:*)
- **Cross Filter Direction**: Single Direction
- **Active**: Yes
- **Foreign Key**: FactResellerSales[OrderDateKey]
- **Primary Key**: DimDate[DateKey]

**Step 3.3:** ตรวจสอบ Relationship: **FactResellerSales → DimReseller**
- **Cardinality**: One-to-Many (1:*)
- **Cross Filter Direction**: Single Direction
- **Active**: Yes
- **Foreign Key**: FactResellerSales[ResellerKey]
- **Primary Key**: DimReseller[ResellerKey]

**Step 3.4:** ตรวจสอบ Relationships อื่นๆ ตามลำดับ

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Relationships ทั้งหมดเป็นแบบ One-to-Many หรือไม่?**
   - **คำตอบ:** ส่วนใหญ่ใช่ เพราะ Fact Tables เชื่อมกับ Dimension Tables แบบ Many-to-One

2. **Cross Filter Direction ส่วนใหญ่เป็นแบบใด?**
   - **คำตอบ:** Single Direction (แนะนำ) เพื่อ Performance ที่ดี

3. **Active Relationships มีกี่ตัว?**
   - **คำตอบ:** ส่วนใหญ่เป็น Active เพราะใช้บ่อยที่สุด

---

## 🎯 แบบฝึกหัดที่ 2: สร้าง Relationship ใหม่

### วัตถุประสงค์

เรียนรู้วิธีสร้าง Relationship ใหม่ใน Power BI Desktop

---

### ขั้นตอนที่ 1: ตรวจสอบ Relationships ที่มีอยู่

**Step 1.1:** เปิด Model View

**Step 1.2:** ตรวจสอบว่ามี Relationship ระหว่าง `FactResellerSales` และ `DimGeography` หรือไม่

**Step 1.3:** สังเกต:
- ถ้ามี → Relationship จะแสดงเป็นเส้นเชื่อม
- ถ้าไม่มี → ไม่มีเส้นเชื่อม

---

### ขั้นตอนที่ 2: สร้าง Relationship ใหม่

**Step 2.1:** ถ้าไม่มี Relationship ให้สร้างใหม่:

**Step 2.2:** ลากคอลัมน์ `GeographyKey` จาก `FactResellerSales` ไปยัง `DimGeography[GeographyKey]`

**Step 2.3:** จะปรากฏหน้าต่าง "Create relationship"

**Step 2.4:** ตรวจสอบ Settings:
- **From table**: FactResellerSales
- **From column**: GeographyKey
- **To table**: DimGeography
- **To column**: GeographyKey

**Step 2.5:** ตรวจสอบ Cardinality:
- ควรเป็น **One-to-Many (1:*)** โดยอัตโนมัติ
- ถ้าไม่ถูกต้อง ให้เลือกใหม่

**Step 2.6:** ตรวจสอบ Cross Filter Direction:
- เลือก **Single Direction** (แนะนำ)

**Step 2.7:** ตั้งชื่อ Relationship:
- พิมพ์: `ResellerSales → Geography`

**Step 2.8:** ตรวจสอบ Active:
- ติ๊กถูก **Active** (ต้องการให้เป็น Active Relationship)

**Step 2.9:** คลิก **OK** เพื่อสร้าง Relationship

---

### ขั้นตอนที่ 3: ตรวจสอบผลลัพธ์

**Step 3.1:** ตรวจสอบว่า Relationship แสดงผลใน Model View:
- ควรเห็นเส้นเชื่อมระหว่าง FactResellerSales และ DimGeography

**Step 3.2:** คลิกที่เส้น Relationship เพื่อตรวจสอบ Properties:
- Cardinality: One-to-Many (1:*)
- Cross Filter Direction: Single Direction
- Active: Yes
- Name: ResellerSales → Geography

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้องสร้าง Relationship ระหว่าง Fact Table และ Dimension Table?**
   - **คำตอบ:** เพื่อให้ Power BI สามารถ Filter และ Aggregate ข้อมูลผ่าน Relationships ได้

2. **Cardinality ควรเป็นแบบไหนระหว่าง Fact Table และ Dimension Table?**
   - **คำตอบ:** One-to-Many (1:*) เพราะ Fact Table มีหลายแถวเชื่อมกับ Dimension Table แถวเดียว

---

## 🎯 แบบฝึกหัดที่ 3: สร้าง Inactive Relationship

### วัตถุประสงค์

เรียนรู้วิธีสร้าง Inactive Relationship สำหรับ Role-Playing Dimensions เช่น Multiple Date Relationships

---

### ขั้นตอนที่ 1: เข้าใจสถานการณ์

**Step 1.1:** เข้าใจปัญหา:
- ตาราง `Sales` มี `OrderDate` และ `ShipDate`
- ตาราง `Date` มี `DateKey`
- ต้องการใช้ทั้ง OrderDate และ ShipDate ใน Visuals

**Step 1.2:** เข้าใจข้อจำกัด:
- Power BI อนุญาตให้มี **Active Relationship เพียง 1 ตัว** ระหว่าง 2 ตาราง
- ต้องสร้าง **Inactive Relationship** สำหรับ Date Keys อื่นๆ

---

### ขั้นตอนที่ 2: สร้าง Active Relationship

**Step 2.1:** สร้าง Active Relationship สำหรับ OrderDate (ใช้บ่อยที่สุด):

**Step 2.2:** ลาก `Sales[OrderDate]` ไปยัง `Date[DateKey]`

**Step 2.3:** ตั้งค่า:
- **Cardinality**: One-to-Many (1:*)
- **Cross Filter Direction**: Single Direction
- **Active**: Yes (ติ๊กถูก)
- **Name**: `Sales → Date (Order)`

**Step 2.4:** คลิก **OK** เพื่อสร้าง Relationship

---

### ขั้นตอนที่ 3: สร้าง Inactive Relationship

**Step 3.1:** สร้าง Inactive Relationship สำหรับ ShipDate:

**Step 3.2:** ลาก `Sales[ShipDate]` ไปยัง `Date[DateKey]`

**Step 3.3:** ตั้งค่า:
- **Cardinality**: One-to-Many (1:*)
- **Cross Filter Direction**: Single Direction
- **Active**: **ยกเลิก** (ไม่ติ๊ก) ← สิ่งสำคัญ
- **Name**: `Sales → Date (Ship)`

**Step 3.4:** คลิก **OK** เพื่อสร้าง Relationship

---

### ขั้นตอนที่ 4: ตรวจสอบผลลัพธ์

**Step 4.1:** ตรวจสอบว่า Relationships แสดงผลถูกต้อง:
- ควรเห็น **2 เส้น** เชื่อมระหว่าง Sales และ Date
- เส้นทึบ = Active Relationship
- เส้นประ = Inactive Relationship

**Step 4.2:** คลิกที่เส้น Relationship เพื่อตรวจสอบ:
- Active Relationship: `Sales → Date (Order)` (Active = Yes)
- Inactive Relationship: `Sales → Date (Ship)` (Active = No)

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ทำไมต้องสร้าง Inactive Relationship?**
   - **คำตอบ:** เพราะ Power BI อนุญาตให้มี Active Relationship เพียง 1 ตัวระหว่าง 2 ตาราง แต่ในกรณี Role-Playing Dimensions (เช่น Multiple Date Keys) ต้องใช้หลาย Relationships

2. **Inactive Relationship ใช้งานอย่างไร?**
   - **คำตอบ:** ใช้ `USERELATIONSHIP()` ใน DAX เพื่อเปิดใช้งาน Inactive Relationship ชั่วคราว

---

## 🎯 แบบฝึกหัดที่ 4: เปรียบเทียบ Cross Filter Direction

### วัตถุประสงค์

เข้าใจความแตกต่างระหว่าง Single Direction และ Both Direction Filtering และผลกระทบต่อ Performance

---

### ขั้นตอนที่ 1: สร้าง Relationships 2 ตัว

**Step 1.1:** สร้าง Relationship แบบ Single Direction:
- `Sales[CustomerKey]` → `Customer[CustomerKey]`
- **Cross Filter Direction**: Single Direction
- **Name**: `Sales → Customer (Single)`

**Step 1.2:** สร้าง Relationship แบบ Both Direction:
- `Sales[ProductKey]` → `Product[ProductKey]`
- **Cross Filter Direction**: Both Direction
- **Name**: `Sales → Product (Both)`

---

### ขั้นตอนที่ 2: ทดสอบ Single Direction

**Step 2.1:** สร้าง Table Visual:
- **Rows**: Customer[CustomerName]
- **Values**: Total Sales

**Step 2.2:** สังเกตผลลัพธ์:
- ✅ สามารถ Filter Sales ด้วย Customer ได้ (ปกติ)
- ❌ ไม่สามารถ Filter Customer ด้วย Sales ได้ (ย้อนกลับไม่ได้)

**Step 2.3:** เข้าใจพฤติกรรม:
- Filter แพร่กระจายจาก Customer → Sales (ทิศทางเดียว)
- Filter ไม่แพร่กระจายจาก Sales → Customer

---

### ขั้นตอนที่ 3: ทดสอบ Both Direction

**Step 3.1:** สร้าง Table Visual:
- **Rows**: Product[ProductName]
- **Values**: Total Sales

**Step 3.2:** สร้าง Slicer จาก Sales[OrderQuantity]

**Step 3.3:** สังเกตผลลัพธ์:
- ✅ สามารถ Filter Sales ด้วย Product ได้ (ปกติ)
- ✅ สามารถ Filter Product ด้วย Sales ได้ (ย้อนกลับได้)

**Step 3.4:** เข้าใจพฤติกรรม:
- Filter แพร่กระจายทั้งสองทิศทาง
- เมื่อเลือก OrderQuantity ใน Slicer → Product จะถูก Filter ด้วย

---

### ขั้นตอนที่ 4: เปรียบเทียบ Performance

**Step 4.1:** เข้าใจผลกระทบต่อ Performance:

**Single Direction:**
- ✅ เร็วกว่า
- ✅ ใช้ Memory น้อยกว่า
- ✅ แนะนำสำหรับกรณีส่วนใหญ่

**Both Direction:**
- ❌ ช้ากว่า (ต้องคำนวณ Filter ทั้งสองทิศทาง)
- ❌ ใช้ Memory มากขึ้น
- ⚠️ ใช้เฉพาะเมื่อจำเป็นจริงๆ

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Single Direction และ Both Direction ต่างกันอย่างไร?**
   - **คำตอบ:** Single Direction = Filter แพร่กระจายทิศทางเดียว (Dimension → Fact), Both Direction = Filter แพร่กระจายทั้งสองทิศทาง

2. **ควรใช้ Both Direction เมื่อไหร่?**
   - **คำตอบ:** ใช้เฉพาะเมื่อจำเป็นจริงๆ เช่น ต้องการ Filter Dimension ด้วย Fact Table เพราะอาจส่งผลต่อ Performance

---

## 🎯 แบบฝึกหัดที่ 5: Star Schema Pattern

### วัตถุประสงค์

เข้าใจ Star Schema Pattern และ Relationships ระหว่าง Fact Tables และ Dimension Tables

---

### ขั้นตอนที่ 1: ตรวจสอบ Fact Table

**Step 1.1:** ใช้ไฟล์ `Data Model - Reseller Sales.pbix`

**Step 1.2:** ไปที่ Model View

**Step 1.3:** ระบุ Fact Table:
- **Fact Table**: FactResellerSales
- อยู่ตรงกลางของ Model View
- เชื่อมกับ Dimension Tables หลายตัว

---

### ขั้นตอนที่ 2: ตรวจสอบ Dimension Tables

**Step 2.1:** ระบุ Dimension Tables ทั้งหมด:

**Dimension Tables:**
- DimProduct
- DimDate
- DimReseller
- DimGeography
- DimEmployee
- DimPromotion

**Step 2.2:** สังเกตว่า Dimension Tables อยู่รอบๆ Fact Table (เหมือนรังสี)

---

### ขั้นตอนที่ 3: ตรวจสอบ Relationships

**Step 3.1:** ตรวจสอบ Relationships ทั้งหมด:

**Relationships:**
- FactResellerSales[ProductKey] → DimProduct[ProductKey]
- FactResellerSales[OrderDateKey] → DimDate[DateKey]
- FactResellerSales[ResellerKey] → DimReseller[ResellerKey]
- FactResellerSales[GeographyKey] → DimGeography[GeographyKey]
- FactResellerSales[EmployeeKey] → DimEmployee[EmployeeKey]
- FactResellerSales[PromotionKey] → DimPromotion[PromotionKey]

**Step 3.2:** สังเกต Pattern:
- Fact Table (Center) - FactResellerSales
- Multiple Dimension Tables (Stars) - DimProduct, DimDate, DimReseller, etc.
- One-to-Many Relationships จาก Fact → Dimensions

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **Star Schema คืออะไร?**
   - **คำตอบ:** Star Schema = Fact Table อยู่ตรงกลาง, Dimension Tables อยู่รอบๆ เชื่อมด้วย One-to-Many Relationships

2. **ทำไม Star Schema ถึงดี?**
   - **คำตอบ:** Performance ดี, เข้าใจง่าย, เหมาะกับ VertiPaq Engine

---

## Part 2: DAX Functions สำหรับ Relationships

## 🎯 แบบฝึกหัดที่ 6: RELATED() และ RELATEDTABLE()

### วัตถุประสงค์

เรียนรู้วิธีใช้ RELATED() และ RELATEDTABLE() เพื่อดึงข้อมูลจาก Related Tables

---

### ขั้นตอนที่ 1: เข้าใจ RELATED()

**Step 1.1:** เข้าใจว่า RELATED() คืออะไร:

**RELATED()** = Function ที่ใช้ดึงข้อมูลจาก Related Table (Many-to-One)

**ลักษณะ:**
- ใช้ใน **Row Context** (เช่น Calculated Columns, Iterator Functions)
- ใช้ได้เมื่อมี Relationship ระหว่าง Tables
- คืนค่าเป็น Single Value

---

### ขั้นตอนที่ 2: สร้าง Calculated Column ด้วย RELATED()

**Step 2.1:** สร้าง Calculated Column ในตาราง `FactResellerSales`:

**Step 2.2:** ไปที่ **Data View** หรือ **Model View**

**Step 2.3:** เลือก Table `FactResellerSales`

**Step 2.4:** ไปที่ **Table tools** → **New column**

**Step 2.5:** พิมพ์สูตร:

```dax
ProductName = RELATED(DimProduct[ProductName])
```

**Step 2.6:** กด Enter เพื่อบันทึก Column

**อธิบายสูตร:**
- `RELATED(DimProduct[ProductName])` ดึง ProductName จาก DimProduct
- ใช้ได้เพราะมี Relationship ระหว่าง FactResellerSales[ProductKey] → DimProduct[ProductKey]

---

### ขั้นตอนที่ 3: เข้าใจ RELATEDTABLE()

**Step 3.1:** เข้าใจว่า RELATEDTABLE() คืออะไร:

**RELATEDTABLE()** = Function ที่ใช้ดึงข้อมูลจาก Related Table เป็น Table (One-to-Many)

**ลักษณะ:**
- ใช้ใน **Row Context** (เช่น Calculated Columns)
- ใช้ได้เมื่อมี Relationship ระหว่าง Tables
- คืนค่าเป็น **Table**

---

### ขั้นตอนที่ 4: สร้าง Calculated Column ด้วย RELATEDTABLE()

**Step 4.1:** สร้าง Calculated Column ในตาราง `DimProduct`:

**Step 4.2:** พิมพ์สูตร:

```dax
SalesCount = COUNTROWS(RELATEDTABLE(FactResellerSales))
```

**อธิบายสูตร:**
- `RELATEDTABLE(FactResellerSales)` ดึง Table ของ Sales ที่เกี่ยวข้องกับ Product นี้
- `COUNTROWS(...)` นับจำนวนแถวใน Table ที่ได้
- ผลลัพธ์ = จำนวนการขายของ Product นี้

---

### ขั้นตอนที่ 5: สร้าง Calculated Column ที่คำนวณยอดขายรวม

**Step 5.1:** สร้าง Calculated Column ในตาราง `DimProduct`:

**Step 5.2:** พิมพ์สูตร:

```dax
TotalSalesAmount = 
SUMX(
    RELATEDTABLE(FactResellerSales),
    FactResellerSales[SalesAmount]
)
```

**อธิบายสูตร:**
- `RELATEDTABLE(FactResellerSales)` ดึง Table ของ Sales ที่เกี่ยวข้องกับ Product นี้
- `SUMX(..., FactResellerSales[SalesAmount])` รวม SalesAmount ของทุกแถวใน Table
- ผลลัพธ์ = ยอดขายรวมของ Product นี้

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **RELATED() และ RELATEDTABLE() ต่างกันอย่างไร?**
   - **คำตอบ:** RELATED() คืนค่า Single Value (Many-to-One), RELATEDTABLE() คืนค่าเป็น Table (One-to-Many)

2. **RELATED() ใช้ได้ใน Measures หรือไม่?**
   - **คำตอบ:** ไม่ได้โดยตรง เพราะ RELATED() ต้องใช้ใน Row Context, Measures ไม่มี Row Context โดยตรง ต้องใช้ใน Iterator Functions (เช่น SUMX)

---

## 🎯 แบบฝึกหัดที่ 7: CALCULATE() และ Filter

### วัตถุประสงค์

เรียนรู้วิธีใช้ CALCULATE() เพื่อควบคุม Filter Context ผ่าน Relationships

---

### ขั้นตอนที่ 1: สร้าง Base Measure

**Step 1.1:** สร้าง Measure `Total Sales`:

**Step 1.2:** เลือก Table `FactResellerSales`

**Step 1.3:** ไปที่ **Table tools** → **New measure**

**Step 1.4:** พิมพ์สูตร:

```dax
Total Sales = SUM(FactResellerSales[SalesAmount])
```

**Step 1.5:** กด Enter เพื่อบันทึก Measure

---

### ขั้นตอนที่ 2: สร้าง Measure ด้วย CALCULATE()

**Step 2.1:** สร้าง Measure `Sales - Bikes`:

**Step 2.2:** พิมพ์สูตร:

```dax
Sales - Bikes = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DimProduct[ProductCategoryName] = "Bikes"
)
```

**อธิบายสูตร:**
- `CALCULATE(...)` เปลี่ยน Filter Context
- `DimProduct[ProductCategoryName] = "Bikes"` Filter เฉพาะ Product Category = "Bikes"
- Filter ทำงานผ่าน Relationship ระหว่าง FactResellerSales และ DimProduct

---

### ขั้นตอนที่ 3: สร้าง Measure ที่คำนวณเปอร์เซ็นต์

**Step 3.1:** สร้าง Measure `Sales % - Bikes`:

**Step 3.2:** พิมพ์สูตร:

```dax
Sales % - Bikes = 
DIVIDE(
    [Sales - Bikes],
    [Total Sales]
)
```

**อธิบายสูตร:**
- `DIVIDE(...)` แบ่งค่าอย่างปลอดภัย (จัดการ Error เมื่อหารด้วย 0)
- `[Sales - Bikes]` ยอดขายเฉพาะ Bikes
- `[Total Sales]` ยอดขายรวม
- ผลลัพธ์ = เปอร์เซ็นต์ยอดขาย Bikes จากยอดรวม

---

### ขั้นตอนที่ 4: สร้าง Measure ที่ Filter หลายเงื่อนไข

**Step 4.1:** สร้าง Measure `Sales - Bikes 2020`:

**Step 4.2:** พิมพ์สูตร:

```dax
Sales - Bikes 2020 = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DimProduct[ProductCategoryName] = "Bikes",
    DimDate[CalendarYear] = 2020
)
```

**อธิบายสูตร:**
- Filter 2 เงื่อนไข: Product Category = "Bikes" และ Year = 2020
- ทั้งสอง Filter ทำงานผ่าน Relationships

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **CALCULATE() ใช้ทำอะไร?**
   - **คำตอบ:** ใช้เพื่อควบคุม Filter Context และ Filter ข้อมูลผ่าน Relationships

2. **ทำไมต้องใช้ DIVIDE() แทนการหารด้วย / ?**
   - **คำตอบ:** เพราะ DIVIDE() จัดการ Error เมื่อหารด้วย 0 ได้ดีกว่า (คืนค่า 0 หรือ Blank)

---

## 🎯 แบบฝึกหัดที่ 8: USERELATIONSHIP()

### วัตถุประสงค์

เรียนรู้วิธีใช้ USERELATIONSHIP() เพื่อเปิดใช้งาน Inactive Relationships

---

### ขั้นตอนที่ 1: เข้าใจสถานการณ์

**Step 1.1:** เข้าใจปัญหา:
- มีตาราง `Sales` ที่มี `OrderDate` และ `DueDate`
- มีตาราง `Date` ที่มี `DateKey`
- มี Active Relationship: Sales[OrderDate] → Date[DateKey]
- มี Inactive Relationship: Sales[DueDate] → Date[DateKey] (ต้องสร้างก่อน)

---

### ขั้นตอนที่ 2: สร้าง Inactive Relationship

**Step 2.1:** สร้าง Inactive Relationship (ถ้ายังไม่มี):

**Step 2.2:** ลาก `Sales[DueDate]` ไปยัง `Date[DateKey]`

**Step 2.3:** ตั้งค่า:
- **Active**: **ยกเลิก** (ไม่ติ๊ก)
- **Name**: `Sales → Date (Due)`

**Step 2.4:** คลิก **OK**

---

### ขั้นตอนที่ 3: สร้าง Measures ด้วย USERELATIONSHIP()

**Step 3.1:** สร้าง Measure `Sales by Order Date`:

```dax
Sales by Order Date = SUM(Sales[Amount])
```

**อธิบาย:**
- ใช้ Active Relationship (OrderDate) โดยอัตโนมัติ
- ไม่ต้องใช้ USERELATIONSHIP()

**Step 3.2:** สร้าง Measure `Sales by Due Date`:

```dax
Sales by Due Date = 
CALCULATE(
    SUM(Sales[Amount]),
    USERELATIONSHIP(DimDate[DateKey], Sales[DueDate])
)
```

**อธิบาย:**
- ใช้ USERELATIONSHIP() เพื่อเปิดใช้งาน Inactive Relationship กับ DueDate
- ต้องใช้ใน CALCULATE() เท่านั้น

**Step 3.3:** สร้าง Measure `Difference`:

```dax
Difference = [Sales by Order Date] - [Sales by Due Date]
```

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **USERELATIONSHIP() ใช้ทำอะไร?**
   - **คำตอบ:** ใช้เพื่อเปิดใช้งาน Inactive Relationship ชั่วคราวใน CALCULATE()

2. **USERELATIONSHIP() ใช้ที่ไหนได้บ้าง?**
   - **คำตอบ:** ใช้ใน CALCULATE() เท่านั้น

---

## 🎯 แบบฝึกหัดที่ 9: CROSSFILTER()

### วัตถุประสงค์

เรียนรู้วิธีใช้ CROSSFILTER() เพื่อเปลี่ยน Cross Filter Direction แบบ Dynamic

---

### ขั้นตอนที่ 1: เข้าใจ CROSSFILTER()

**Step 1.1:** เข้าใจว่า CROSSFILTER() คืออะไร:

**CROSSFILTER()** = Function ที่ใช้เปลี่ยน Cross Filter Direction แบบ Dynamic

**Syntax:**
```dax
CROSSFILTER(Column1, Column2, Direction)
```

**Direction:**
- `BOTH` = Both Direction
- `ONEWAY` = Single Direction (Many → One)
- `NONE` = ปิด Filter

---

### ขั้นตอนที่ 2: สร้าง Measures ด้วย CROSSFILTER()

**Step 2.1:** สร้าง Measure `Total Sales - Both`:

```dax
Total Sales - Both = 
CALCULATE(
    SUM(Sales[Amount]),
    CROSSFILTER(Sales[CustomerKey], Customer[CustomerKey], BOTH)
)
```

**Step 2.2:** สร้าง Measure `Total Sales - One Way`:

```dax
Total Sales - One Way = 
CALCULATE(
    SUM(Sales[Amount]),
    CROSSFILTER(Sales[CustomerKey], Customer[CustomerKey], ONEWAY)
)
```

**Step 2.3:** สร้าง Measure `Total Sales - No Filter`:

```dax
Total Sales - No Filter = 
CALCULATE(
    SUM(Sales[Amount]),
    CROSSFILTER(Sales[CustomerKey], Customer[CustomerKey], NONE)
)
```

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **CROSSFILTER() ใช้เมื่อไหร่?**
   - **คำตอบ:** ใช้เมื่อต้องการเปลี่ยน Cross Filter Direction แบบ Dynamic ในบาง Measures โดยไม่ต้องเปลี่ยน Settings ที่ Relationship Level

2. **ทำไมต้องระวังการใช้ Both Direction?**
   - **คำตอบ:** เพราะอาจส่งผลต่อ Performance และทำให้ผลลัพธ์ไม่ถูกต้องได้

---

## 🎯 แบบฝึกหัดที่ 10: ALL(), ALLRELATED(), ALLSELECTED()

### วัตถุประสงค์

เข้าใจความแตกต่างระหว่าง ALL(), ALLRELATED(), และ ALLSELECTED() และเมื่อไหร่ควรใช้แต่ละตัว

---

### ขั้นตอนที่ 1: เข้าใจ ALL()

**Step 1.1:** เข้าใจว่า ALL() คืออะไร:

**ALL()** = Function ที่ลบ Filter ทั้งหมด

**Step 1.2:** สร้าง Measure:

```dax
Total Sales - ALL = 
CALCULATE(
    SUM(Sales[Amount]),
    ALL(Customer, Product)
)
```

**อธิบาย:**
- ลบ Filter จาก Customer และ Product ทั้งหมด
- ผลลัพธ์ = ยอดขายรวมทั้งหมด (ไม่สน Filter)

---

### ขั้นตอนที่ 2: เข้าใจ ALLRELATED()

**Step 2.1:** เข้าใจว่า ALLRELATED() คืออะไร:

**ALLRELATED()** = Function ที่ลบ Filter จาก Related Tables เท่านั้น

**Step 2.2:** สร้าง Measure:

```dax
Total Sales - ALLRELATED = 
CALCULATE(
    SUM(Sales[Amount]),
    ALLRELATED(Customer),
    ALLRELATED(Product)
)
```

**อธิบาย:**
- ลบ Filter จาก Related Tables (Customer, Product) เท่านั้น
- ยังคง Filter อื่นๆ (เช่น Date, Region) อยู่

---

### ขั้นตอนที่ 3: เข้าใจ ALLSELECTED()

**Step 3.1:** เข้าใจว่า ALLSELECTED() คืออะไร:

**ALLSELECTED()** = Function ที่คง Filter จาก Visual Level

**Step 3.2:** สร้าง Measure:

```dax
Total Sales - ALLSELECTED = 
CALCULATE(
    SUM(Sales[Amount]),
    ALLSELECTED(Customer[Region]),
    ALLSELECTED(Product[Category])
)
```

**อธิบาย:**
- คง Filter จาก Visual Level (Slicers, Visual Filters)
- ลบ Filter จาก Cell Level เท่านั้น

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **ALL(), ALLRELATED(), และ ALLSELECTED() ต่างกันอย่างไร?**
   - **คำตอบ:** ALL() ลบ Filter ทั้งหมด, ALLRELATED() ลบ Filter จาก Related Tables เท่านั้น, ALLSELECTED() คง Filter จาก Visual Level

---

## 🎯 แบบฝึกหัดที่ 11: SELECTEDVALUE() - Measure Selector Pattern

### วัตถุประสงค์

เรียนรู้วิธีใช้ SELECTEDVALUE() เพื่อสร้าง Measure Selector Pattern

---

### ขั้นตอนที่ 1: เข้าใจ SELECTEDVALUE()

**Step 1.1:** เข้าใจว่า SELECTEDVALUE() คืออะไร:

**SELECTEDVALUE()** = Function ที่คืนค่าที่ถูกเลือกไว้ในคอลัมน์จาก Filter Context

**Syntax:**
```dax
SELECTEDVALUE(ColumnName, [AlternateResult])
```

---

### ขั้นตอนที่ 2: สร้าง Measure Selector Table

**Step 2.1:** สร้าง Calculated Table ชื่อ `Measure Selector`:

**Step 2.2:** ไปที่ **Table tools** → **New table**

**Step 2.3:** พิมพ์สูตร:

```dax
Measure Selector = 
DATATABLE(
    "Measure Name", STRING,
    "Measure Sort", INTEGER,
    {
        {"Total Sales", 1},
        {"Total Sales - Bikes", 2},
        {"Total Cost", 3}
    }
)
```

**อธิบาย:**
- สร้าง Table ที่มี Measures ที่ต้องการแสดง
- `Measure Sort` ใช้สำหรับเรียงลำดับใน Slicer

---

### ขั้นตอนที่ 3: สร้าง Measure to Show

**Step 3.1:** สร้าง Measure `Measure to Show`:

**Step 3.2:** พิมพ์สูตร:

```dax
Measure to Show = 
VAR ActiveMeasurename = SELECTEDVALUE('Measure Selector'[Measure Name])
VAR Result = 
    SWITCH(
        ActiveMeasurename,
        "Total Sales", [Total Sales],
        "Total Sales - Bikes", [Total Sales - Bikes],
        "Total Cost", [Total Cost],
        BLANK()
    )
RETURN 
    Result
```

**อธิบาย:**
- `SELECTEDVALUE()` ดึงค่าจาก Slicer
- `SWITCH()` เลือก Measure ตามค่าที่ได้
- ถ้าไม่มีการเลือก → คืนค่า BLANK()

---

### ขั้นตอนที่ 4: สร้าง Visual

**Step 4.1:** สร้าง Slicer:
- **Fields**: Measure Selector[Measure Name]

**Step 4.2:** สร้าง Visual:
- **Fields**: DimDate[CalendarYear]
- **Values**: Measure to Show

**Step 4.3:** ทดสอบ:
- เลือก Measure ที่ต้องการใน Slicer
- Visual จะแสดง Measure ที่เลือก

---

### คำถามเพื่อตรวจสอบความเข้าใจ

1. **SELECTEDVALUE() ใช้ทำอะไร?**
   - **คำตอบ:** ใช้ดึงค่าจาก Filter Context หรือ Slicer

2. **Measure Selector Pattern มีประโยชน์อย่างไร?**
   - **คำตอบ:** ช่วยให้ผู้ใช้สามารถเลือก Measure ที่ต้องการแสดงใน Visual เดียวกันได้ ดีกว่าใช้หลาย Visuals

---

## 📝 สรุป

### ✅ สิ่งที่ควรจำ

1. **One-to-Many (1:*)** เป็น Cardinality ที่ใช้บ่อยที่สุด
2. **Single Direction** เป็น Cross Filter Direction ที่แนะนำ (Performance ดี)
3. **Active Relationships** ใช้โดยอัตโนมัติ
4. **Inactive Relationships** ต้องใช้ USERELATIONSHIP() ใน DAX
5. **Star Schema** เป็น Pattern ที่ใช้บ่อยที่สุด
6. **RELATED()** ใช้สำหรับ Many-to-One, **RELATEDTABLE()** ใช้สำหรับ One-to-Many
7. **CALCULATE()** ใช้เพื่อควบคุม Filter Context ผ่าน Relationships
8. **SELECTEDVALUE()** ใช้เพื่อดึงค่าจาก Filter Context หรือ Slicer

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลัก
- [CODE-EXAMPLES.md](./CODE-EXAMPLES.md) - Code Examples เพิ่มเติม
