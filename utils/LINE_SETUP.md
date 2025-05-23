# การตั้งค่า LINE Notification สำหรับระบบขอเอกสาร

## 📋 ขั้นตอนการตั้งค่า

### 1. ติดตั้ง dependencies

```bash
npm install @line/bot-sdk
```

### 2. ตั้งค่า LINE Bot ใน LINE Developers Console

1. ไปที่ [LINE Developers Console](https://developers.line.biz/)
2. สร้าง Provider และ Channel ใหม่ (Messaging API)
3. บันทึกข้อมูลต่อไปนี้:
   - Channel ID
   - Channel Secret
   - Channel Access Token

### 3. อัปเดตไฟล์ .env

```env
# LINE Bot Configuration
LINE_CHANNEL_ACCESS_TOKEN=your_channel_access_token_here
LINE_CHANNEL_SECRET=your_channel_secret_here
LINE_CHANNEL_ID=your_channel_id_here

# เลือกวิธีส่งข้อความ (เลือกอย่างใดอย่างหนึ่ง)
# วิธีที่ 1: ส่งเข้า LINE Group (แนะนำ)
LINE_GROUP_ID=your_line_group_id_here

# วิธีที่ 2: ส่งให้ Admin คนเดียว
# LINE_ADMIN_USER_ID=your_admin_line_user_id_here

# วิธีที่ 3: ส่งให้หลายคน (คั่นด้วย comma)
# LINE_NOTIFY_USERS=user_id_1,user_id_2,user_id_3
```

### 4. หา LINE Group ID

#### วิธีที่ 1: ใช้ Group ID Finder (แนะนำ)

```bash
# รันเซิร์ฟเวอร์หา Group ID
node utils/findGroupId.js
```

จากนั้น:
1. ไปที่ http://localhost:3201
2. ทำตามขั้นตอนในหน้าเว็บ
3. เพิ่ม Bot เข้า Group และส่งข้อความ
4. Copy Group ID จาก console

#### วิธีที่ 2: ใช้ Webhook แบบ manual

1. ตั้งค่า Webhook URL ใน LINE Developers Console: `https://your-domain.com/webhook/find-group`
2. เปิด "Use webhook" และปิด "Auto-reply messages"
3. เพิ่ม LINE Bot เข้า Group
4. ส่งข้อความใน Group
5. ดู server logs เพื่อหา Group ID

### 5. การทดสอบ

```bash
# ทดสอบการส่ง LINE notification
curl http://localhost:3200/api/test-line

# ดูการตั้งค่าปัจจุบัน
curl http://localhost:3200/api/line-config
```

## 🚀 การใช้งาน

เมื่อมีนักศึกษาส่งคำขอเอกสารใหม่ ระบบจะส่งข้อความแจ้งเตือนไปยัง LINE Group โดยอัตโนมัติ

### ตัวอย่างข้อความที่จะได้รับ:

```
🔔 คำขอเอกสารใหม่

━━━━━━━━━━━━━━━━━━━━━━
📄 รหัสคำขอ: #12345
👤 นักศึกษา: สมชาย ใจดี
🎓 รหัสนักศึกษา: 6401234567
📋 ประเภทเอกสาร: ใบแสดงผลการศึกษา
📦 วิธีการรับ: รับด้วยตนเอง
💰 ยอดรวม: 100.00 บาท
⏰ เวลา: 23 พ.ค. 2025 14:30
━━━━━━━━━━━━━━━━━━━━━━

🔗 กรุณาเข้าระบบเพื่อดำเนินการ
```

## 🔧 การแก้ไขปัญหา

### ปัญหาที่พบบ่อย:

1. **ส่งข้อความไม่ได้**
   - ตรวจสอบ Channel Access Token
   - ตรวจสอบ Group ID ถูกต้องหรือไม่
   - ตรวจสอบว่า Bot อยู่ใน Group หรือไม่

2. **หา Group ID ไม่ได้**
   - ตรวจสอบ Webhook URL ตั้งค่าถูกต้อง
   - ตรวจสอบว่าเปิด "Use webhook" แล้ว
   - ตรวจสอบว่าปิด "Auto-reply messages" แล้ว

3. **Webhook ไม่ทำงาน**
   - ใช้ ngrok หรือ cloudflare tunnel สำหรับ testing local
   - ตรวจสอบ SSL certificate สำหรับ production

### การ Debug:

```bash
# ดู logs ของระบบ
tail -f logs/app.log

# ทดสอบ LINE API ด้วย curl
curl -X POST https://api.line.me/v2/bot/message/push \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_CHANNEL_ACCESS_TOKEN' \
  -d '{
    "to": "GROUP_ID",
    "messages": [{
      "type": "text",
      "text": "Test message"
    }]
  }'
```

## 📚 เอกสารเพิ่มเติม

- [LINE Messaging API Documentation](https://developers.line.biz/en/docs/messaging-api/)
- [LINE Bot SDK for Node.js](https://github.com/line/line-bot-sdk-nodejs)

## ⚙️ ตัวเลือกการตั้งค่า

ระบบสนับสนุนการส่งข้อความ 3 แบบ (เลือกแบบใดแบบหนึ่ง):

1. **LINE Group** (แนะนำ): ส่งเข้า Group สำหรับทีมงาน
2. **Admin เดียว**: ส่งให้ผู้ดูแลระบบคนเดียว
3. **หลายคน**: ส่งให้ผู้ดูแลหลายคนพร้อมกัน

ระบบจะเลือกวิธีการส่งตามลำดับความสำคัญ: Group > หลายคน > คนเดียว
