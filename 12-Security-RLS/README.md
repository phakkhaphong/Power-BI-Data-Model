# 12 - Security & Row-Level Security (RLS)

## เนื้อหาหลักสูตร

โมดูลนี้เกี่ยวกับการจัดการความปลอดภัยและ Row-Level Security ใน Power BI Semantic Model

> **หมายเหตุ:** Security และ RLS รันได้ทั้ง **Power BI Desktop** และ **Power BI Service** แต่การทดสอบ Security Roles และการจัดการ Security บน Service ต้องใช้ **Power BI Service**

### หัวข้อการเรียนรู้

- แนวคิดของ Security ใน Semantic Model
- การกำหนด Roles
- Row-Level Security (RLS) แบบ Static
- Row-Level Security (RLS) แบบ Dynamic
- การใช้ DAX สำหรับ RLS Filters
- Object-Level Security (OLS)
- Table-Level Security
- การทดสอบ Security Roles
- การจัดการ Security บน Power BI Service
- การใช้ USERNAME() และ USERPRINCIPALNAME()
- การใช้ CUSTOMDATA() สำหรับ Dynamic RLS
- Security Best Practices

### วัตถุประสงค์

หลังจากจบโมดูลนี้ ผู้เรียนจะสามารถ:
- สร้างและจัดการ Security Roles ได้
- ใช้งาน Row-Level Security แบบ Static และ Dynamic ได้
- เขียน DAX Filters สำหรับ Security ได้อย่างถูกต้อง
- ทดสอบ Security Roles ได้
- นำ Best Practices ไปใช้ในการจัดการ Security ได้

### ประเด็นสำคัญ

- Static RLS: กำหนดค่าตายตัวสำหรับแต่ละ Role
- Dynamic RLS: ใช้ DAX เพื่อกำหนด Security ตามเงื่อนไข
- Security Inheritance: การสืบทอด Security ระหว่าง Roles
- Performance Impact: ผลกระทบของ Security ต่อ Performance

> **หมายเหตุ:** การสร้าง Security Roles และทดสอบเบื้องต้นทำได้ใน **Power BI Desktop** แต่การทดสอบแบบเต็มรูปแบบและการจัดการ Security บน Production ต้องใช้ **Power BI Service**

