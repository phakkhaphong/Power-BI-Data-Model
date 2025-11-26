# Code Examples - Case Studies

## 📋 ไฟล์รวบรวม Code Examples เพิ่มเติม

ไฟล์นี้รวบรวม Code Examples เพิ่มเติมที่แตกต่างจากใน EXERCISES.md เช่น Pattern ที่ซับซ้อนกว่า, Optimization Techniques, และ Advanced DAX Patterns

> **หมายเหตุ:** Code ในไฟล์นี้เป็น Code เพิ่มเติมที่แตกต่างจากใน EXERCISES.md เพื่อให้ผู้เรียนได้เรียนรู้ Pattern อื่นๆ และเทคนิคขั้นสูง

---

## 1. Advanced Time Intelligence Patterns

### Rolling 12 Months Sales

```dax
Sales Rolling 12 Months = 
CALCULATE(
    [Total Sales],
    DATESBETWEEN(
        DimDate[Date],
        EDATE(MAX(DimDate[Date]), -11),
        MAX(DimDate[Date])
    )
)
```

### Moving Average (3 Months)

```dax
Sales Moving Average 3M = 
AVERAGEX(
    DATESINPERIOD(
        DimDate[Date],
        MAX(DimDate[Date]),
        -3,
        MONTH
    ),
    [Total Sales]
)
```

### Year-to-Date Last Year

```dax
Sales YTD LY = 
CALCULATE(
    [Total Sales],
    DATESYTD(SAMEPERIODLASTYEAR(DimDate[Date]))
)
```

---

## 2. Advanced Segmentation Patterns

### Dynamic Segmentation with Parameters

```dax
Customer Segment Dynamic = 
VAR TotalSales = [Total Sales]
VAR PlatinumThreshold = 10000
VAR GoldThreshold = 5000
RETURN
    SWITCH(
        TRUE(),
        TotalSales > PlatinumThreshold, "Platinum",
        TotalSales >= GoldThreshold, "Gold",
        "Silver"
    )
```

### Multiple Criteria Segmentation

```dax
Customer Segment Advanced = 
VAR TotalSales = [Total Sales]
VAR OrderCount = [Order Count]
VAR AvgOrderValue = DIVIDE(TotalSales, OrderCount, 0)
RETURN
    SWITCH(
        TRUE(),
        TotalSales > 10000 && AvgOrderValue > 500, "VIP",
        TotalSales > 10000, "Platinum",
        TotalSales >= 5000 && OrderCount > 10, "Gold Active",
        TotalSales >= 5000, "Gold",
        "Silver"
    )
```

---

## 3. Advanced Many-to-Many Patterns

### Bridge Table Pattern for Basket Analysis

```dax
Products Bought Together = 
VAR SelectedProduct = SELECTEDVALUE(DimProduct[ProductKey])
VAR OrdersWithSelectedProduct = 
    CALCULATETABLE(
        VALUES(FactResellerSales_Basket[OrderKey]),
        DimProduct[ProductKey] = SelectedProduct
    )
VAR ProductsInSameBasket = 
    CALCULATE(
        DISTINCTCOUNT(DimProduct[ProductKey]),
        OrdersWithSelectedProduct,
        ALL(DimProduct[ProductKey])
    )
RETURN
    ProductsInSameBasket - 1  // ลบ 1 เพื่อไม่นับสินค้าที่เลือกเอง
```

### Cross-Selling Opportunity

```dax
Cross-Sell Opportunity = 
VAR SelectedProduct = SELECTEDVALUE(DimProduct[ProductKey])
VAR CustomersWhoBoughtSelected = 
    CALCULATETABLE(
        VALUES(DimCustomer[CustomerKey]),
        DimProduct[ProductKey] = SelectedProduct
    )
VAR CustomersWhoBoughtOthers = 
    CALCULATE(
        DISTINCTCOUNT(DimCustomer[CustomerKey]),
        NOT(DimProduct[ProductKey] = SelectedProduct),
        CustomersWhoBoughtSelected
    )
RETURN
    CustomersWhoBoughtOthers
```

---

## 4. Performance Optimization Patterns

### Using Variables to Improve Performance

```dax
Sales with Discount Optimized = 
VAR TotalSalesAmount = SUM(FactResellerSales[SalesAmount])
VAR TotalDiscountAmount = SUM(FactResellerSales[DiscountAmount])
RETURN
    TotalSalesAmount - TotalDiscountAmount
```

### Filter Optimization

