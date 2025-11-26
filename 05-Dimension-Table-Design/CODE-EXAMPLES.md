# Code Examples - Dimension Table Design

## 📝 เอกสารตัวอย่างโค้ด Power Query และ DAX

ไฟล์นี้รวบรวมตัวอย่างโค้ดสำหรับการออกแบบ Dimension Table โดยเฉพาะ SCD Type 2

> **ไฟล์ตัวอย่าง:** `Data Model SCD.SemanticModel` และ `Data Model SCD.Report`

---

## 🎯 SCD Type 2 - Power Query Implementation

### ภาพรวมของโครงสร้าง

**ตารางที่ใช้:**
- `SourceTable` - ตารางข้อมูลต้นทาง
- `Dimension` - ตาราง Dimension ปัจจุบัน
- `FullLoad` - ตาราง Dimension ที่อัพเดทแล้ว (SCD Type 2)

**คอลัมน์ในตาราง FullLoad:**
- `SalesRepID` - Surrogate Key
- `RepSourceID` - Business Key
- `FirstName`, `LastName`, `Region` - Dimension Attributes
- `StartDate` - วันที่เริ่มต้น
- `EndDate` - วันที่สิ้นสุด
- `IsCurrent` - Flag ว่าบันทึกปัจจุบันหรือไม่
- `Hash` - สำหรับตรวจสอบการเปลี่ยนแปลง

---

### 1. SourceTable - ตารางข้อมูลต้นทาง

```m
let
  Source = Table.FromRows(Json.Document(...), ...),
  #"Changed column type" = Table.TransformColumnTypes(Source, {{"RepSourceID", Int64.Type}}),
  #"Added custom" = Table.TransformColumnTypes(
    Table.AddColumn(#"Changed column type", "Hash", 
      each Binary.ToText(
        Text.ToBinary(
          Text.Combine(
            List.Transform({[RepSourceID],[FirstName],[LastName],[Region]}, 
              each if _ = null then "" else Text.From(_)
            ), "|"
          )
        ), BinaryEncoding.Hex
      )
    ), 
    {{"Hash", type text}}
  )
in
  #"Added custom"
```

**อธิบาย:**
- สร้าง Hash จากคอลัมน์สำคัญ (RepSourceID, FirstName, LastName, Region)
- ใช้ Hash เพื่อเปรียบเทียบการเปลี่ยนแปลง

---

### 2. Dimension - ตาราง Dimension ปัจจุบัน

```m
let
  Source = Table.FromRows(Json.Document(...), ...),
  #"Changed Type" = Table.TransformColumnTypes(Source, {
    {"SalesRepID", Int64.Type}, 
    {"RepSourceID", Int64.Type}, 
    {"FirstName", type text}, 
    {"LastName", type text}, 
    {"Region", type text}, 
    {"StartDate", type text}, 
    {"EndDate", type text}, 
    {"IsCurrent", type logical}, 
    {"Hash", type text}
  })
in
  #"Changed Type"
```

**อธิบาย:**
- ตาราง Dimension ที่มีข้อมูล SCD Type 2
- มี SalesRepID (Surrogate Key) และ Hash

---

### 3. AggregatedDimHash - รวม Hash จาก Dimension

```m
let
  Source = Dimension,
  #"Grouped rows" = Table.Group(Source, {"Hash"}, {
    {"Count", each Table.RowCount(_), Int64.Type}
  })
in
  #"Grouped rows"
```

**อธิบาย:**
- รวม Hash จาก Dimension เพื่อเปรียบเทียบกับ SourceTable
- ใช้เพื่อหาว่ามี Hash ใหม่หรือไม่

---

### 4. CompareStoM - เปรียบเทียบ Source กับ Model

