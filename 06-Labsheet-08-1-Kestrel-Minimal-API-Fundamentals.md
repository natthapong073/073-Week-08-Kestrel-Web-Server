# ใบงานการทดลองที่ 8.1 (Labsheet 8.1)
## พื้นฐาน Kestrel Web Server และ Minimal API

> **คำชี้แจง** ใบงานนี้มุ่งเน้นให้นักศึกษาได้สัมผัสประสบการณ์การสร้าง High-Performance Web Server ด้วยตนเองผ่านกระบวนการ *"ได้ทดลอง ได้เห็นผลลัพธ์ทีละว้าว"* โดยจะค่อยๆ สร้างโค้ดทีละสเต็ปสั้นๆ ห้ามคัดลอกโค้ดก้อนใหญ่ เพื่อสร้างความเข้าใจที่แท้จริง

---
## วัตถุประสงค์การทดลอง (Objectives)
1. สามารถติดตั้งและตรวจสอบสภาพแวดล้อมการทำงานของ .NET 8 / 9 SDK ได้
2. สามารถสร้างและรัน Kestrel Web Server ด้วยคำสั่ง .NET CLI ได้อย่างถูกต้อง
3. เข้าใจโครงสร้างของไฟล์โปรเจกต์ `.csproj` และไฟล์โค้ดหลัก `Program.cs`
4. สามารถสร้าง Minimal API Endpoint ที่ส่งคืนข้อมูลแบบสตริงธรรมดาและออบเจกต์ JSON อัตโนมัติได้
5. สามารถเขียน Endpoint รับค่าพารามิเตอร์ผ่าน URL Path ได้ด้วยตนเอง

---
## เครื่องมือและสิ่งที่ต้องเตรียม (Prerequisites)
- คอมพิวเตอร์ระบบปฏิบัติการ Windows / macOS / Linux
- ติดตั้ง **.NET SDK 8.0** หรือใหม่กว่า
- โปรแกรม **Visual Studio Code (VS Code)**
- เว็บเบราว์เซอร์ (Google Chrome, Microsoft Edge)

---

### ใบงานย่อยที่ 8-1 การสร้างเว็บไซต์อย่างง่ายด้วย Kestrel  
#### กิจกรรมที่ 1 การตรวจสอบความพร้อมของระบบและสร้างโปรเจกต์ 

1. เปิดโปรแกรม **Terminal** หรือ **PowerShell** ใน VS Code แล้วพิมพ์คำสั่งตรวจสอบเวอร์ชัน
   ```bash
   dotnet --version
   ```
   > *ผลลัพธ์ควรแสดงเป็นตัวเลข เช่น `8.0.xxx`,   `9.0.xxx`  หรือ 10.0.xxx*

2. สร้างโฟลเดอร์สำหรับการทดลองใบงานย่อยที่ 8.1 และสร้างโปรเจกต์ Web ว่างเปล่า (Empty Web)
   ```bash
   # สร้างโฟลเดอร์และเข้าไปด้านใน
   mkdir Lab8-1 && cd Lab8-1

   # สร้างโปรเจกต์ Web ว่างเปล่า
   dotnet new web -o .
   ```

3. ทดลองสั่งรันเว็บเซิร์ฟเวอร์
   ```bash
   dotnet run
   ```
   สังเกตข้อความบน Terminal จะพบข้อความแจ้งว่าเซิร์ฟเวอร์เริ่มทำงานแล้ว
   ```text
   info: Microsoft.Hosting.Lifetime[14]
         Now listening on: http://localhost:5xxx
   ```
โดย 5xxx จะเป็นเลข 4 หลักที่ระบบสร้างมาให้

4. เปิดเบราว์เซอร์แล้วพิมพ์ URL: `http://localhost:5xxx`  
   หรือกด ctrl + click ที่บรรทัด `Now listening on: http://localhost:5xxx`
   **สิ่งที่เห็น** คำว่า **"Hello World!"** ปรากฏบนหน้าจอ

