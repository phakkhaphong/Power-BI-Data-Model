# Code Examples - Incremental Refresh & Partitioning

## 📋 ไฟล์รวบรวม Code Examples สำหรับโมดูล Incremental Refresh & Partitioning

ไฟล์นี้รวบรวมตัวอย่างการตั้งค่า Incremental Refresh และ Partitioning

---

## 1. Power Query Parameters

### RangeStart และ RangeEnd

```m
// RangeStart Parameter
let
    Source = DateTimeZone.ToUtc(#datetimezone(2024, 1, 1, 0, 0, 0, 0, 0))
in
    Source

// RangeEnd Parameter
let
    Source = DateTimeZone.ToUtc(DateTimeZone.LocalNow())
in
    Source
```

---

## 2. Incremental Refresh Query

```m
let
    Source = Sql.Database("server", "database"),
    Table = Source{[Schema="dbo",Item="Sales"]}[Data],
    #"Filtered Rows" = Table.SelectRows(Table, each [OrderDate] >= RangeStart and [OrderDate] < RangeEnd)
in
    #"Filtered Rows"
```

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลักของโมดูล
- [EXERCISES.md](./EXERCISES.md) - แบบฝึกหัด