```m
let
  Source = Table.NestedJoin(
    SourceTable, {"Hash"}, 
    AggregatedDimHash, {"Hash"}, 
    "AggregatedDimHash", 
    JoinKind.LeftAnti
  ),
  #"Expanded AggregatedDimHash" = Table.ExpandTableColumn(
    Source, "AggregatedDimHash", {"Count"}, {"Count"}
  ),
  #"Filtered rows" = Table.SelectRows(#"Expanded AggregatedDimHash", 
    each ([Count] = null)
  ),
  #"Removed columns" = Table.RemoveColumns(#"Filtered rows", {"Count"})
in
  #"Removed columns"
```

**อธิบาย:**
- ใช้ LeftAnti Join เพื่อหา Hash ที่มีใน SourceTable แต่ไม่มีใน Dimension
- นี่คือ Records ใหม่ที่ต้องเพิ่ม

---

### 5. LastID - หา ID ล่าสุด

```m
let
  Source = Dimension,
  #"Drill down" = Source[SalesRepID],
  #"Calculated maximum" = List.Max(#"Drill down"),
  Custom = try #"Calculated maximum" + 1 otherwise 1
in
  Custom
```

**อธิบาย:**
- หา SalesRepID สูงสุดจาก Dimension
- คำนวณ ID ต่อไปสำหรับ Records ใหม่

---

### 6. NewRecords - สร้าง Records ใหม่

```m
let
  Source = CompareStoM,
  #"Added index" = Table.AddIndexColumn(Source, "Index", LastID, 1, Int64.Type),
  #"Added custom" = Table.TransformColumnTypes(
    Table.AddColumn(#"Added index", "StartDate", 
      each Date.From(DateTime.LocalNow())
    ), 
    {{"StartDate", type date}}
  ),
  #"Added custom 1" = Table.TransformColumnTypes(
    Table.AddColumn(#"Added custom", "EndDate", 
      each #date(9999,12,31)
    ), 
    {{"EndDate", type date}}
  ),
  #"Added custom 2" = Table.TransformColumnTypes(
    Table.AddColumn(#"Added custom 1", "IsCurrent", 
      each true
    ), 
    {{"IsCurrent", type logical}}
  ),
  #"Renamed columns" = Table.RenameColumns(#"Added custom 2", {
    {"Index", "SalesRepID"}
  })
in
  #"Renamed columns"
```

**อธิบาย:**
- สร้าง SalesRepID ใหม่โดยใช้ LastID
- ตั้ง StartDate = วันนี้
- ตั้ง EndDate = 9999-12-31 (แสดงว่ายังใช้ได้)
- ตั้ง IsCurrent = true

---

### 7. RecordsToUpdate - Records ที่ต้องอัพเดท

```m
let
  Source = Table.NestedJoin(
    Dimension, {"Hash"}, 
    SourceTable, {"Hash"}, 
    "SourceTable", 
    JoinKind.LeftAnti
  ),
  #"Expanded SourceTable" = Table.ExpandTableColumn(
    Source, "SourceTable", {"Hash"}, {"Hash.1"}
  ),
  #"Removed columns" = Table.RemoveColumns(#"Expanded SourceTable", {"Hash.1"}),
  #"Replaced value" = Table.ReplaceValue(
    #"Removed columns", 
    true, false, 
    Replacer.ReplaceValue, 
    {"IsCurrent"}
  ),
  #"Replaced value 1" = Table.ReplaceValue(
    #"Replaced value", 
    each[EndDate], 
    Date.From(DateTime.LocalNow()), 
    Replacer.ReplaceValue, 
    {"EndDate"}
  )
in
  #"Replaced value 1"
```

**อธิบาย:**
- หา Records ที่มีใน Dimension แต่ไม่มี Hash ใน SourceTable
- นี่คือ Records ที่มีการเปลี่ยนแปลง (Hash เปลี่ยน)
- ตั้ง IsCurrent = false (ปิด Records เก่า)
- ตั้ง EndDate = วันนี้ (จบ Records เก่า)

---

### 8. StagingTableForUpdates - รวม NewRecords และ RecordsToUpdate