```dax
Sales Filtered Optimized = 
VAR FilteredProducts = 
    CALCULATETABLE(
        VALUES(DimProduct[ProductKey]),
        DimProduct[ProductCategoryName] = "Bikes"
    )
RETURN
    CALCULATE(
        [Total Sales],
        FilteredProducts
    )
```

---

## 5. Advanced Calculation Patterns

### Pareto Analysis (80/20 Rule)

```dax
Sales Cumulative % = 
VAR CurrentSales = [Total Sales]
VAR TotalSalesAll = 
    CALCULATE(
        [Total Sales],
        ALLSELECTED(DimProduct)
    )
VAR CumulativeSales = 
    CALCULATE(
        [Total Sales],
        FILTER(
            ALLSELECTED(DimProduct),
            [Total Sales] >= CurrentSales
        )
    )
RETURN
    DIVIDE(CumulativeSales, TotalSalesAll, 0)
```

### Rank with Ties Handling

```dax
Product Rank = 
VAR CurrentSales = [Total Sales]
RETURN
    COUNTROWS(
        FILTER(
            ALLSELECTED(DimProduct),
            [Total Sales] > CurrentSales
        )
    ) + 1
```

---

## 6. Advanced Date Patterns

### Fiscal Year Calculations

```dax
Sales Fiscal YTD = 
VAR CurrentDate = MAX(DimDate[Date])
VAR FiscalYearStart = 
    DATE(
        YEAR(CurrentDate) - IF(MONTH(CurrentDate) >= 7, 0, 1),
        7,
        1
    )
RETURN
    CALCULATE(
        [Total Sales],
        DimDate[Date] >= FiscalYearStart,
        DimDate[Date] <= CurrentDate
    )
```

### Week-over-Week Comparison

```dax
Sales WoW = 
VAR CurrentWeek = [Total Sales]
VAR LastWeek = 
    CALCULATE(
        [Total Sales],
        DATEADD(DimDate[Date], -7, DAY)
    )
RETURN
    DIVIDE(CurrentWeek - LastWeek, LastWeek, 0)
```

---

## 7. Advanced Parent-Child Patterns

### Employee Level Calculation

```dax
Employee Level = 
VAR PathLength = PATHLENGTH([HierarchyPath])
RETURN
    PathLength
```

### Sales by Manager (Rollup)

```dax
Sales by Manager = 
CALCULATE(
    [Total Sales],
    ALL(DimEmployee),
    FILTER(
        ALL(DimEmployee),
        PATHCONTAINS([HierarchyPath], SELECTEDVALUE(DimEmployee[ManagerID]))
    )
)
```

---

## 8. Error Handling Patterns

### Safe Division with Error Handling

```dax
Sales Growth Safe = 
VAR CurrentSales = [Total Sales]
VAR PreviousSales = [Sales LY]
VAR IsValid = 
    PreviousSales <> 0 && 
    NOT(ISBLANK(CurrentSales)) && 
    NOT(ISBLANK(PreviousSales))
RETURN
    IF(
        IsValid,
        DIVIDE(CurrentSales - PreviousSales, PreviousSales, 0),
        BLANK()
    )
```

### Handling Empty Sets

```dax
Sales Average if Exists = 
IF(
    COUNTROWS(VALUES(FactResellerSales[SalesAmount])) > 0,
    AVERAGE(FactResellerSales[SalesAmount]),
    BLANK()
)
```

---

## 9. Conditional Formatting Measures

### Color Coding Based on Performance

```dax
Performance Color = 
VAR Performance = [Quota Performance]
RETURN
    SWITCH(
        TRUE(),
        Performance >= 1.2, "#00FF00",  // เขียว (เกินเป้า 20%)
        Performance >= 1.0, "#90EE90",  // เขียวอ่อน (บรรลุเป้า)
        Performance >= 0.8, "#FFA500",  // ส้ม (ใกล้เป้า)
        "#FF0000"  // แดง (ต่ำกว่าเป้า)
    )
```

---

## 10. Dynamic Measures with Calculation Groups

### Time Intelligence with Calculation Groups

```dax
// Calculation Item: YTD
SELECTEDMEASURE()

// Calculation Item: QTD  
CALCULATE(
    SELECTEDMEASURE(),
    DATESQTD(DimDate[Date])
)

// Calculation Item: MTD
CALCULATE(
    SELECTEDMEASURE(),
    DATESMTD(DimDate[Date])
)
```

---

## 🔗 เอกสารที่เกี่ยวข้อง

- [README.md](./README.md) - เอกสารหลักของโมดูล
- [EXERCISES.md](./EXERCISES.md) - แบบฝึกหัด Step-by-Step
