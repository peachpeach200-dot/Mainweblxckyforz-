# Bio Link + Appwrite Admin CMS

โปรเจกต์ Next.js สำหรับทำ Link-in-bio พร้อมหลังบ้าน Admin โดยใช้ **Appwrite Cloud** แทน Supabase

## ฟีเจอร์

- Public profile page
- Avatar image + animated GIF
- Avatar effects: glow / neon / spin / shadow
- Cover image
- Font selector
- Dynamic links
- Custom icon/thumbnail ต่อ link
- Drag & drop ordering
- Appwrite Auth email/password
- Appwrite Database
- Appwrite Storage
- Appwrite Realtime refresh เมื่อ profile/links เปลี่ยน
- Responsive mobile/desktop

## 1. สร้าง Appwrite Project

1. เข้า Appwrite Cloud แล้วสร้าง Project เช่น `Peach Bio Link`
2. เพิ่ม Web Platform สำหรับเว็บของคุณ
3. ตอนพัฒนาในเครื่องให้เพิ่ม hostname เป็น `localhost`
4. สร้าง Admin user ที่ **Auth > Users > Create user** และจด User ID ไว้

> เว็บนี้ใช้ Email + Password login ของ Appwrite โดยไม่เก็บ password เอง

## 2. สร้าง Database

สร้าง Database เช่น `bio_link`

### Collection: `profile`

สร้าง Collection ID ตามที่ต้องการ แล้วสร้าง attributes:

| Attribute | Type | Required |
|---|---|---|
| display_name | String | Yes |
| bio | String | No |
| avatar_url | String | No |
| cover_url | String | No |
| avatar_effect | String | No |
| font_family | String | No |

ให้สร้าง Document ID เป็น `profile` เมื่อมีการสร้างครั้งแรกจากหน้า Admin ระบบจะสร้างให้อัตโนมัติถ้ายังไม่มี

### Collection: `links`

สร้าง attributes:

| Attribute | Type | Required |
|---|---|---|
| title | String | Yes |
| url | String | Yes |
| icon_url | String | No |
| position_order | Integer | Yes |
| is_active | Boolean | Yes |

## 3. ตั้ง Permission

แนะนำให้ใช้ **Admin Team** เพื่อไม่เปิดสิทธิ์แก้ไขให้ผู้ใช้ทุกคน

1. สร้าง Team เช่น `Bio Link Admin`
2. เพิ่ม Admin user เข้า Team
3. ใส่ Team ID ใน `.env.local` เป็น `NEXT_PUBLIC_APPWRITE_ADMIN_TEAM_ID`
4. ตั้ง Collection permissions ให้ Public อ่านได้ และ Team ของ Admin มีสิทธิ์สร้าง/แก้ไข/ลบ
5. ตั้ง Document permissions ให้ Public อ่านได้ และ Admin Team แก้ไข/ลบได้

ถ้าไม่ใช้ Team ให้ใส่ Admin User ID ใน `NEXT_PUBLIC_APPWRITE_ADMIN_USER_ID` และตั้งสิทธิ์ให้ User คนนั้นแทน

> การตรวจ Admin ในหน้าเว็บเป็นด่านเพิ่ม แต่ **Appwrite Permissions ต้องเป็นตัวบังคับความปลอดภัยจริง** อย่าเปิด write เป็น `Any` ใน production

## 4. สร้าง Storage Bucket

สร้าง Bucket เช่น `bio-assets`

รองรับอย่างน้อย:

- image/jpeg
- image/png
- image/webp
- image/gif
- image/avif

ตั้ง permission ให้ Public อ่านไฟล์ และ Admin Team สร้าง/แก้ไข/ลบไฟล์

GIF ใช้ `<img>` จึงยังเล่น animation ได้ตามปกติ

## 5. Environment Variables

คัดลอก:

`.env.example` -> `.env.local`

แล้วใส่ค่า Appwrite Project/Database/Collection/Bucket ของตัวเอง:

```env
NEXT_PUBLIC_APPWRITE_ENDPOINT=https://cloud.appwrite.io/v1
NEXT_PUBLIC_APPWRITE_PROJECT_ID=YOUR_PROJECT_ID
NEXT_PUBLIC_APPWRITE_DATABASE_ID=YOUR_DATABASE_ID
NEXT_PUBLIC_APPWRITE_PROFILE_COLLECTION_ID=YOUR_PROFILE_COLLECTION_ID
NEXT_PUBLIC_APPWRITE_LINKS_COLLECTION_ID=YOUR_LINKS_COLLECTION_ID
NEXT_PUBLIC_APPWRITE_BUCKET_ID=YOUR_BUCKET_ID
NEXT_PUBLIC_APPWRITE_ADMIN_USER_ID=YOUR_ADMIN_USER_ID
NEXT_PUBLIC_APPWRITE_ADMIN_TEAM_ID=YOUR_ADMIN_TEAM_ID
```

ไม่ต้องใส่ Appwrite API key หรือ secret key ใน `NEXT_PUBLIC_*` และโปรเจกต์นี้ไม่ต้องใช้ server secret สำหรับการทำงานปกติของหน้าเว็บ

## 6. ติดตั้งและรัน

```bash
npm install
npm run dev
```

เปิด:

- Public: http://localhost:3000
- Admin: http://localhost:3000/login

## 7. Flow การทำงาน

Browser
  |
  +--> Appwrite Account (Email/Password)
  |
  +--> Appwrite Database: profile / links
  |
  +--> Appwrite Storage: bio-assets
  |
  +--> Appwrite Realtime

หน้า Public อ่าน profile + links ที่ active และ subscribe Realtime เพื่อโหลดข้อมูลใหม่เมื่อ Admin บันทึก

หน้า Admin ตรวจ session + Admin User ID ก่อนแก้ข้อมูล และ Appwrite Permissions เป็นชั้นความปลอดภัยหลัก

## 8. Deploy

สามารถ deploy บน Vercel ได้ โดยเพิ่ม Environment Variables ชุดเดียวกับ `.env.local`

อย่าลืมเพิ่มโดเมน production ใน Appwrite Project > Platforms > Web

## หมายเหตุ

- Standard upload เหมาะกับไฟล์รูปทั่วไป; ถ้าจะรองรับไฟล์ขนาดใหญ่มาก ควรเพิ่ม chunk/resumable upload
- URL ของรูปจาก Storage ถูกสร้างด้วย `getFileView()` และใช้ตรงกับ `<img>`/background image
- การเปลี่ยนลำดับ Link จะถูกบันทึกลง `position_order`