```m
let
  Source = Table.Combine({NewRecords, RecordsToUpdate}),
  #"Reordered columns" = Table.ReorderColumns(Source, {
    "SalesRepID", 
    "RepSourceID", 
    "FirstName", 
    "LastName", 
    "Region", 
    "StartDate", 
    "EndDate", 
    "IsCurrent", 
    "Hash"
  })
in
  #"Reordered columns"
```

**อธิบาย:**
- รวม NewRecords และ RecordsToUpdate
- เรียงลำดับคอลัมน์ให้ถูกต้อง

---

### 9. FullLoad - ตารางสุดท้าย

```m
let
  Source = Table.NestedJoin(
    Dimension, {"SalesRepID"}, 
    RecordsToUpdate, {"SalesRepID"}, 
    "RecordsToUpdate", 
    JoinKind.LeftAnti
  ),
  #"Expanded RecordsToUpdate" = Table.ExpandTableColumn(
    Source, "RecordsToUpdate", {"Hash"}, {"Hash.1"}
  ),
  #"Removed columns" = Table.RemoveColumns(#"Expanded RecordsToUpdate", {"Hash.1"}),
  #"Appended query" = Table.Combine({#"Removed columns", StagingTableForUpdates}),
  #"Sorted rows" = Table.Sort(#"Appended query", {
    {"SalesRepID", Order.Ascending}
  })
in
  #"Sorted rows"
```

**อธิบาย:**
- ลบ Records ที่ต้องอัพเดทออกจาก Dimension เดิม
- รวมกับ StagingTableForUpdates (NewRecords + RecordsToUpdate)
- เรียงตาม SalesRepID

---

## 🎯 SCD Type 2 - DAX Examples

### ตัวอย่างที่ 1: Filter ตาม IsCurrent

**สถานการณ์:** คำนวณยอดขายของ Sales Rep ปัจจุบันเท่านั้น

```dax
Sales - Current Reps = 
CALCULATE(
    SUM(FactSales[SalesAmount]),
    FullLoad[IsCurrent] = TRUE
)
```

---

### ตัวอย่างที่ 2: Filter ตาม Date Range

**สถานการณ์:** คำนวณยอดขายของ Sales Rep ในช่วงเวลาที่กำหนด

```dax
Sales - By Date Range = 
CALCULATE(
    SUM(FactSales[SalesAmount]),
    FullLoad[StartDate] <= SELECTEDVALUE('Date'[DateKey]),
    FullLoad[EndDate] >= SELECTEDVALUE('Date'[DateKey])
)
```

---

### ตัวอย่างที่ 3: RELATED() กับ SCD Type 2

**สถานการณ์:** สร้าง Calculated Column ใน Fact Table

```dax
Current Region = 
RELATED(FullLoad[Region])
```

**หมายเหตุ:** ถ้ามีหลาย Records ใน FullLoad ที่มี RepSourceID เดียวกัน อาจต้องใช้ FILTER() แทน

---

### ตัวอย่างที่ 4: หา Current Record

**สถานการณ์:** สร้าง Measure เพื่อหา Region ปัจจุบัน

```dax
Current Region = 
CALCULATE(
    MAX(FullLoad[Region]),
    FullLoad[IsCurrent] = TRUE,
    FullLoad[RepSourceID] = SELECTEDVALUE(FactSales[RepSourceID])
)
```

---

## 🎯 Calculated Columns สำหรับ Dimension Tables

### ตัวอย่างที่ 1: ดึงข้อมูลจาก Related Table

**สถานการณ์:** สร้าง Calculated Column ในตาราง `DimProduct` เพื่อดึงชื่อหมวดหมู่สินค้า

```dax
Category Name = RELATED(DimProductCategory[EnglishProductCategoryName])
```

**อธิบาย:**
- ใช้ `RELATED()` เพื่อดึงข้อมูลจาก Related Table
- ต้องมี Relationship ระหว่าง DimProduct และ DimProductCategory

---

### ตัวอย่างที่ 2: ดึง Subcategory จาก Related Table

