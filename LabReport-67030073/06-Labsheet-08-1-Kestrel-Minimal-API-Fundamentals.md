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
   - **คำตอบ:** เบราว์เซอร์แสดงหน้า Error "This site can't be reached / ERR_CONNECTION_REFUSED" (ทดสอบด้วย `curl` ได้ `curl: (7) Failed to connect to localhost port 5080 ... Couldn't connect to server` ) เพราะ Kestrel ไม่ได้เป็น Service แยกที่รันค้างอยู่ในระบบแบบ Apache/IIS แต่ฝังตัวอยู่ในโปรเซสของโปรแกรมเราเอง เมื่อกด `Ctrl + C` โปรเซสจบการทำงาน Socket ที่ Listen พอร์ต 5080 ก็ถูกปิดไปด้วย จึงไม่มีใครรับ TCP Connection อีกต่อไป (OS ตอบกลับด้วย TCP RST)

### ผลการรันโปรแกรมบน Web Browser
เมื่อเข้าถึง URL `http://localhost:5135` หน้าเว็บจะแสดงข้อความว่า **"Welcome to IoT Edge Gateway by Natthapong Niampradit!"** ดังภาพ:

![IoT Edge Gateway Result](./Images/ใส่ชื่อไฟล์รูปภาพของคุณ.png)

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
   <img width="1919" height="642" alt="image" src="https://github.com/user-attachments/assets/0cb7bb95-22ee-4016-a277-1d9fda19ed9c" />


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
   <img width="1919" height="340" alt="image" src="https://github.com/user-attachments/assets/dbf89e57-3797-4177-8e69-db8a3313e734" />


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
| URL | `actionResult` ที่ได้ | Log บน Terminal |
| :--- | :--- | :--- |
| `/api/led/on` | `TURN ON 💡` | `[09:50:16] LED Control: on` |
| `/api/led/off` | `TURN OFF 🌑` | `[09:51:36] LED Control: off` |
| `/api/led/blink` | `TURN OFF 🌑` | `[09:52:00] LED Control: blink` |
<img width="1919" height="509" alt="image" src="https://github.com/user-attachments/assets/5808bf53-c963-4817-903a-01c0e2153dcf" />
<img width="1919" height="410" alt="image" src="https://github.com/user-attachments/assets/6cf0eecf-958c-40fa-82fa-7e6961cbc76f" />


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
1. เพิ่ม Endpoint ต่อไปนี้ลงใน `Program.cs`
   
    app.MapGet("/api/led/{state}", (string state) => {
    string action = state.ToLower() == "on" ? "TURN ON 💡" : "TURN OFF 🌑";
    
    Console.WriteLine($"[{DateTime.Now:HH:mm:ss}] LED Control: {state}");
    
    return Results.Ok(new { 
        device = "LED_D2", 
        requestedState = state, 
        actionResult = action,
        serverTime = DateTime.Now.ToString("HH:mm:ss")
    });
});
 <img width="1919" height="271" alt="image" src="https://github.com/user-attachments/assets/3b2beea9-f272-4cdd-b061-659b7a01ffbf" />


---

## คำถามท้ายการทดลอง (Review Questions)
1. ในสถาปัตยกรรมของ Kestrel ตัวแปร `builder` ทำหน้าที่อะไร และตัวแปร `app` ทำหน้าที่อะไร
   * **ตัวแปร `builder` (`WebApplicationBuilder`):** ทำหน้าที่เตรียมความพร้อมและตั้งค่าสภาพแวดล้อม (Configuration) ก่อนสร้างแอปพลิเคชัน เช่น การตั้งค่าพอร์ต, การลงทะเบียน Services ต่างๆ (Dependency Injection), และการตั้งค่า Logging เปรียบเสมือนการเตรียมวัสดุและเครื่องมือก่อนประกอบร่างเซิร์ฟเวอร์
   * **ตัวแปร `app` (`WebApplication`):** ทำหน้าที่จัดการแอปพลิเคชันที่สร้างเสร็จแล้ว โดยใช้สำหรับกำหนดเส้นทาง (Routing) เช่นคำสั่ง `app.MapGet(...)` เพื่อกำหนดว่า Path ใดจะทำงานด้วยฟังก์ชันอะไร รวมถึงใช้กำหนด HTTP Request Pipeline และใช้คำสั่ง `app.Run()` เพื่อสั่งให้ Kestrel เริ่มรับการเชื่อมต่อจากภายนอก
2. เปรียบเทียบความสะดวกระหว่างการสร้าง Web Server บน .NET Minimal API กับการรันผ่าน LAMP Stack (Apache + PHP) ว่ามีข้อดีข้อเสียต่างกันอย่างไรในมุมมองของงาน IoT Gateway
   * **.NET Minimal API:**
   * **ข้อดี:** กินทรัพยากรน้อย (Lightweight) เริ่มต้นทำงานรวดเร็ว และมี Web Server (Kestrel) ฝังมาในตัว (Self-contained) ทำให้ไม่ต้องติดตั้ง Service ซอฟต์แวร์แยก โค้ดมีความกระชับ เหมาะมากกับการนำไปรันบนอุปกรณ์ IoT Gateway หรือ Edge Device ที่มีทรัพยากรจำกัด
3. นักศึกษาคิดว่าการเพิ่ม `/api/` เข้าไปใน route นั้นมีประโยชน์อย่างไรบ้าง ถ้าไม่ใส่จะเกิดปัญหาอะไรบ้าง
   * **ประโยชน์:** ช่วยแบ่งแยกจุดประสงค์การใช้งานอย่างชัดเจน (Separation of Concerns) ทำให้ระบบทราบว่า Path นี้ถูกออกแบบมาเพื่อให้บริการข้อมูลดิบ (Data Service) แบบ Machine-to-Machine (เช่น คืนค่าเป็น JSON) ไม่ใช่เพื่อแสดงผลเป็นหน้าเว็บเพจ (HTML) ให้คนดู
   * **ปัญหาถ้าไม่ใส่:** อาจเกิดปัญหาเส้นทางทับซ้อนกัน (Route Collision) ในอนาคต เช่น หากเราใช้เส้นทาง `/student` ในการส่งค่า JSON กลับไป แล้ววันหนึ่งเราต้องการสร้างหน้าเว็บไซต์แสดงโปรไฟล์นักศึกษาที่ URL `/student` เส้นทางทั้งสองจะชนกัน การมี `/api/student` จึงช่วยแยกพื้นที่ของ API ออกจากพื้นที่ของ Web UI อย่างเป็นระบบตามหลัก RESTful API  
