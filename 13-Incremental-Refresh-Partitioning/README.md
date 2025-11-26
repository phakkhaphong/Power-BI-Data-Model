# 13 - Incremental Refresh & Partitioning

## เนื้อหาหลักสูตร

โมดูลนี้เกี่ยวกับการทำ Incremental Refresh และ Partitioning สำหรับ Semantic Model ขนาดใหญ่

> **หมายเหตุ:** Incremental Refresh และ Partitioning **ต้องใช้ Power BI Service** (ต้องการ Premium License สำหรับบาง Features เช่น Large Models, XMLA Endpoints)

### หัวข้อการเรียนรู้

- แนวคิดของ Incremental Refresh
- เมื่อไหร่ควรใช้ Incremental Refresh
- การตั้งค่า Incremental Refresh Policy
- RangeStart และ RangeEnd Parameters
- การกำหนด Incremental Refresh Rules
- การจัดการ Partitions
- การสร้าง Partitions แบบ Manual
- การ Merge Partitions
- การลบ Partitions เก่า (Retention Policy)
- Real-time Data และ Incremental Refresh
- Hybrid Tables
- การ Monitor Incremental Refresh
- Troubleshooting Incremental Refresh
- Best Practices

### วัตถุประสงค์

หลังจากจบโมดูลนี้ ผู้เรียนจะสามารถ:
- ตั้งค่า Incremental Refresh ได้อย่างถูกต้อง
- จัดการ Partitions ได้
- กำหนด Retention Policies ได้
- Monitor และ Troubleshoot Incremental Refresh ได้
- ปรับปรุง Performance ของ Large Models ได้

### ประเด็นสำคัญ

- **Incremental Refresh**: Refresh เฉพาะข้อมูลใหม่หรือที่เปลี่ยนแปลง
- **Partitioning**: แบ่งตารางออกเป็นส่วนๆ เพื่อเพิ่มประสิทธิภาพ
- **Retention**: การเก็บข้อมูลเก่าไว้ตามนโยบาย
- **Hybrid Tables**: ผสมผสาน Import และ DirectQuery

### ข้อควรระวัง

- ต้องมีคอลัมน์วันที่สำหรับ Incremental Refresh
- ต้องกำหนด Policy ที่ชัดเจน
- ควรทดสอบก่อน Deploy Production
- **ต้องใช้ Power BI Service** - Incremental Refresh และ Partitioning ไม่สามารถทำได้ใน Power BI Desktop โดยตรง ต้อง Deploy ขึ้น Service ก่อน

