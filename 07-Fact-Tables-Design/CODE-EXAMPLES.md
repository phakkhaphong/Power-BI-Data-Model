# Code Examples - Fact Tables Design

## 📝 เอกสารตัวอย่างโค้ด DAX สำหรับ Fact Tables Design

ไฟล์นี้รวบรวมตัวอย่างโค้ด DAX สำหรับ Explicit Measures และ Calculation Groups

> **Data Source:** ตัวอย่างทั้งหมดใช้ **AdventureWorksDW**

---

## 🎯 Explicit Measures

### ตัวอย่างที่ 1: Measure พื้นฐาน - SUM

**สถานการณ์:** คำนวณยอดขายรวม

```dax
Total Sales = SUM(FactResellerSales[SalesAmount])
```

**อธิบาย:**
- ใช้ `SUM()` เพื่อรวมค่าทั้งหมดในคอลัมน์
- Measure นี้จะคำนวณตาม Filter Context ที่มีอยู่

---

### ตัวอย่างที่ 2: Measure พื้นฐาน - COUNT

**สถานการณ์:** นับจำนวนรายการขาย

```dax
Total Orders = COUNTROWS(FactResellerSales)
```

**อธิบาย:**
- ใช้ `COUNTROWS()` เพื่อนับจำนวนแถวในตาราง

---

### ตัวอย่างที่ 3: Measure พื้นฐาน - AVERAGE

**สถานการณ์:** คำนวณยอดขายเฉลี่ยต่อรายการ

```dax
Average Sales = AVERAGE(FactResellerSales[SalesAmount])
```

---

### ตัวอย่างที่ 4: Measure พื้นฐาน - DISTINCTCOUNT

**สถานการณ์:** นับจำนวนสินค้าที่ไม่ซ้ำกัน

```dax
Unique Products = DISTINCTCOUNT(FactResellerSales[ProductKey])
```

---

### ตัวอย่างที่ 5: Measure ที่ใช้ CALCULATE()

**สถานการณ์:** คำนวณยอดขายเฉพาะสินค้าประเภท Bikes

```dax
Sales - Bikes = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DimProduct[ProductCategoryName] = "Bikes"
)
```

**อธิบาย:**
- `CALCULATE()` ใช้เพื่อเปลี่ยน Filter Context
- Filter ผ่าน Relationship ไปยัง DimProduct

---

### ตัวอย่างที่ 6: Measure ที่ใช้ DIVIDE()

**สถานการณ์:** คำนวณเปอร์เซ็นต์

```dax
Sales % by Category = 
DIVIDE(
    SUM(FactResellerSales[SalesAmount]),
    CALCULATE(
        SUM(FactResellerSales[SalesAmount]),
        ALL(DimProduct[ProductCategoryName])
    )
)
```

**อธิบาย:**
- `DIVIDE()` ใช้แทน `/` เพื่อป้องกันการหารด้วยศูนย์
- `ALL()` ลบ Filter ออกจาก ProductCategoryName

---

### ตัวอย่างที่ 7: Measure ที่ใช้ SUMX()

**สถานการณ์:** คำนวณยอดขายรวมจากหลายตาราง

```dax
Total Sales All Tables = 
SUMX(
    FactResellerSales,
    FactResellerSales[SalesAmount]
) + 
SUMX(
    FactInternetSales,
    FactInternetSales[SalesAmount]
)
```

---

### ตัวอย่างที่ 8: Measure ที่ใช้ ALLSELECTED()

**สถานการณ์:** คำนวณยอดขายรวมใน Visual

```dax
Total Sales Selected = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    ALLSELECTED()
)
```

---

## 🎯 Calculation Groups

### ตัวอย่างที่ 9: Basic Calculation Group - Measure Selector

**โครงสร้าง:**

```
Calculation Group: "my 1st Calculation group"
├── Calculation Item: "Reseller Sales Cost"
│   └── Expression: [Reseller Sales Cost]
└── Calculation Item: "Reseller Sales Revenue"
    └── Expression: [Reseller Sales Revenue]
```

**คอลัมน์:**
- `Measure Selector` - Column ที่ใช้แสดงชื่อ Calculation Items
- `Ordinal` - Column ที่ใช้เรียงลำดับ

**การใช้งาน:**
- ใช้เพื่อเลือก Measure ที่ต้องการแสดง
- ผู้ใช้สามารถเลือก Measure ผ่าน Slicer ได้

---

## 🎯 Time Intelligence Calculation Group

### ตัวอย่างที่ 10: Time Intelligence Calculation Group

**โครงสร้าง:**