> สังเกตว่าเราไม่ต้องติดตั้ง Apache, ไม่ต้องรัน Nginx, ไม่ต้องคอนฟิก PHP เลยแม้แต่น้อย โปรแกรม `.exe` ที่เราเขียนมี Kestrel Web Server ฝังตัวอยู่แล้วในกระบวนการ

---

#### [Checkpoint 1.1 ทดสอบความเข้าใจ]


1. ในหน้าจอ Terminal ขณะที่เซิร์ฟเวอร์กำลังรันอยู่ ให้กดปุ่ม `Ctrl + C` เพื่อหยุดโปรแกรม
2. กลับไปที่หน้าเบราว์เซอร์แล้วกดปุ่ม **Refresh (F5)** สังเกตว่าเกิดอะไรขึ้น และอธิบายสั้นๆ ว่าทำไมจึงเป็นเช่นนั้น
   - **คำตอบ:** หน้าเว็บแสดงข้อความ `ERR_CONNECTION_REFUSED` (ไม่สามารถเชื่อมต่อได้) เนื่องจาก Kestrel Web Server ที่คอยทำหน้าที่รับคำขอ (Request) ถูกปิดการทำงานลงด้วยคำสั่ง `Ctrl + C` ทำให้ไม่มีเซิร์ฟเวอร์คอยตอบสนองที่พอร์ตดังกล่าวอีกต่อไป

---

#### กิจกรรมที่ 2 โครงสร้างและเขียนโค้ด

1. เปิดไฟล์ `Program.cs` ขึ้นมาดู จะพบว่ามีโค้ดเพียงไม่กี่บรรทัด
   ```csharp
   var builder = WebApplication.CreateBuilder(args);
   var app = builder.Build();

   app.MapGet("/", () => "Hello World!");

   app.Run();
   ```

2. ทดลองเปลี่ยนข้อความทักทายเป็นชื่อของนักศึกษาเอง เช่น
   ```csharp
   app.MapGet("/", () => "Welcome to IoT Edge Gateway by [ใส่ชื่อ-นามสกุลนักศึกษา]!");
   ```

3. บันทึกไฟล์ สั่ง `dotnet run` อีกครั้ง แล้วกด Refresh บนเบราว์เซอร์เพื่อดูผลลัพธ์
<img width="472" height="119" alt="image" src="https://github.com/user-attachments/assets/a9d3a88b-75bb-4c82-8195-4c1cc547d1cf" />

---

#### กิจกรรมที่ 3 การแปลง C# Object เป็น JSON โดยอัตโนมัติ 

ในระบบ IoT การสื่อสารส่วนใหญ่ใช้รูปแบบข้อมูล **JSON (JavaScript Object Notation)** มาดูกันว่า .NET จัดการเรื่องนี้ให้เราง่ายแค่ไหน

1. เปิดไฟล์ `Program.cs` แล้วเพิ่มโค้ด Endpoint ใหม่เข้าไป **ก่อนบรรทัด `app.Run();`**
   ```csharp
   app.MapGet("/api/status", () => new {
       gateway = "ESP32-EdgeGateway",
       status = "Online",
       uptimeSeconds = Environment.TickCount64 / 1000,
       isHealthy = true
   });
   ```

2. สั่ง `dotnet run` จากนั้นเปิดเบราว์เซอร์ไปที่  
     `http://localhost:5000/api/status`

3. **สิ่งที่เห็น** หน้าจอเบราว์เซอร์จะแสดงผลเป็น JSON โครงสร้างสมบูรณ์
   ```json
   {
     "gateway": "ESP32-EdgeGateway",
     "status": "Online",
     "uptimeSeconds": 25,
     "isHealthy": true
   }
   ```
   <img width="699" height="160" alt="image" src="https://github.com/user-attachments/assets/24b97507-80f0-409a-9954-c16a8eeae01a" />


