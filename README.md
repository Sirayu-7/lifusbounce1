# lifusbounce

`lifusbounce` เป็นโปรเจกต์วิจัยและ overlay แบบ external บน Windows สำหรับ Rainbow Six Siege ที่พัฒนาด้วย C++20 โดยมี Direct3D 11 overlay, ตัวอ่านเซสชันเกม, การแสดงข้อมูลผู้เล่น, ระบบช่วยเล็ง, ข้อมูลโอเปอเรเตอร์และอาวุธ, การวาดโครงกระดูก, ระบบวินิจฉัย และ kernel driver คู่กัน

โปรเจกต์นี้เป็น fork ของ [lifusdev/lifusbounce](https://github.com/lifusdev/lifusbounce)

> **คำเตือน:** โปรเจกต์นี้มีโค้ดโหมดเคอร์เนลและพฤติกรรมที่เกี่ยวข้องกับการหลบเลี่ยง DSE สามารถปรับเปลี่ยนสถานะความปลอดภัยของระบบ โหลด unsigned driver และกระตุ้นระบบป้องกันของ Windows หรือ anti-cheat กรุณาใช้งานเฉพาะในระบบแยกเพื่อการวิจัย หรือบนเครื่องที่คุณได้รับอนุญาตให้ทดสอบ ห้ามนำไปใช้เพื่อโกงในเกมออนไลน์หรือฝ่าฝืนข้อกำหนดการใช้งานของเกม

## คุณสมบัติ

- Overlay ด้วย Direct3D 11 และ DirectComposition แบบ layered
- แผงตั้งค่าด้วย Dear ImGui
- แสดงชื่อผู้เล่น โอเปอเรเตอร์ พลังชีวิต อาวุธ และสถานะผู้กู้ระเบิด
- กล่อง 2D และวงกลมหัวพร้อมโครงกระดูก
- ตั้งค่าระบบช่วยเล็งและการแสดง FOV ได้
- รองรับการตรวจทีมและคีย์บอร์ดกำหนดเอง
- มีรูปภาพ badge ของโอเปอเรเตอร์และฟอนต์ฝังในรีซอร์ส
- รองรับการอ่านหน่วยความจำกระบวนการ รายชื่อกระบวนการ Regions และ Threads จาก kernel
- ใช้ Zydis สำหรับถอดรหัสและจำลองคำสั่ง
- มีระบบบันทึกวินิจฉัย และจัดเก็บ/ล้าง artifacts อัตโนมัติ
- มี kernel driver และ user-mode loader ที่ทำงานร่วมกัน

## ความต้องการของระบบ

- Windows 10 หรือใหม่กว่า เวอร์ชัน x64
- Visual Studio พร้อมงาน C++ desktop development
- MSVC toolset `v145`
- Windows SDK 10
- Windows Driver Kit (WDK) สำหรับคอมไพล์ kernel driver
- สิทธิ์ Administrator เมื่อใช้งานฟังก์ชันที่เกี่ยวข้อง
- ต้องปิด Hypervisor-protected Code Integrity (HVCI) เพื่อโหลด unsigned kernel driver

## โครงสร้างโปรเจกต์

```text
src/
├── core/       การโหลด driver, หน่วยความจำ, วินิจฉัย, disassembly และ emulation
├── sdk/        การอ่านกระบวนการ/เซสชันและตรรกะของฟีเจอร์เกม
└── ui/         Direct3D, ImGui, overlay, เมนู, รีซอร์ส และการวาดภาพ

driver/
├── src/        โค้ดตัว kernel driver
├── include/    อินเทอร์เฟซและประเภทข้อมูลร่วมของ driver
├── loader/     Loader สำหรับโหลดและถอดถอน driver
└── resources/  ไฟล์ INF และไฟล์รีซอร์ส

resources/      รีซอร์สแอปพลิเคชัน ฟอนต์ และรูปโอเปอเรเตอร์
third_party/    ImGui, miniz, Zydis และ Zycore
```

## การคอมไพล์

เปิด `lifus.slnx` ใน Visual Studio จากนั้น build โปรเจกต์ `lifus` ด้วยแพลตฟอร์ม **x64 / Release**

ไฟล์แอปพลิเคชันหลักจะถูกสร้างที่:

```text
out\release\lifus.exe
```

สำหรับ kernel driver และ loader ให้รัน `driver\build.cmd` จาก Command Prompt ที่เปิดด้วยสิทธิ์ผู้ดูแลระบบ โดยติดตั้ง Visual Studio และ WDK ให้พร้อมใช้งาน:

```bat
cd /d driver
build.cmd
```

ผลลัพธ์จากการ build อยู่ที่:

```text
bin\driver\lifus.sys
bin\loader\loader.exe
```

## การควบคุม

| ปุ่ม | การทำงาน |
|---|---|
| Insert | เปิด/ปิดเมนู overlay |
| ปุ่มเมาส์ขวา | เปิดใช้งานระบบช่วยเล็งเมื่อเปิดฟีเจอร์นั้นไว้ |
| End | ปิดแอปพลิเคชัน |

สามารถปรับการแสดงผลและฟีเจอร์ต่าง ๆ ได้จากเมนูภายในแอป

## ระบบวินิจฉัย

แอปพลิเคชันเริ่มต้น diagnostic subsystem ขณะเปิดใช้งาน สามารถเปิดโฟลเดอร์ log และ artifacts ได้จากแท็บ Debug ในแอป

## ไลบรารีภายนอก

- [Dear ImGui](https://github.com/ocornut/imgui)
- [Zydis](https://github.com/zyantific/zydis)
- [Zycore](https://github.com/zyantific/zycore-c)
- [miniz](https://github.com/richgel999/miniz)

ข้อมูลลิขสิทธิ์และใบอนุญาตของไลบรารีภายนอกอยู่ใน `third_party/notices.txt` และในไดเรกทอรีของแต่ละ dependency

## ใบอนุญาต

โปรเจกต์นี้ยังไม่มีไฟล์ license สิทธิ์จึงยังเป็นของผู้พัฒนาแต่ละคนตามที่เกี่ยวข้อง เว้นแต่จะมีการเพิ่มไฟล์ใบอนุญาตอย่างชัดเจนในภายหลัง