```
Calculation Group: "Time Intelligence"
├── Calculation Item: Current
│   └── Expression: SELECTEDMEASURE()
├── Calculation Item: LY
│   └── Expression: CALCULATE(SELECTEDMEASURE(), SAMEPERIODLASTYEAR(...))
├── Calculation Item: MTD
│   └── Expression: CALCULATE(SELECTEDMEASURE(), DATESMTD(...))
├── Calculation Item: QTD
│   └── Expression: CALCULATE(SELECTEDMEASURE(), DATESQTD(...))
└── Calculation Item: YTD
    └── Expression: CALCULATE(SELECTEDMEASURE(), DATESYTD(...))
```

### Calculation Item: Current

```dax
Current = SELECTEDMEASURE()
```

**อธิบาย:**
- คืนค่า Measure ปัจจุบันโดยไม่เปลี่ยนแปลง
- ใช้เป็น Baseline สำหรับเปรียบเทียบ

---

### Calculation Item: LY (Last Year)

```dax
LY = 
CALCULATE(
    SELECTEDMEASURE(),
    SAMEPERIODLASTYEAR('Date'[FullDateAlternateKey])
)
```

**อธิบาย:**
- คำนวณ Measure สำหรับปีที่แล้ว
- ใช้ SAMEPERIODLASTYEAR() เพื่อหาช่วงเวลาที่เท่ากันในปีที่แล้ว

---

### Calculation Item: MTD (Month to Date)

```dax
MTD = 
CALCULATE(
    SELECTEDMEASURE(),
    DATESMTD('Date'[FullDateAlternateKey])
)
```

**อธิบาย:**
- คำนวณ Measure จากต้นเดือนถึงปัจจุบัน
- ใช้ DATESMTD() เพื่อหา Range จากต้นเดือน

---

### Calculation Item: QTD (Quarter to Date)

```dax
QTD = 
CALCULATE(
    SELECTEDMEASURE(),
    DATESQTD('Date'[FullDateAlternateKey])
)
```

**อธิบาย:**
- คำนวณ Measure จากต้นไตรมาสถึงปัจจุบัน
- ใช้ DATESQTD() เพื่อหา Range จากต้นไตรมาส

---

### Calculation Item: YTD (Year to Date)

```dax
YTD = 
CALCULATE(
    SELECTEDMEASURE(),
    DATESYTD('Date'[Full Date])
)
```

**อธิบาย:**
- คำนวณ Measure จากต้นปีถึงปัจจุบัน
- ใช้ DATESYTD() เพื่อหา Range จากต้นปี

---

### Calculation Item: Prev Year

```dax
Prev Year = 
CALCULATE(
    SELECTEDMEASURE(),
    SAMEPERIODLASTYEAR('Date'[Full Date])
)
```

**อธิบาย:**
- คำนวณ Measure สำหรับปีที่แล้ว
- ใช้ SAMEPERIODLASTYEAR() เพื่อหาช่วงเวลาที่เท่ากันในปีที่แล้ว

**ตัวอย่าง:** ถ้าปัจจุบันเลือกวันที่ "2024-03-15" จะคำนวณค่าสำหรับ "2023-03-15"

---

## 🎯 เปรียบเทียบ: With vs Without Calculation Group

### ตัวอย่างที่ 11: Without Calculation Group

**ต้องสร้าง Measures หลายตัว:**

```dax
// Base Measures
Order Sales Amount = SUM('Reseller Sales'[Sales Amount])
Total Cost = SUM('Reseller Sales'[TotalProductCost])

// Time Intelligence Measures สำหรับ Order Sales Amount
Order Sales Amount MTD = 
CALCULATE(
    [Order Sales Amount],
    DATESMTD('Date'[Full Date])
)

Order Sales Amount QTD = 
CALCULATE(
    [Order Sales Amount],
    DATESQTD('Date'[Full Date])
)

Order Sales Amount YTD = 
CALCULATE(
    [Order Sales Amount],
    DATESYTD('Date'[Full Date])
)

// Time Intelligence Measures สำหรับ Total Cost
Total Cost MTD = 
CALCULATE(
    [Total Cost],
    DATESMTD('Date'[Full Date])
)

Total Cost QTD = 
CALCULATE(
    [Total Cost],
    DATESQTD('Date'[Full Date])
)

Total Cost YTD = 
CALCULATE(
    [Total Cost],
    DATESYTD('Date'[Full Date])
)
```

**ผลลัพธ์:** 8 Measures (2 Base + 6 Time Intelligence)

**หมายเหตุ:** ตัวอย่างใช้ AdventureWorksDW

---

### ตัวอย่างที่ 12: With Calculation Group

**สร้างเพียง Base Measures และ Calculation Group:**

