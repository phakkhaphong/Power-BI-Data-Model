# 05 - Relationships ⭐ หัวใจของหลักสูตร

## 📚 เอกสารการเรียนรู้ด้วยตนเอง (Self-Learning Guide)

โมดูลนี้เกี่ยวกับการสร้างและจัดการความสัมพันธ์ระหว่างตารางใน Semantic Model ซึ่งเป็นหัวใจสำคัญของ Power BI Semantic Model

> **หมายเหตุสำคัญ**: Relationships เป็นหัวใจของ Semantic Model การเข้าใจ Relationships อย่างลึกซึ้งจะช่วยให้คุณสร้าง Semantic Model ที่มีประสิทธิภาพได้

---

## 🎯 วัตถุประสงค์การเรียนรู้

หลังจากจบโมดูลนี้ คุณจะสามารถ:
- ✅ สร้างและจัดการ Relationships ได้อย่างถูกต้อง
- ✅ เข้าใจ Cardinality และเลือกใช้ได้เหมาะสม
- ✅ เข้าใจ Cross Filter Direction และผลกระทบต่อ Performance
- ✅ สร้าง Active และ Inactive Relationships
- ✅ ออกแบบ Relationships ให้มีประสิทธิภาพตามหลัก VertiPaq Engine
- ✅ ใช้ DAX Functions สำหรับ Relationships ได้ (RELATED, RELATEDTABLE, USERELATIONSHIP, CROSSFILTER)
- ✅ เขียน DAX เพื่อควบคุม Filter Context ผ่าน Relationships ได้

---

## 📋 สารบัญ

### Part 1: พื้นฐาน Relationships (โมดูลนี้)

