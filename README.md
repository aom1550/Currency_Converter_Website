# 🌎 Currency Converter Application

แอพพลิเคชันแลกเปลี่ยนเงินตราที่ช่วยให้คุณคำนวณอัตราแลกเปลี่ยนได้อย่างรวดเร็วและแม่นยำ

## ✨ คุณสมบัติหลัก

- 💱 **อัตราแลกเปลี่ยนแบบเรียลไทม์**: ข้อมูลปัจจุบันจากแหล่งข้อมูลที่เชื่อถือได้ผ่าน ExchangeRate API
- 🔄 **แปลงได้หลายสกุลเงิน**: รองรับมากกว่า 150 สกุลเงินจากทั่วโลก
- 📱 **รองรับทุกอุปกรณ์**: Responsive design ใช้งานได้ทั้งบนคอมพิวเตอร์ แท็บเล็ต และสมาร์ทโฟน
- 🧮 **คำนวณหลายจำนวนเงิน**: สามารถเพิ่มรายการคำนวณได้ไม่จำกัด

## 🛠️ เทคโนโลยีที่ใช้

- **Vue.js**: JavaScript framework สำหรับสร้าง User Interface
- **CSS3**: ใช้ในการออกแบบ UI พร้อม animations และ responsive design
- **ExchangeRate API**: API สำหรับดึงข้อมูลอัตราแลกเปลี่ยนเงินตรา

## 🚀 การติดตั้ง

### ความต้องการเบื้องต้น
- Node.js (v14 หรือสูงกว่า)
- npm หรือ yarn

### ขั้นตอนการติดตั้ง
1. Clone repository นี้
```bash
git clone https://github.com/yourusername/currency-converter.git
cd currency-converter
```

2. ติดตั้ง dependencies
```bash
npm install
# หรือ
yarn install
```

3. เริ่มต้น development server
```bash
npm run serve
# หรือ
yarn serve
```

4. สร้าง production build
```bash
npm run build
# หรือ
yarn build
```

## 📝 การใช้งาน

1. **หน้าเริ่มต้น**:
   - กดปุ่ม "เริ่มต้นใช้งาน" เพื่อเริ่มใช้งานแอพ

2. **การแลกเปลี่ยนเงินตรา**:
   - เลือกสกุลเงินต้นทางที่ต้องการแลก (From)
   - เลือกสกุลเงินปลายทางที่ต้องการได้รับ (To)
   - ใส่จำนวนเงินที่ต้องการแลก
   - กดปุ่ม "คำนวณ" เพื่อดูผลลัพธ์

3. **ฟีเจอร์เพิ่มเติม**:
   - สามารถสลับสกุลเงินได้ด้วยปุ่ม "⇄"
   - เพิ่มรายการแลกเปลี่ยนได้ไม่จำกัดด้วยปุ่ม "+ เพิ่มจำนวนเงิน"
   - ลบรายการที่ไม่ต้องการด้วยปุ่ม "×"

## ⚠️ ข้อควรระวัง

- แอพนี้ใช้ API key จาก ExchangeRate API ซึ่งมีข้อจำกัดในการใช้งาน หากใช้งานเกินที่กำหนด อาจต้องสมัครแพ็คเกจเพิ่มเติม
- ข้อมูลอัตราแลกเปลี่ยนอาจมีความล่าช้าเล็กน้อยขึ้นอยู่กับการอัพเดทของ API

## 🔑 API Key Configuration

แอพนี้ใช้ ExchangeRate API โดยต้องกำหนด API key ในไฟล์ `.env`:

1. สร้างไฟล์ `.env` ที่ root ของโปรเจค
2. เพิ่มบรรทัดต่อไปนี้:
```
VUE_APP_EXCHANGE_API_KEY=your_api_key_here
```
3. แทนที่ `your_api_key_here` ด้วย API key ที่ได้จาก [ExchangeRate API](https://www.exchangerate-api.com/)

## 🤝 การมีส่วนร่วมในโปรเจค

หากต้องการมีส่วนร่วมในการพัฒนา:
1. Fork repository นี้
2. สร้าง branch ใหม่ (`git checkout -b feature/amazing-feature`)
3. Commit การเปลี่ยนแปลงของคุณ (`git commit -m 'Add some amazing feature'`)
4. Push ไปยัง branch ที่สร้าง (`git push origin feature/amazing-feature`)
5. เปิด Pull Request

---

⭐️ โปรเจคนี้ถูกสร้างขึ้นด้วย Vue.js และ ExchangeRate API ⭐️