```dax
// Base Measures (สร้างเพียง 2 ตัว)
Order Sales Amount = SUM('Reseller Sales'[Sales Amount])
Total Cost = SUM('Reseller Sales'[TotalProductCost])

// Calculation Group: "Time Intelligence"
calculationItem 'Current Period' = SELECTEDMEASURE()

calculationItem 'Prev Year' =
    CALCULATE(
        SELECTEDMEASURE(),
        SAMEPERIODLASTYEAR('Date'[Full Date])
    )

calculationItem MTD =
    CALCULATE(
        SELECTEDMEASURE(),
        DATESMTD('Date'[Full Date])
    )

calculationItem QTD =
    CALCULATE(
        SELECTEDMEASURE(),
        DATESQTD('Date'[Full Date])
    )

calculationItem YTD =
    CALCULATE(
        SELECTEDMEASURE(),
        DATESYTD('Date'[Full Date])
    )
```

**ผลลัพธ์:** 2 Measures + 1 Calculation Group (5 Calculation Items)

**ข้อดี:**
- ลดจำนวน Measures อย่างมาก
- เมื่อต้องการเปลี่ยน Logic (เช่น ปรับสูตร YTD) แก้ไขที่ Calculation Item เดียว
- Model เรียบง่ายขึ้น และดูแลรักษาง่ายขึ้น

**หมายเหตุ:** ตัวอย่างใช้ AdventureWorksDW

---

## 🎯 Currency Conversion Calculation Group

### ตัวอย่างที่ 13: Conversion Rate Calculation Group (จากไฟล์จริง)

**โครงสร้าง:**

```
Calculation Group: "Conversion Rate"
├── Calculation Item: No conversion (USD)
│   └── Expression: SELECTEDMEASURE()
├── Calculation Item: Conversion (AVG)
│   └── Expression: SELECTEDMEASURE() * AverageRate
└── Calculation Item: Conversion (EOD)
    └── Expression: SELECTEDMEASURE() * EndOfDayRate
```

### Calculation Item: No conversion (USD)

```dax
'No conversion (USD)' = SELECTEDMEASURE()
```

**อธิบาย:**
- ไม่แปลงค่า (ใช้ Local Currency - USD)
- คืนค่า Measure ปัจจุบันโดยไม่เปลี่ยนแปลง

---

### Calculation Item: Conversion (AVG)

```dax
'Conversion (AVG)' =
    VAR _rate =
        CALCULATE (
            AVERAGE ( CurrencyRate[AverageRate] ),
            CROSSFILTER ( 'Date'[Date Key], CurrencyRate[DateKey], BOTH )
        )
    RETURN
        SELECTEDMEASURE () * _rate
```

**อธิบาย:**
- คำนวณ Average Exchange Rate จาก CurrencyRate table
- ใช้ `CROSSFILTER()` เพื่อเชื่อม Date Dimension กับ CurrencyRate table
- `CROSSFILTER(..., BOTH)` เปิดใช้งาน Bidirectional Filter
- คูณ `SELECTEDMEASURE()` ด้วย Average Rate

**เหตุผลใช้ CROSSFILTER():**
- CurrencyRate table ไม่ได้เชื่อมโดยตรงกับ Fact Table
- ต้องใช้ Date Dimension เป็นตัวเชื่อม
- CROSSFILTER() ช่วยให้สามารถ Filter CurrencyRate จาก Date ใน Visual ได้

---

### Calculation Item: Conversion (EOD)

```dax
'Conversion (EOD)' =
    VAR _rate =
        CALCULATE (
            AVERAGE ( CurrencyRate[EndOfDayRate] ),
            CROSSFILTER ( 'Date'[Date Key], CurrencyRate[DateKey], BOTH )
        )
    RETURN
        SELECTEDMEASURE () * _rate
```

**อธิบาย:**
- คำนวณ End of Day Exchange Rate จาก CurrencyRate table
- ใช้ `CROSSFILTER()` เพื่อเชื่อม Date Dimension กับ CurrencyRate table
- คูณ `SELECTEDMEASURE()` ด้วย End of Day Rate

**ตัวอย่างการใช้งาน:**
- เมื่อเลือกวันที่ "2024-03-15" และเลือก "Conversion (AVG)"
- จะคำนวณอัตราแลกเปลี่ยนเฉลี่ยสำหรับวันที่ 2024-03-15
- แล้วคูณด้วย Measure ที่เลือก (เช่น Total Sales)

**หมายเหตุ:** ตัวอย่างใช้ AdventureWorksDW

---

## 🎯 SELECTEDMEASURE() Function

### พื้นฐาน

**Syntax:**
```dax
SELECTEDMEASURE()
```

**คำอธิบาย:**
- คืนค่า Measure ที่อยู่ใน Context ปัจจุบัน
- ไม่ต้องส่ง Parameter
- ใช้ใน Calculation Items เท่านั้น

**ตัวอย่าง:**
```dax
Current = SELECTEDMEASURE()
```

---

### ใช้กับ CALCULATE()