**สถานการณ์:** สร้าง Calculated Column ในตาราง `DimProduct` เพื่อดึงชื่อ Subcategory

```dax
Subcategory Name = RELATED(DimProductSubcategory[EnglishProductSubcategoryName])
```

---

### ตัวอย่างที่ 3: สร้าง Full Name

**สถานการณ์:** สร้าง Calculated Column ในตาราง `DimCustomer` เพื่อรวมชื่อและนามสกุล

```dax
Full Name = DimCustomer[FirstName] & " " & DimCustomer[LastName]
```

**อธิบาย:**
- ใช้ `&` เพื่อเชื่อมต่อข้อความ
- สามารถใช้สำหรับแสดงผลใน Visualizations

---

### ตัวอย่างที่ 4: จำแนกประเภทจาก Attributes

**สถานการณ์:** สร้าง Calculated Column ในตาราง `DimProduct` เพื่อจำแนกขนาดสินค้า

```dax
Size Category = 
IF(
    DimProduct[Size] = "L" || DimProduct[Size] = "XL",
    "Large",
    IF(
        DimProduct[Size] = "M",
        "Medium",
        "Small"
    )
)
```

---

### ตัวอย่างที่ 5: การใช้ RELATED() ใน Snowflake Schema

**สถานการณ์:** ใน Snowflake Schema ที่มี DimProduct → DimProductSubcategory → DimProductCategory

```dax
// ใน DimProduct
Category = RELATED(DimProductSubcategory[EnglishProductCategoryName])
```

**หมายเหตุ:**
- RELATED() จะทำงานผ่าน Relationships ทั้ง chain
- ถ้ามี Relationship: DimProduct → DimProductSubcategory → DimProductCategory
- สามารถดึง Category ผ่าน RELATED() ได้

---

## 🎯 Calculated Tables สำหรับ Dimension Tables

### ตัวอย่างที่ 6: สร้าง Dimension Table จากหลาย Sources

**สถานการณ์:** สร้าง Calculated Table เพื่อรวม Product Categories และ Subcategories

```dax
Product Categories = 
DISTINCT(
    SELECTCOLUMNS(
        DimProduct,
        "CategoryName", RELATED(DimProductCategory[EnglishProductCategoryName]),
        "SubcategoryName", RELATED(DimProductSubcategory[EnglishProductSubcategoryName])
    )
)
```

**อธิบาย:**
- ใช้ DISTINCT() เพื่อลบค่าซ้ำ
- ใช้ SELECTCOLUMNS() เพื่อเลือกคอลัมน์ที่ต้องการ
- ใช้ RELATED() เพื่อดึงข้อมูลจาก Related Tables

---

### ตัวอย่างที่ 7: สร้าง Lookup Table

**สถานการณ์:** สร้าง Lookup Table สำหรับ Product Categories

```dax
Category Lookup = 
ADDCOLUMNS(
    VALUES(DimProductCategory[EnglishProductCategoryName]),
    "CategoryKey", RANKX(
        ALL(DimProductCategory[EnglishProductCategoryName]),
        DimProductCategory[EnglishProductCategoryName],
        ,
        ASC,
        Dense
    )
)
```

---

## 🎯 Attribute Hierarchies - Parent-Child Hierarchy

### ตัวอย่างที่ 8: สร้าง Path Column

**โครงสร้างตาราง Employee:**
- `EmployeeKey` - Key ของ Employee
- `ParentEmployeeKey` - Key ของ Manager/Parent

**สร้าง Calculated Column:**
```dax
Path = PATH(Employee[EmployeeKey], Employee[ParentEmployeeKey])
```

**ผลลัพธ์ตัวอย่าง:**
- EmployeeKey = 1, ParentEmployeeKey = NULL → `"1"`
- EmployeeKey = 2, ParentEmployeeKey = 1 → `"1|2"`
- EmployeeKey = 3, ParentEmployeeKey = 2 → `"1|2|3"`

---

### ตัวอย่างที่ 9: สร้าง Path Length Column