1. [แนวคิดของ Relationships](#1-แนวคิดของ-relationships)
2. [การสร้าง Relationships](#2-การสร้าง-relationships)
3. [Cardinality](#3-cardinality)
4. [Cross Filter Direction](#4-cross-filter-direction)
5. [Active และ Inactive Relationships](#5-active-และ-inactive-relationships)
6. [Relationship Properties](#6-relationship-properties)
7. [VertiPaq Engine และ Relationships](#7-vertipaq-engine-และ-relationships)
8. [แบบฝึกหัด](#แบบฝึกหัด)

### Part 2: DAX สำหรับ Relationships (โมดูลนี้ - ต่อ)

👉 **Part 2 อยู่ในโมดูลนี้** - เรียนรู้ DAX Functions สำหรับทำงานกับ Relationships (ดูด้านล่าง)

---

## 📖 ความรู้พื้นฐานที่ควรมี

ก่อนเริ่มเรียนโมดูลนี้ คุณควรมีความรู้เกี่ยวกับ:
- ✅ Power BI Desktop Basics
- ✅ Data Modeling Basics (โมดูล 03)
- ✅ VertiPaq Engine (โมดูล 01) - แนะนำให้เรียนก่อน

> **หมายเหตุ**: หลักสูตรนี้ใช้ **AdventureWorksDW** เป็น Data Source หลัก ตารางหลักที่ใช้:
> - Fact Tables: `FactInternetSales`, `FactResellerSales`
> - Dimension Tables: `DimProduct`, `DimDate`, `DimCustomer`, `DimReseller`, `DimGeography`, `DimEmployee`, `DimPromotion`

---

## 1. แนวคิดของ Relationships

### 🎯 จุดประสงค์

เข้าใจว่า Relationships คืออะไรและทำไมถึงสำคัญ

### 📝 Relationships คืออะไร?

**Relationships** = ความสัมพันธ์ระหว่างตาราง (Tables) ใน Semantic Model

**ทำไมถึงสำคัญ?**
- Relationships ทำให้ Power BI เข้าใจว่าข้อมูลในตารางต่างๆ เชื่อมโยงกันอย่างไร
- ช่วยให้ Power BI สามารถ Join ข้อมูลจากหลายตารางได้อัตโนมัติ
- ทำให้เราสามารถสร้าง Reports และ Visualizations ที่ใช้ข้อมูลจากหลายตารางได้

### 📝 ตัวอย่างง่ายๆ

**สถานการณ์:** มี 2 ตาราง

**ตาราง Sales (การขาย):**

| OrderID | ProductKey | Amount |
|---------|------------|--------|
| 1001 | P001 | 500 |
| 1002 | P002 | 300 |
| 1003 | P001 | 200 |

**ตาราง Product (สินค้า):**

| ProductKey | ProductName |
|------------|-------------|
| P001 | น้ำดื่ม |
| P002 | ขนม |

**คำถาม:** ต้องการแสดงรายงาน "ยอดขายของน้ำดื่ม"

**วิธีทำ:** สร้าง Relationship ระหว่าง `Sales[ProductKey]` และ `Product[ProductKey]`

**ผลลัพธ์:** Power BI จะรู้ว่า P001 = น้ำดื่ม และสามารถแสดงผลได้

### 📝 Foreign Key และ Primary Key

**Primary Key:**
- คอลัมน์ที่ใช้ระบุตัวตนของแต่ละแถวในตาราง
- ต้องมีค่า unique (ไม่ซ้ำกัน)
- ตัวอย่าง: `Product[ProductKey]`

**Foreign Key:**
- คอลัมน์ใน Fact Table ที่ชี้ไปยัง Primary Key ของ Dimension Table
- ตัวอย่าง: `Sales[ProductKey]` ชี้ไปยัง `Product[ProductKey]`

**ตัวอย่าง:**

**Dimension Table: Product:**

| ProductKey | ProductName |
|------------|-------------|
| P001 | น้ำดื่ม |
| P002 | ขนม |

← ProductKey เป็น Primary Key

**Fact Table: Sales:**

| OrderID | ProductKey | Amount |
|---------|------------|--------|
| 1001 | P001 | 500 |
| 1002 | P002 | 300 |

← ProductKey เป็น Foreign Key

**Relationship:** `Sales[ProductKey] → Product[ProductKey]`

---

## 2. การสร้าง Relationships

### 🎯 จุดประสงค์

เรียนรู้วิธีสร้าง Relationships ใน Power BI Desktop

### 📝 วิธีสร้าง Relationships

#### วิธีที่ 1: Auto-Detection (อัตโนมัติ)

**ขั้นตอน:**
1. นำข้อมูลเข้าสู่ Power BI Desktop
2. Power BI จะตรวจสอบ Relationships อัตโนมัติ
3. ถ้าพบชื่อคอลัมน์ที่ตรงกัน จะสร้าง Relationship ให้อัตโนมัติ

**ข้อดี:**
- ✅ สะดวก เร็ว
- ✅ ไม่ต้องทำเอง

**ข้อเสีย:**
- ❌ อาจสร้างผิด
- ❌ อาจไม่ตรงกับความต้องการ

#### วิธีที่ 2: Manual Creation (สร้างเอง)

**ขั้นตอน:**
1. เปิด **Model View** (ไอคอนตารางด้านซ้าย)
2. ลากคอลัมน์จาก Fact Table ไปยัง Dimension Table
3. หรือ คลิกขวาที่คอลัมน์ → **Manage Relationships** → **New**

**ตัวอย่าง:**
```
ลาก: Sales[ProductKey] → Product[ProductKey]
```

**ข้อดี:**
- ✅ ควบคุมได้ดี
- ✅ ตรวจสอบได้ชัดเจน

### 📝 การจัดการ Relationships

**ดู Relationships:**
- เปิด Model View
- จะเห็นเส้นเชื่อมระหว่างตาราง

**แก้ไข Relationship:**
1. คลิกที่เส้น Relationship
2. คลิกขวา → **Properties**
3. ปรับ Cardinality และ Cross Filter Direction

**ลบ Relationship:**
1. คลิกที่เส้น Relationship
2. กด **Delete** หรือ คลิกขวา → **Delete**

---

## 3. Cardinality

### 🎯 จุดประสงค์

เข้าใจ Cardinality และเลือกใช้ได้เหมาะสม

### 📝 Cardinality คืออะไร?

**Cardinality** = จำนวนความสัมพันธ์ระหว่างตาราง

**ประเภทของ Cardinality:**

#### 1. One-to-Many (1:*) ⭐ **ใช้บ่อยที่สุด**

**ความหมาย:**
- หนึ่งแถวใน Dimension Table สามารถมีหลายแถวใน Fact Table

**ตัวอย่าง:**

**Product (One):**

| ProductKey | ProductName |
|------------|-------------|
| P001 | น้ำดื่ม |
| P002 | ขนม |

← 1 (หนึ่งแถวใน Dimension Table)

**Sales (Many):**

| OrderID | ProductKey | Amount |
|---------|------------|--------|
| 1001 | P001 | 500 |
| 1002 | P002 | 300 |
| 1003 | P001 | 200 |

← Many (หลายแถวใน Fact Table)

**Relationship:** `Product[ProductKey] → Sales[ProductKey]` (1:*)

**เมื่อไหร่ใช้:**
- ⭐ ใช้บ่อยที่สุด (90% ของกรณี)
- Fact Table → Dimension Tables

#### 2. Many-to-One (*:1)

**ความหมาย:**
- หลายแถวใน Fact Table ชี้ไปยังหนึ่งแถวใน Dimension Table
- **เป็นด้านกลับของ One-to-Many** (เหมือนกัน)

#### 3. One-to-One (1:1)

**ความหมาย:**
- หนึ่งแถวในตารางแรก มีแถวเดียวในตารางที่สอง

**ตัวอย่าง:**

**Employee (1):**

| EmployeeID | EmployeeName |
|-----------|--------------|
| E001 | สมชาย |

**EmployeeDetails (1):**

| EmployeeID | Address |
|-----------|---------|
| E001 | กรุงเทพ |

**Relationship:** `Employee[EmployeeID] → EmployeeDetails[EmployeeID]` (1:1)

**เมื่อไหร่ใช้:**
- ใช้ไม่บ่อย
- เมื่อข้อมูลแยกออกเป็น 2 ตาราง (เช่น Employee และ EmployeeDetails)

#### 4. Many-to-Many (*:*)

**ความหมาย:**
- หลายแถวในตารางแรกสามารถมีหลายแถวในตารางที่สอง

**ตัวอย่าง:**

**Student (Many):**

| StudentID | StudentName |
|-----------|-------------|
| S001 | สมชาย |
| S002 | สมหญิง |

**Course (Many):**

| CourseID | CourseName |
|----------|------------|
| C001 | คณิตศาสตร์ |
| C002 | ภาษาอังกฤษ |

**Bridge Table: Enrollment**

| StudentID | CourseID |
|-----------|----------|
| S001 | C001 |
| S001 | C002 |
| S002 | C001 |

**Relationships:**
Student[StudentID] → Enrollment[StudentID] (1:*)
Course[CourseID] → Enrollment[CourseID] (1:*)
```

**เมื่อไหร่ใช้:**
- ใช้ไม่บ่อย
- ต้องใช้ Bridge Table (ตารางเชื่อม)

### 📝 วิธีเลือก Cardinality

**✅ ใช้ One-to-Many (1:*):**
- Fact Table → Dimension Tables (90% ของกรณี)
- สินค้า → การขาย
- ลูกค้า → การขาย
- วันที่ → การขาย

**⚠️ ใช้ One-to-One (1:1):**
- เมื่อแยกตารางเป็น 2 ตาราง (ไม่ค่อยใช้)

**⚠️ ใช้ Many-to-Many (*:*):**
- เมื่อต้องใช้ Bridge Table (ไม่ค่อยใช้)

---

## 4. Cross Filter Direction และ Filter Context Propagation

### 🎯 จุดประสงค์

เข้าใจ Cross Filter Direction, Filter Context Propagation และผลกระทบต่อ Performance

### 📝 Cross Filter Direction คืออะไร?

**Cross Filter Direction** = ทิศทางการแพร่กระจายของ Filter ผ่าน Relationship

**Filter Context Propagation** = การแพร่กระจายของ Filter Context ผ่าน Relationships ตาม Cross Filter Direction

**ประเภท:**

#### 1. Single Direction (Many → One) ⭐ **แนะนำ - Default**

**ความหมาย:**
- Filter แพร่กระจายจาก "One" ไป "Many" (Dimension → Fact)
- Filter Context ไปทิศทางเดียว

**ตัวอย่างการทำงาน:**
```
DimProduct[Category] = "Electronics"
    ↓ (Filter Context แพร่กระจายไป Fact Table)
FactResellerSales (Filtered - แสดงเฉพาะสินค้า Electronics)
```

**ข้อดี:**
- ✅ Performance ดีที่สุด
- ✅ ใช้ Memory น้อย
- ✅ เร็ว

**ข้อเสีย:**
- ❌ ไม่สามารถ Filter ย้อนกลับได้ (จาก Fact → Dimension)

**เมื่อไหร่ใช้:**
- ⭐ **ใช้เป็น Default** (90% ของกรณี)
- Dimension Tables → Fact Tables

#### 2. Both Direction (Bi-directional)

**ความหมาย:**
- Filter แพร่กระจายทั้งสองทิศทาง (Dimension ↔ Fact)
- Filter Context ไปทั้งสองทิศทาง

**ตัวอย่างการทำงาน:**
```
DimCustomer[Region] = "North America"
    ↔ (Filter Context แพร่กระจายทั้งสองทิศทาง)
FactResellerSales (Filtered - แสดงเฉพาะลูกค้าใน North America)
    ↔
DimGeography (Filtered - สามารถ Filter ย้อนกลับได้)
```

**ข้อดี:**
- ✅ Filter ได้ทั้งสองทิศทาง
- ✅ เหมาะสำหรับกรณีที่ต้องการ Filter ย้อนกลับ

**ข้อเสีย:**
- ❌ Performance ช้ากว่า Single Direction
- ❌ ใช้ Memory มากขึ้น
- ❌ อาจทำให้ Model ซับซ้อนขึ้น

**เมื่อไหร่ใช้:**
- ⚠️ ใช้เฉพาะเมื่อจำเป็นจริงๆ
- เมื่อต้อง Filter ย้อนกลับ (เช่น Filter Geography จาก Sales)
- ควรพิจารณา Performance impact ก่อน

### 📝 วิธีเลือก Cross Filter Direction

**✅ ใช้ Single Direction (แนะนำ):**
- **Default สำหรับ Relationships ระหว่าง Dimension → Fact**
- 90% ของกรณี
- ใช้เมื่อไม่ต้อง Filter ย้อนกลับ

**⚠️ ใช้ Both Direction (ใช้เมื่อจำเป็น):**
- เมื่อต้อง Filter ย้อนกลับจริงๆ
- พิจารณา Performance impact ก่อน
- อาจใช้ CROSSFILTER() แบบ Dynamic ใน DAX แทน (ดูใน Part 2)

---

## 5. Active และ Inactive Relationships

### 🎯 จุดประสงค์

เข้าใจ Active และ Inactive Relationships และการใช้ USERELATIONSHIP()

### 📝 Active Relationships

**Active Relationship:**
- Relationship ที่ใช้งานโดยอัตโนมัติ
- Default ของทุก Relationship
- ใช้ใน Visualizations และ Measures อัตโนมัติ

**ตัวอย่าง:**
```
Sales[OrderDate] → Date[DateKey] (Active)
→ ใช้ใน Measures อัตโนมัติ
```

### 📝 Inactive Relationships

**Inactive Relationship:**
- Relationship ที่ไม่ใช้งานโดยอัตโนมัติ
- ต้องใช้ USERELATIONSHIP() ใน DAX เพื่อใช้งาน
- ใช้สำหรับ Role-Playing Dimensions

**ตัวอย่าง:**
```
Sales[OrderDate] → Date[DateKey] (Active)
Sales[ShipDate] → Date[DateKey] (Inactive) ← ต้องใช้ USERELATIONSHIP()
```

### 📝 Role-Playing Dimensions

**Role-Playing Dimension:**
- Dimension Table เดียวกันแต่มีหลาย Roles
- ใช้ Inactive Relationships หลายตัว

**ตัวอย่าง:**
```
ตาราง Sales มี:
- OrderDate (วันที่สั่งซื้อ)
- ShipDate (วันที่ส่ง)
- DueDate (วันที่ครบกำหนด)

ตาราง Date (Role-Playing Dimension):
- OrderDate → Date[DateKey] (Active)
- ShipDate → Date[DateKey] (Inactive)
- DueDate → Date[DateKey] (Inactive)
```

**👉 เรียนรู้เพิ่มเติม:** ดูใน Part 2 ของโมดูลนี้ - USERELATIONSHIP()

---

## 6. Relationship Properties

### 🎯 จุดประสงค์

เรียนรู้การตั้งค่า Properties ของ Relationship

### 📝 Relationship Properties

**Properties ที่สำคัญ:**

#### 1. Cardinality
- กำหนด One-to-Many, Many-to-One, One-to-One, Many-to-Many

#### 2. Cross Filter Direction
- กำหนด Single Direction หรือ Both Direction

#### 3. Active
- กำหนด Active หรือ Inactive

#### 4. Name
- ตั้งชื่อ Relationship (แนะนำให้ตั้งชื่อให้ชัดเจน)

**วิธีตั้งค่า:**
1. คลิกที่เส้น Relationship
2. คลิกขวา → **Properties**
3. ปรับค่าต่างๆ

---

## 7. VertiPaq Engine และ Relationships

### 🎯 จุดประสงค์

เข้าใจความสัมพันธ์ระหว่าง VertiPaq Engine และ Relationships

### 📝 Low Cardinality และ Relationships

**Foreign Keys ควรมี Low Cardinality:**

**❌ ไม่ดี - High Cardinality:**
```
Sales[TransactionID] → Transaction[TransactionID]
→ TransactionID มีค่า unique มาก (High Cardinality)
→ ไม่เหมาะสำหรับ Relationships
```

**✅ ดี - Low Cardinality:**
```
Sales[ProductKey] → Product[ProductKey]
→ ProductKey มีค่า unique น้อย (Low Cardinality)
→ เหมาะสำหรับ Relationships
```

### 📝 Sorted Data และ Relationships

**ข้อมูลที่เรียงลำดับช่วยเพิ่มประสิทธิภาพ:**

**✅ ดี - Sorted Data:**
```
FactResellerSales เรียงตาม:
1. OrderDate
2. ProductKey
→ เพิ่มประสิทธิภาพการ Join
```

**👉 เรียนรู้เพิ่มเติม:** [01-Introduction & VertiPaq Engine](../01-Introduction/README.md)

---

> **📝 แบบฝึกหัด:** ดูแบบฝึกหัดทั้งหมดใน [EXERCISES.md](./EXERCISES.md)

---

## 📝 สรุปและ Best Practices

### ✅ Best Practices

1. **ใช้ One-to-Many (1:*) เป็นหลัก**
   - 90% ของกรณี
   - Fact Tables → Dimension Tables

2. **ใช้ Single Direction เป็น Default**
   - Performance ดีที่สุด
   - ใช้ Both Direction เฉพาะเมื่อจำเป็นจริงๆ

3. **ใช้ Low Cardinality Foreign Keys**
   - Foreign Keys ควรมีค่าซ้ำกันมาก
   - ช่วยเพิ่มประสิทธิภาพ VertiPaq Engine

4. **ตั้งชื่อ Relationships ให้ชัดเจน**
   - เช่น: "FactResellerSales → DimProduct", "FactResellerSales → DimDate"

5. **ตรวจสอบ Relationships อย่างสม่ำเสมอ**
   - ใช้ Auto-Detection แต่ตรวจสอบให้ดี
   - สร้างเองเมื่อจำเป็น

### ⚠️ สิ่งที่ควรระวัง

1. **อย่าใช้ Both Direction ถ้าไม่จำเป็น**
   - มีผลกระทบต่อ Performance
   - พิจารณาใช้ CROSSFILTER() แบบ Dynamic ใน DAX แทน

2. **ระวัง Many-to-Many Relationships**
   - อาจมีผลกระทบต่อ Performance
   - ใช้ Bridge Table เมื่อจำเป็น

3. **ตรวจสอบ Cardinality ให้ถูกต้อง**
   - Cardinality ที่ผิดจะทำให้ผลลัพธ์ผิด

---

# Part 2: DAX สำหรับ Relationships

## 🎯 วัตถุประสงค์การเรียนรู้

หลังจากจบ Part 2 คุณจะสามารถ:
- ✅ ใช้ DAX Functions ที่เกี่ยวกับ Relationships ได้อย่างถูกต้อง
- ✅ เข้าใจ Filter Context และ Relationship Propagation
- ✅ เขียน DAX เพื่อควบคุม Filtering ผ่าน Relationships ได้
- ✅ แก้ปัญหาเกี่ยวกับ Relationships ด้วย DAX ได้

---

## 📋 สารบัญ Part 2

1. [RELATED() และ RELATEDTABLE()](#1-related-และ-relatedtable-part-2)
2. [CALCULATE() และ Relationship Filters](#2-calculate-และ-relationship-filters-part-2)
3. [USERELATIONSHIP()](#3-userelationship-part-2)
4. [CROSSFILTER()](#4-crossfilter-part-2)
5. [TREATAS()](#5-treatas-part-2)
6. [ALLRELATED() และ ALLSELECTED()](#6-allrelated-และ-allselected-part-2)
7. [Filter Context และ Relationships](#7-filter-context-และ-relationships-part-2)

---

## 1. RELATED() และ RELATEDTABLE() {#1-related-และ-relatedtable-part-2}

### 🎯 จุดประสงค์

เรียนรู้วิธีดึงข้อมูลจาก Related Tables โดยใช้ RELATED() และ RELATEDTABLE()

### 📝 RELATED()

**Syntax:**
```dax
RELATED(<column>)
```

**คำอธิบาย:**
- ดึงข้อมูลจาก Related Table (Many-to-One relationship)
- ใช้ใน **Row Context** เท่านั้น
- ใช้กับ Calculated Columns หรือใน Iterator Functions

**ตัวอย่างที่ 1: ดึงข้อมูลชื่อสินค้าจาก Dimension Table (AdventureWorksDW)**

**สถานการณ์:** 
- มีตาราง `FactResellerSales` (Fact Table) ที่มี `ProductKey`
- มีตาราง `DimProduct` (Dimension Table) ที่มี `ProductKey` และ `EnglishProductName`
- Relationship: FactResellerSales[ProductKey] → DimProduct[ProductKey] (Many-to-One)

**สร้าง Calculated Column ในตาราง FactResellerSales:**
```dax
Product Name = RELATED(DimProduct[EnglishProductName])
```

**ตัวอย่างที่ 2: ดึงข้อมูลหลายคอลัมน์**

```dax
// คอลัมน์ 1: ชื่อสินค้า
Product Name = RELATED(DimProduct[EnglishProductName])

// คอลัมน์ 2: หมวดหมู่สินค้า
Product Category = RELATED(DimProduct[ProductCategory])
```

### 📝 RELATEDTABLE()

**Syntax:**
```dax
RELATEDTABLE(<table>)
```

**คำอธิบาย:**
- ดึงข้อมูลจาก Related Table (One-to-Many relationship)
- คืนค่าเป็น Table
- ใช้ใน Row Context หรือใน CALCULATE()

**ตัวอย่างที่ 1: นับจำนวนการขายของแต่ละสินค้า (AdventureWorksDW)**

**สถานการณ์:**
- ตาราง `DimProduct` (One)
- ตาราง `FactResellerSales` (Many)
- Relationship: DimProduct[ProductKey] → FactResellerSales[ProductKey] (One-to-Many)

**สร้าง Calculated Column ในตาราง DimProduct:**
```dax
Sales Count = COUNTROWS(RELATEDTABLE(FactResellerSales))
```

**ตัวอย่างที่ 2: คำนวณยอดขายรวมของแต่ละสินค้า**

```dax
Total Sales = 
SUMX(
    RELATEDTABLE(FactResellerSales),
    FactResellerSales[SalesAmount]
)
```

### ⚠️ ข้อควรระวัง

**❌ ผิด - ใช้ RELATED() ใน Measure:**
```dax
// ❌ ผิด! RELATED() ไม่สามารถใช้ใน Measure ได้
Total Product Name = RELATED(DimProduct[EnglishProductName])
```

**✅ ถูก - ใช้ใน Calculated Column:**
```dax
// ✅ ถูก! RELATED() ใช้ได้ใน Calculated Column
Product Name = RELATED(DimProduct[EnglishProductName])
```

**✅ ถูก - ใช้ใน Iterator Function:**
```dax
// ✅ ถูก! RELATED() ใช้ได้ใน SUMX (Iterator Function)
Total Sales by Category = 
SUMX(
    FactResellerSales,
    RELATED(DimProduct[StandardCost]) * FactResellerSales[OrderQuantity]
)
```

---

## 2. CALCULATE() และ Relationship Filters {#2-calculate-และ-relationship-filters-part-2}

### 🎯 จุดประสงค์

เรียนรู้วิธีใช้ CALCULATE() เพื่อควบคุม Filter Context ผ่าน Relationships

### 📝 CALCULATE() พื้นฐาน

**Syntax:**
```dax
CALCULATE(<expression>, <filter1>, <filter2>, ...)
```

**คำอธิบาย:**
- CALCULATE() เป็น Function ที่สำคัญที่สุดใน DAX
- ใช้เพื่อควบคุม Filter Context
- สามารถ Filter ผ่าน Relationships ได้

**ตัวอย่างที่ 1: Filter ผ่าน Relationship (AdventureWorksDW)**

**สถานการณ์:**
- มีตาราง `FactResellerSales` และ `DimProduct`
- Relationship: FactResellerSales[ProductKey] → DimProduct[ProductKey]

**สร้าง Measure:**
```dax
Sales - Bikes = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DimProduct[ProductCategoryKey] = 1  -- หรือใช้ DimProduct[ProductCategoryName] = "Bikes"
)
```

**การทำงาน:**
1. Filter DimProduct[ProductCategoryKey] = 1
2. Filter แพร่กระจายผ่าน Relationship ไปยัง FactResellerSales
3. คำนวณ SUM(FactResellerSales[SalesAmount]) เฉพาะสินค้า Bikes

**ตัวอย่างที่ 2: Filter หลายเงื่อนไข**

```dax
Sales - Bikes 2023 = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    DimProduct[ProductCategoryName] = "Bikes",
    DimDate[CalendarYear] = 2023
)
```

### 📝 ALL() และ Relationship Context

**ALL()** - ลบ Filter ทั้งหมดออกจากคอลัมน์หรือตาราง

**ตัวอย่างที่ 1: คำนวณยอดขายรวมของทุก Category**

```dax
Total Sales All Categories = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    ALL(DimProduct[ProductCategoryName])
)
```

**ตัวอย่างที่ 2: คำนวณเปอร์เซ็นต์ของยอดขายแต่ละ Category**

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

---

## 3. USERELATIONSHIP() {#3-userelationship-part-2}

### 🎯 จุดประสงค์

เรียนรู้วิธีใช้ Inactive Relationships ใน DAX

### 📝 USERELATIONSHIP() พื้นฐาน

**Syntax:**
```dax
CALCULATE(
    <expression>,
    USERELATIONSHIP(<column1>, <column2>)
)
```

**คำอธิบาย:**
- ใช้เพื่อ Activate Inactive Relationship ชั่วคราว
- ต้องกำหนด Inactive Relationship ใน Model ก่อน
- ใช้เฉพาะใน CALCULATE()

### 🔧 การตั้งค่า Inactive Relationship

**ขั้นตอน:**
1. เปิด Model View
2. สร้าง Relationship ระหว่างสองตาราง
3. คลิกขวาที่ Relationship → Properties
4. **ยกเลิก** Active checkbox
5. Relationship จะกลายเป็น Inactive

### 📝 ตัวอย่าง: Role-Playing Dimensions (AdventureWorksDW)

**สถานการณ์:**
- ตาราง `FactResellerSales` มี `OrderDateKey`, `ShipDateKey`, และ `DueDateKey`
- ตาราง `DimDate` มี `DateKey`
- สร้าง 3 Relationships:
  - Active: FactResellerSales[OrderDateKey] → DimDate[DateKey]
  - Inactive: FactResellerSales[ShipDateKey] → DimDate[DateKey]
  - Inactive: FactResellerSales[DueDateKey] → DimDate[DateKey]

**ตัวอย่างที่ 1: คำนวณยอดขายตาม Order Date (ใช้ Active Relationship)**

```dax
Sales by Order Date = 
SUM(FactResellerSales[SalesAmount])
// ใช้ Active Relationship โดยอัตโนมัติ
```

**ตัวอย่างที่ 2: คำนวณยอดขายตาม Ship Date (ใช้ Inactive Relationship)**

```dax
Sales by Ship Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    USERELATIONSHIP(FactResellerSales[ShipDateKey], DimDate[DateKey])
)
```

**ตัวอย่างที่ 3: คำนวณยอดขายตาม Due Date**

```dax
Sales by Due Date = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    USERELATIONSHIP(FactResellerSales[DueDateKey], DimDate[DateKey])
)
```

---

## 4. CROSSFILTER() {#4-crossfilter-part-2}

### 🎯 จุดประสงค์

เรียนรู้วิธีควบคุม Cross Filter Direction แบบ Dynamic

### 📝 CROSSFILTER() พื้นฐาน

**Syntax:**
```dax
CALCULATE(
    <expression>,
    CROSSFILTER(<column1>, <column2>, <direction>)
)
```

**Direction:**
- `BOTH` - Filter ทั้งสองทิศทาง
- `ONEWAY` - Filter ทิศทางเดียว (Many → One)
- `NONE` - ไม่มี Filter

### 📝 ตัวอย่างการใช้งาน (AdventureWorksDW)

**สถานการณ์:**
- Relationship: FactResellerSales[ProductKey] → DimProduct[ProductKey] (Single Direction)

**ตัวอย่างที่ 1: เปลี่ยนเป็น Both Direction ชั่วคราว**

```dax
Sales - Both Direction = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    CROSSFILTER(FactResellerSales[ProductKey], DimProduct[ProductKey], BOTH)
)
```

**ตัวอย่างที่ 2: ปิด Filter Direction ชั่วคราว**

```dax
Sales - No Filter = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    CROSSFILTER(FactResellerSales[ProductKey], DimProduct[ProductKey], NONE)
)
```

---

## 5. TREATAS() {#5-treatas-part-2}

### 🎯 จุดประสงค์

เรียนรู้วิธีใช้ TREATAS() เพื่อสร้าง Virtual Relationships

### 📝 TREATAS() พื้นฐาน

**Syntax:**
```dax
TREATAS(<table>, <column1>[, <column2>, ...])
```

**คำอธิบาย:**
- สร้าง Virtual Relationship โดยไม่ต้องสร้าง Relationship จริง
- ใช้เมื่อต้องการเชื่อมความสัมพันธ์ชั่วคราว

**ตัวอย่าง (AdventureWorksDW):**

ต้องการหาจำนวนพนักงานที่จ้างในแต่ละปี แต่ไม่ต้องการสร้าง Relationship ระหว่าง DimDate และ DimEmployee

```dax
Employees Hired by Year = 
CALCULATE(
    DISTINCTCOUNT(DimEmployee[EmployeeKey]),
    TREATAS(
        VALUES(DimDate[DateKey]),
        DimEmployee[HireDateKey]
    )
)
```

---

## 6. ALLRELATED() และ ALLSELECTED() {#6-allrelated-และ-allselected-part-2}

### 🎯 จุดประสงค์

เข้าใจความแตกต่างระหว่าง ALL(), ALLRELATED(), และ ALLSELECTED()

### 📝 ALLRELATED()

**Syntax:**
```dax
ALLRELATED(<table or column>)
```

**คำอธิบาย:**
- ลบ Filter เฉพาะที่มาจาก Related Tables
- คง Filter ที่มาจาก External Slicers หรือ Visual Filters

**ตัวอย่าง:**

```dax
Total Sales All Related = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    ALLRELATED(DimProduct)
)
```

### 📝 ALLSELECTED()

**Syntax:**
```dax
ALLSELECTED(<table or column>)
```

**คำอธิบาย:**
- คง Filter ที่มาจาก Visual Level
- ลบ Filter ภายใน Visual แต่คง Filter จาก Slicers

**ตัวอย่าง:**

```dax
Total Sales Selected = 
CALCULATE(
    SUM(FactResellerSales[SalesAmount]),
    ALLSELECTED(DimProduct[ProductCategoryName])
)
```

### 📊 เปรียบเทียบ

| Function | ลบ Filter จาก | คง Filter จาก |
|----------|--------------|---------------|
| **ALL()** | ทุกที่ | ไม่มี |
| **ALLRELATED()** | Related Tables เท่านั้น | External Slicers |
| **ALLSELECTED()** | ภายใน Visual | Visual Level, Slicers |

---

## 7. Filter Context และ Relationships {#7-filter-context-และ-relationships-part-2}

### 🎯 จุดประสงค์

เข้าใจการทำงานของ Filter Context ผ่าน Relationships ในบริบทของ DAX

### 📝 Filter Context ใน DAX

**Filter Context** = เงื่อนไขการกรองข้อมูลที่ใช้งานอยู่ในปัจจุบัน

**การทำงานกับ Relationships:**
- Filter Context แพร่กระจายผ่าน Relationships ตาม Cross Filter Direction ที่ตั้งไว้ (ดูใน [ส่วนที่ 4](#4-cross-filter-direction-และ-filter-context-propagation))
- **Single Direction**: Filter Context ไปจาก "One" ไป "Many" (Default)
- **Both Direction**: Filter Context ไปทั้งสองทิศทาง

**👉 หมายเหตุ:** สำหรับรายละเอียดเรื่อง Filter Context Propagation, Single Direction, และ Both Direction ดูใน [ส่วนที่ 4](#4-cross-filter-direction-และ-filter-context-propagation)

---

> **📝 แบบฝึกหัด Part 2:** ดูแบบฝึกหัดทั้งหมดใน [EXERCISES.md](./EXERCISES.md)

---

## 📝 สรุปและ Best Practices

### ✅ Best Practices

1. **ใช้ RELATED() ใน Calculated Columns**
   - ใช้สำหรับดึงข้อมูลจาก Related Tables
   - ไม่สามารถใช้ใน Measures ได้โดยตรง

2. **ใช้ RELATEDTABLE() สำหรับการนับหรือคำนวณ**
   - ใช้ใน Calculated Columns เพื่อนับหรือคำนวณจาก Related Table

3. **ใช้ CALCULATE() เพื่อควบคุม Filter Context**
   - Filter ผ่าน Relationships โดยใช้ชื่อคอลัมน์จาก Related Table

4. **ใช้ USERELATIONSHIP() สำหรับ Inactive Relationships**
   - ต้องกำหนด Inactive Relationship ใน Model ก่อน

5. **ใช้ CROSSFILTER() เมื่อต้องเปลี่ยน Filter Direction ชั่วคราว**
   - ควรใช้เฉพาะเมื่อจำเป็น เพราะอาจมีผลกระทบต่อ Performance

6. **เข้าใจ ALL(), ALLRELATED(), ALLSELECTED()**
   - ใช้ให้เหมาะสมกับสถานการณ์

### ⚠️ สิ่งที่ควรระวัง

1. **อย่าใช้ RELATED() ใน Measures**
   - ใช้ใน Calculated Columns หรือ Iterator Functions แทน

2. **ระวัง Performance**
   - Both Direction Filtering อาจช้ากว่า Single Direction
   - ใช้ CROSSFILTER() แบบ Dynamic เมื่อจำเป็นเท่านั้น

3. **เข้าใจ Filter Context**
   - Filter Context มีความสำคัญมากใน DAX
   - ควร Debug และทดสอบเสมอ
   - เข้าใจว่า Filter Context แพร่กระจายตาม Cross Filter Direction

---

## 📚 เอกสารที่เกี่ยวข้อง

- **01-Introduction & VertiPaq Engine**: เข้าใจ VertiPaq จะช่วยในการออกแบบ Relationships
- **04-Dimensional-Model**: Star Schema เป็นพื้นฐานของ Relationships
- **06-Dimension-Table-Design**: Role-Playing Dimensions
- **08-Advanced-Modeling**: เทคนิคขั้นสูงของ Relationships

---

**🎉 ขอแสดงความยินดี! คุณได้เรียนจบโมดูล Relationships แล้ว (ทั้ง Part 1 และ Part 2)!**

**ขั้นตอนต่อไป:** 
- ฝึกปฏิบัติด้วยไฟล์ตัวอย่างจาก AdventureWorksDW
- เรียนโมดูล 05-Dimension-Table-Design
- เรียนโมดูล 06-Date-Dimensions-Relationships
