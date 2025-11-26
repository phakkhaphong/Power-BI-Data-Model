# Code Examples - Best Practices

## 📋 ไฟล์รวบรวม Code Examples สำหรับโมดูล Best Practices

ไฟล์นี้รวบรวมตัวอย่างและคำแนะนำ Best Practices สำหรับการสร้าง Semantic Model

---

## 1. Naming Conventions

### ตัวอย่างการตั้งชื่อ

**Fact Tables:**
- FactResellerSales ✅
- FactInternetSales ✅
- FactSalesQuota ✅

**Dimension Tables:**
- DimProduct ✅
- DimDate ✅
- DimCustomer ✅

**Measures:**
- Total Sales ✅
- Sales YTD ✅
- Average Order Value ✅

---

## 2. Model Organization

### โครงสร้างที่แนะนำ

```
Semantic Model
├── Fact Tables (Center)
├── Dimension Tables (Around)
├── Calculation Groups
└── Helper Tables
```

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลักของโมดูล
- [EXERCISES.md](./EXERCISES.md) - แบบฝึกหัด