> เราไม่ได้สั่ง `json_encode()` หรือแปลงสตริงเลย เพียงแค่เราส่ง C# Anonymous Object ออกมา Kestrel จะทำการ Serialize เป็น JSON และแปะ Header `Content-Type: application/json` ให้อัตโนมัติ!

---

#### กิจกรรมที่ 4 การรับค่าผ่าน URL Path (Route Parameters)

เพิ่มฟังก์ชันการรับคำสั่งควบคุม เช่น สั่งเปิด-ปิดหลอดไฟ LED หรือรีเลย์ผ่าน URL

1. เพิ่ม Endpoint ต่อไปนี้ลงใน `Program.cs`
   ```csharp
   app.MapGet("/api/led/{state}", (string state) => {
       string action = state.ToLower() == "on" ? "TURN ON 💡" : "TURN OFF 🌑";
       return Results.Ok(new { 
           device = "LED_D2", 
           requestedState = state, 
           actionResult = action,
           serverTime = DateTime.Now.ToString("HH:mm:ss")
       });
   });
   ```

2. ทดสอบเปิด URL บนเบราว์เซอร์
   - ทดสอบพิมพ์ `http://localhost:5000/api/led/on`
   - ทดสอบพิมพ์ `http://localhost:5000/api/led/off`
   - สังเกตผลลัพธ์ JSON ที่ได้รับกลับมา


เพิ่มบรรทัด 
```csharp
	Console.WriteLine($"[{DateTime.Now:HH:mm:ss}] LED Control: {state}");
```
ถัดจากบรรทัด
```csharp
	    string action = state.ToLower() == "on" ? "TURN ON 💡" : "TURN OFF 🌑";   
```

เพื่อแสดงสถานะของ LED ที่ terminal

จากนั้นทดลองเปลี่ยนข้อความที่ URL bar แล้วสังเกตุและบันทึกผลที่ terminal
**ผลลัพธ์ที่คาดหวังบนเทอร์มินอล**
```
[xx:xx:xx] LED Control: on
[xx:xx:xx] LED Control: off
```
<img width="874" height="245" alt="image" src="https://github.com/user-attachments/assets/62105f32-56d3-47d5-9860-b7feb0e3d304" />


---

## ภารกิจท้าทาย (Micro-Challenge)

ให้นักศึกษาเขียน Endpoint ของตัวเองเพิ่มลงใน `Program.cs` ตามเงื่อนไขดังนี้

1. ตั้งชื่อ Path ว่า `/api/student`
2. เมื่อเปิดเข้าไปดู จะต้องคืนค่า JSON ที่มีข้อมูลดังต่อไปนี้
   - `studentId` = รหัสนักศึกษาของตนเอง (ชนิดสตริง)
   - `studentName`= ชื่อ-นามสกุลภาษาอังกฤษของตนเอง
   - `faculty`= คณะและสาขาวิชาที่กำลังศึกษา
   - `targetSensor`= ชื่อเซนเซอร์ที่ตนเองสนใจนำมาต่อกับ ESP32 ในวิชานี้ (เช่น "DHT22", "Potentiometer", "MQ-2")
   - `timestamp`= เวลาปัจจุบันของเซิร์ฟเวอร์ (`DateTime.Now.ToString(...)`)

 **หลักฐานการส่งงาน** บันทึกภาพหน้าจอเบราว์เซอร์ที่เปิดแสดงผล JSON จาก `/api/student` พร้อมโค้ดใน VS Code ลงในรายงานผลการทดลอง
<img width="1460" height="119" alt="image" src="https://github.com/user-attachments/assets/18abf6a4-90bb-46ca-a2f8-c1574b861123" />
<img width="635" height="698" alt="image" src="https://github.com/user-attachments/assets/534767da-3bef-4b9f-88d4-80b741d7fb29" />

---

