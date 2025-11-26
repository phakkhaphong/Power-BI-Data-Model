# Code Examples - Performance Optimization

## 📋 ไฟล์รวบรวม Code Examples สำหรับโมดูล Performance Optimization

ไฟล์นี้รวบรวมตัวอย่างและคำแนะนำสำหรับการปรับปรุงประสิทธิภาพของ Semantic Model

---

## 1. การวิเคราะห์ Performance ด้วย VertiPaq Analyzer

### ขั้นตอน

1. เปิด DAX Studio
2. เชื่อมต่อกับ Power BI Desktop
3. ไปที่แท็บ **VertiPaq Analyzer**
4. เลือกตารางที่ต้องการวิเคราะห์

### ตัวอย่างผลลัพธ์

```
Table: FactResellerSales

Column Name          | Cardinality | Size (KB) | Encoding
---------------------|-------------|-----------|------------
ProductKey           | 397         | 124       | Dictionary
OrderDateKey         | 1214        | 489       | Dictionary
SalesAmount          | 1234567     | 3456      | Value
OrderQuantity        | 156         | 89        | RLE
```

**วิเคราะห์:**
- คอลัมน์ที่มี Cardinality ต่ำ → Encoding ดี
- คอลัมน์ที่มี Cardinality สูง → ควรปรับปรุง

---

## 2. การวิเคราะห์ Query Performance ด้วย DAX Studio

### ตัวอย่าง Query

```dax
EVALUATE
SUMMARIZE(
    FactResellerSales,
    DimProduct[ProductName],
    "Total Sales", SUM(FactResellerSales[SalesAmount])
)
```

### การวิเคราะห์

1. เปิด DAX Studio
2. Paste Query ลงไป
3. กด **Run**
4. ดูผลลัพธ์:
   - Execution Time
   - Storage Engine Queries
   - Formula Engine Queries

---

## 3. การวิเคราะห์และวัดผล Performance Optimization

### ตัวอย่างการวัดผล

**Before Optimization:**
- Model Size: 250 MB
- Query Duration: 500ms
- Memory Usage: 1.2 GB

**After Optimization:**
- Model Size: 180 MB (-28%)
- Query Duration: 250ms (-50%)
- Memory Usage: 0.9 GB (-25%)

> **หมายเหตุ:** เทคนิคพื้นฐานในการ Optimize Performance (เช่น ลด Cardinality, เรียงข้อมูล, ใช้ Measures) มีอยู่ใน **09-Best-Practices**

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลักของโมดูล
- [EXERCISES.md](./EXERCISES.md) - แบบฝึกหัด