**สร้าง Calculated Column:**
```dax
PathLEN = PATHLENGTH(Employee[Path])
```

**ผลลัพธ์ตัวอย่าง:**
- Path = `"1"` → PathLEN = `1`
- Path = `"1|2"` → PathLEN = `2`
- Path = `"1|2|3"` → PathLEN = `3`

---

### ตัวอย่างที่ 10: สร้าง Org Level Columns

**Org Level 1 (CEO/Root):**
```dax
Org Level 1 = 
LOOKUPVALUE(
    Employee[FullName],
    Employee[EmployeeKey],
    PATHITEM(Employee[Path], 1, INTEGER)
)
```

**Org Level 2 (VP/Director):**
```dax
Org Level 2 = 
LOOKUPVALUE(
    Employee[FullName],
    Employee[EmployeeKey],
    PATHITEM(Employee[Path], 2, INTEGER)
)
```

**Org Level 3 (Manager):**
```dax
Org Level 3 = 
LOOKUPVALUE(
    Employee[FullName],
    Employee[EmployeeKey],
    PATHITEM(Employee[Path], 3, INTEGER)
)
```

---

### ตัวอย่างที่ 11: หา Manager ของ Employee

**สร้าง Calculated Column:**
```dax
Manager Name = 
VAR ParentKey = Employee[ParentEmployeeKey]
RETURN
    IF(
        ISBLANK(ParentKey),
        "No Manager",
        LOOKUPVALUE(Employee[FullName], Employee[EmployeeKey], ParentKey)
    )
```

---

### ตัวอย่างที่ 12: หา Root Employee

**สร้าง Calculated Column:**
```dax
Root Employee = 
LOOKUPVALUE(
    Employee[FullName],
    Employee[EmployeeKey],
    PATHITEM(Employee[Path], 1, INTEGER)
)
```

---

## 📚 สรุปขั้นตอนการทำ SCD Type 2

### ขั้นตอนที่ 1: สร้าง Hash
- สร้าง Hash จาก Dimension Attributes ที่สำคัญ
- ใช้เพื่อเปรียบเทียบการเปลี่ยนแปลง

### ขั้นตอนที่ 2: เปรียบเทียบ Source กับ Dimension
- หา Records ใหม่ (NewRecords)
- หา Records ที่เปลี่ยน (RecordsToUpdate)

### ขั้นตอนที่ 3: อัพเดท Dimension
- เพิ่ม NewRecords ใหม่
- ปิด Records เก่า (IsCurrent = false, EndDate = วันนี้)
- เพิ่ม Records ใหม่ (IsCurrent = true, StartDate = วันนี้)

---

## 🎯 Best Practices

### ✅ ใช้ Calculated Columns ใน Dimension Tables เมื่อ:

1. **ต้องการดึงข้อมูลจาก Related Table**
   - ใช้ RELATED() เพื่อ Flatten Snowflake Schema
   - ลดความซับซ้อนใน Relationships

2. **ต้องการค่าคงที่ที่ใช้ในการ Filter/Sort**
   - สร้าง Categories หรือ Classifications
   - ใช้ในการสร้าง Hierarchies

3. **ต้องการสร้าง Parent-Child Hierarchy**
   - ใช้ PATH Functions
   - สร้าง Org Level Columns

4. **ต้องการ Performance ที่ดี**
   - Calculated Columns คำนวณตอน Refresh
   - ใช้ได้เลยโดยไม่ต้องคำนวณใหม่

### ❌ ไม่ควรใช้ Calculated Columns เมื่อ:

1. **ต้องการค่าที่เปลี่ยนตาม Visual Context**
   - ใช้ Measures แทน

2. **ต้องการคำนวณ Aggregate**
   - ใช้ Measures แทน

---

**📖 เอกสารที่เกี่ยวข้อง:**
- [README.md](./README.md) - เอกสารหลักของโมดูล
- [EXERCISES.md](./EXERCISES.md) - แบบฝึกหัด