## คำถามท้ายการทดลอง (Review Questions)
1. **ในสถาปัตยกรรมของ Kestrel ตัวแปร `builder` ทำหน้าที่อะไร และตัวแปร `app` ทำหน้าที่อะไร**
   - **`builder` (WebApplicationBuilder):** ทำหน้าที่กำหนดค่าเริ่มต้นและตั้งค่าระบบ (Configuration & Services Registration) เช่น การลงทะเบียน Service ต่างๆ, การตั้งค่าพอร์ต และเตรียมความพร้อมสภาพแวดล้อมก่อนที่เว็บแอปพลิเคชันจะเริ่มทำงาน
   - **`app` (WebApplication):** ทำหน้าที่สร้างและประกอบร่างแอปพลิเคชันหลังจากผ่านการ Config แล้ว รวมถึงการกำหนดเส้นทาง (Routing), การจัดการ Middleware และการแมป Endpoint ต่างๆ เช่น `app.MapGet()` เพื่อรอรับคำขอจากผู้ใช้งานหรืออุปกรณ์ IoT

2. **เปรียบเทียบความสะดวกระหว่างการสร้าง Web Server บน .NET Minimal API กับการรันผ่าน LAMP Stack (Apache + PHP) ว่ามีข้อดีข้อเสียต่างกันอย่างไรในมุมมองของงาน IoT Gateway**
   - **.NET Minimal API (Kestrel):**
     - *ข้อดี:* มีเว็บเซิร์ฟเวอร์ฝังตัวในตัว (Embedded Web Server) ไม่ต้องติดตั้งโปรแกรมแยกอย่าง Apache หรือ Nginx, โค้ดสั้นและจัดการแปลง C# Object เป็น JSON ให้อัตโนมัติ, มีประสิทธิภาพสูง (High-Performance) เหมาะกับการประมวลผลข้อมูลแบบ Real-time ของอุปกรณ์ IoT
     - *ข้อเสีย:* ต้องติดตั้ง .NET SDK บนโฮสต์ และอาจใช้ทรัพยากรหน่วยความจำสูงกว่าภาษาเชิงสคริปต์บางภาษาในยามไม่ได้ใช้งาน
   - **LAMP Stack (Apache + PHP):**
     - *ข้อดี:* ติดตั้งง่ายบนเซิร์ฟเวอร์ราคาประหยัดทั่วไป, มีโฮสติ้งรองรับแพร่หลาย
     - *ข้อเสีย:* ต้องตั้งค่าคอนฟิกตัวเว็บเซิร์ฟเวอร์ (Apache) ร่วมกับตัวแปลภาษา (PHP) แยกต่างหาก ซับซ้อนกว่าเมื่อต้องเชื่อมต่อจัดการข้อมูลสตรีมมิ่งแบบเรียลไทม์จากฮาร์ดแวร์หรือพอร์ตอนุกรม (Serial Port) ของอุปกรณ์ IoT

3. **นักศึกษาคิดว่าการเพิ่ม `/api/` เข้าไปใน route นั้นมีประโยชน์อย่างไรบ้าง ถ้าไม่ใส่จะเกิดปัญหาอะไรบ้าง**
   - *ประโยชน์:* ช่วยแบ่งแยกระหว่างเส้นทางที่ใช้สำหรับส่งคืนหน้าเว็บแอปพลิเคชัน (Frontend/UI) กับเส้นทางที่ใช้สำหรับรับส่งข้อมูลโปรแกรมหรือบริการข้อมูล (API Endpoint) ให้เป็นระเบียบชัดเจน
   - *หากไม่ใส่:* อาจทำให้เกิดปัญหาการชนกันของเส้นทาง (Route Collision) ระหว่างหน้าเว็บหลักกับข้อมูล JSON ที่ดึงผ่าน API ได้ง่าย อีกทั้งยังยากต่อการจัดการระบบความปลอดภัยหรือการกำหนดสิทธิ์ (API Versioning / Routing Structure) ในอนาคต