**ตัวอย่าง:**
```dax
LY = 
CALCULATE(
    SELECTEDMEASURE(),
    SAMEPERIODLASTYEAR('Date'[FullDateAlternateKey])
)
```

**อธิบาย:**
- ใช้ SELECTEDMEASURE() ใน CALCULATE()
- เปลี่ยน Filter Context เพื่อคำนวณปีที่แล้ว

---

## 🎯 SELECTEDVALUE() Function

### พื้นฐาน

**Syntax:**
```dax
SELECTEDVALUE(<columnName>[, <alternateResult>])
```

**คำอธิบาย:**
- คืนค่าที่ถูกเลือกไว้ในคอลัมน์จาก Filter Context หรือ Slicer
- หากมีการเลือกค่ามากกว่า 1 ค่า หรือไม่มีการเลือกค่าใดๆ จะคืนค่าตาม Argument ที่สอง

**ตัวอย่าง:**
```dax
Selected Period = 
SELECTEDVALUE(
    'Time Intelligence'[Period],
    "All"
)
```

**อธิบาย:**
- ถ้ามีการเลือก Period ใน Slicer จะคืนค่า Period นั้น
- ถ้าไม่มีการเลือก จะคืนค่า "All"

---

### ใช้ใน Calculation Item

**ตัวอย่าง:**
```dax
Dynamic Format = 
VAR selectedPeriod = SELECTEDVALUE('Time Intelligence'[Period])
RETURN
    IF(
        selectedPeriod = "YTD",
        SELECTEDMEASURE(),  // Format เป็นตัวเลข
        SELECTEDMEASURE()   // Format เป็นเปอร์เซ็นต์
    )
```

---

## 🎯 Multiple Calculation Groups

### Precedence

**เมื่อมีหลาย Calculation Groups:**

```dax
// Calculation Group 1: Time Intelligence
// Calculation Group 2: Conversion Rate (precedence: 1)
```

**การทำงาน:**
- Calculation Group ที่มี Precedence สูงกว่าจะทำงานก่อน
- จากตัวอย่าง: Conversion Rate (precedence: 1) จะทำงานก่อน Time Intelligence

**ตัวอย่างการทำงาน:**

```dax
// ถ้าเลือก YTD และ AVG Rate
// จะทำงานแบบนี้:
AVG Rate: SELECTEDMEASURE() * xrate
  ↓
YTD: CALCULATE(AVG Rate, DATESYTD(...))
```

---

## 🎯 Best Practices

### 1. ใช้ SELECTEDMEASURE() แทนการระบุ Measure โดยตรง

**❌ ไม่ดี:**
```dax
calculationItem 'Sales LY' = 
CALCULATE(
    [Total Sales],  // ระบุ Measure โดยตรง
    SAMEPERIODLASTYEAR(...)
)
```

**✅ ดี:**
```dax
calculationItem LY = 
CALCULATE(
    SELECTEDMEASURE(),  // ใช้ SELECTEDMEASURE()
    SAMEPERIODLASTYEAR(...)
)
```

**เหตุผล:**
- สามารถใช้กับทุก Measure ได้
- ลดจำนวน Calculation Items ที่ต้องสร้าง

---

### 2. ตั้งชื่อ Calculation Items ให้ชัดเจน

**❌ ไม่ดี:**
```dax
calculationItem 'Item1' = SELECTEDMEASURE()
```

**✅ ดี:**
```dax
calculationItem 'YTD' = 
CALCULATE(SELECTEDMEASURE(), DATESYTD(...))
```

---

### 3. ใช้ Precedence เมื่อมีหลาย Calculation Groups

**ตัวอย่าง:**
```dax
// Time Intelligence (ไม่มี precedence)
// Conversion Rate (precedence: 1) - ทำงานก่อน
```

---

## 📚 สรุป

### ✅ ข้อดีของ Calculation Groups

1. **ลดจำนวน Measures**
   - ไม่ต้องสร้าง Measures แยกสำหรับแต่ละ Variation
   - เช่น ไม่ต้องสร้าง Sales YTD, Sales QTD, Sales MTD แยกกัน

2. **ง่ายต่อการบำรุงรักษา**
   - แก้ไข Calculation Item เดียว มีผลต่อทุก Measure
   - เช่น แก้ไข YTD Logic มีผลต่อทุก Measure

3. **Performance ดีขึ้น**
   - ลด Cardinality ของ Measures
   - Query Execution Plan ดีขึ้น

4. **ความสอดคล้อง**
   - Business Logic สอดคล้องกันทั่วทั้ง Report

---

**📖 เอกสารที่เกี่ยวข้อง:**
- [README.md](./README.md) - เอกสารหลักของโมดูล
- [EXERCISES.md](./EXERCISES.md) - แบบฝึกหัด

