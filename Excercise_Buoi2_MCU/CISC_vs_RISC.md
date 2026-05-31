## 1. Giới thiệu khái niệm

### 1.1. CISC — Complex Instruction Set Computer

**CISC** là kiến trúc máy tính có **tập lệnh phức tạp**, được thiết kế từ những năm 1970 khi bộ nhớ còn đắt và compiler còn sơ khai. Triết lý thiết kế: **một lệnh có thể thực hiện nhiều thao tác cấp thấp** (load từ bộ nhớ, tính toán, ghi lại bộ nhớ) trong cùng một lệnh máy duy nhất.

**Đặc điểm chính:**
- Số lượng lệnh lớn (hàng trăm đến hàng nghìn lệnh).
- Độ dài lệnh thay đổi (variable-length): 1 byte đến 15 byte (x86).
- Hỗ trợ nhiều **chế độ địa chỉ** (addressing modes) phức tạp.
- Một lệnh có thể mất **nhiều chu kỳ clock** để hoàn thành.
- Dùng **microcode** bên trong CPU để thực thi lệnh phức tạp.

**Ví dụ điển hình:** Intel x86, x86-64, AMD64, Motorola 68000, IBM System/360.

**Ví dụ lệnh CISC (x86):**
```asm
ADD [EBX+ECX*4+8], EAX    ; cộng EAX vào ô nhớ tại địa chỉ EBX+ECX*4+8
```
Chỉ 1 lệnh nhưng làm: tính địa chỉ → đọc memory → cộng → ghi memory.

---

### 1.2. RISC — Reduced Instruction Set Computer

**RISC** là kiến trúc với **tập lệnh tinh giản**, ra đời cuối những năm 1980 (Berkeley RISC, MIPS, Stanford). Triết lý: **đơn giản hóa từng lệnh để CPU thực thi cực nhanh**, để compiler ghép nhiều lệnh đơn giản lại thay vì dùng lệnh phức tạp.

**Đặc điểm chính:**
- Số lượng lệnh ít (~50–200 lệnh).
- Độ dài lệnh **cố định** (thường 32-bit, hoặc 16/32-bit như Thumb).
- Ít chế độ địa chỉ, đơn giản.
- Đa số lệnh hoàn thành trong **1 chu kỳ clock**.
- Kiến trúc **load-store**: chỉ lệnh `LOAD/STORE` mới truy cập bộ nhớ; mọi tính toán làm trên thanh ghi.
- Số thanh ghi nhiều (32+ register).
- Dùng **pipeline** sâu, dễ song song hóa.

**Ví dụ điển hình:** ARM, RISC-V, MIPS, PowerPC, SPARC.

**Ví dụ lệnh RISC (ARM):** muốn làm tương đương ví dụ CISC trên cần 3 lệnh:
```asm
LDR R1, [R3, R4, LSL #2]    ; load
ADD R1, R1, R0              ; add
STR R1, [R3, R4, LSL #2]    ; store
```

---

## 2. Ưu điểm và nhược điểm

### 2.1. CISC

| Ưu điểm | Nhược điểm |
|---|---|
| Code size nhỏ → tiết kiệm bộ nhớ (quan trọng thời 1970–80) | Phần cứng phức tạp, khó thiết kế và verify |
| Compiler đơn giản hơn vì có sẵn lệnh "cao cấp" | Mỗi lệnh nhiều chu kỳ → khó pipeline tối ưu |
| Tương thích ngược tốt (legacy software chạy hàng chục năm) | Microcode tốn diện tích chip + tiêu thụ điện cao |
| Mật độ code cao → tốt cho cache instruction | Một số lệnh hiếm khi dùng nhưng vẫn tốn transistor |

### 2.2. RISC

| Ưu điểm | Nhược điểm |
|---|---|
| Phần cứng đơn giản → dễ thiết kế, ít bug | Code size lớn hơn (cần nhiều lệnh hơn để làm cùng việc) |
| Pipeline hiệu quả → IPC (instructions per cycle) cao | Compiler phức tạp hơn, chịu nhiều trách nhiệm tối ưu |
| Tiêu thụ điện thấp → lý tưởng cho mobile/embedded | Phụ thuộc compiler tốt; lập trình assembly tay vất vả hơn |
| Dễ mở rộng (thêm core, thêm SIMD…) | Cache instruction phải lớn hơn để bù code size |
| Decode lệnh nhanh do độ dài cố định | Ít tương thích ngược qua các thế hệ |

---

## 3. So sánh CISC và RISC theo các tiêu chí

### 3.1. Cấu trúc tập lệnh

| Tiêu chí | CISC | RISC |
|---|---|---|
| Số lượng lệnh | Lớn (hàng trăm – nghìn) | Nhỏ (~50–200) |
| Độ dài lệnh | Thay đổi (1–15 byte ở x86) | Cố định (thường 32-bit) |
| Chế độ địa chỉ | Nhiều (10+ modes) | Ít (3–5 modes) |
| Truy cập bộ nhớ | Hầu hết lệnh có thể truy cập memory trực tiếp | Chỉ LOAD/STORE |
| Format lệnh | Không đồng nhất | Đồng nhất, dễ decode |

### 3.2. Tốc độ xử lý

| Tiêu chí | CISC | RISC |
|---|---|---|
| CPI (Cycles Per Instruction) | Cao và biến thiên (1 → 20+) | Thấp và đều (~1) |
| Pipeline | Khó, do lệnh không đồng đều | Rất hiệu quả, sâu (5–15 tầng) |
| Tốc độ đơn nhân | Mỗi lệnh "làm được nhiều" nhưng chậm hơn | Mỗi lệnh ít việc nhưng siêu nhanh |
| Throughput tổng thể | Tốt khi cache hit, kém khi miss | Cao và ổn định |

> **Lưu ý hiện đại:** CPU x86 ngày nay (Intel, AMD) bên trong **dịch lệnh CISC sang μops giống RISC** rồi mới thực thi → ranh giới đã mờ đi rất nhiều.

### 3.3. Kích thước chương trình

| Tiêu chí | CISC | RISC |
|---|---|---|
| Code density | **Cao** — chương trình ngắn | Thấp hơn 20–40% |
| Ví dụ | x86 binary thường nhỏ hơn ARM 32-bit cùng chức năng | ARM Thumb-2 cải thiện tình hình bằng lệnh 16-bit |
| Tác động | Tốt cho I-cache, tệ cho decoder | Tệ cho memory footprint, tốt cho fetch |

### 3.4. Độ phức tạp phần cứng

| Tiêu chí | CISC | RISC |
|---|---|---|
| Số transistor cho control unit | Rất lớn (microcode ROM) | Nhỏ (hardwired control) |
| Diện tích chip | Lớn | Nhỏ → nhét được nhiều core |
| Tiêu thụ điện | Cao | Thấp |
| Tản nhiệt | Cần fan/heatsink lớn | Có thể fanless |
| Thời gian thiết kế | Dài, dễ bug | Ngắn hơn, dễ verify |

### 3.5. Ứng dụng thực tế

| Lĩnh vực | Kiến trúc thống trị | Ví dụ cụ thể |
|---|---|---|
| Desktop / Laptop / Server | **CISC (x86-64)** | Intel Core, Xeon; AMD Ryzen, EPYC |
| Smartphone / Tablet | **RISC (ARM)** | Apple A/M-series, Qualcomm Snapdragon, Samsung Exynos |
| Vi điều khiển nhúng | **RISC** | ARM Cortex-M (STM32, NXP), AVR (Arduino), PIC |
| IoT, wearable | **RISC** | ESP32 (Xtensa/RISC-V), Nordic nRF |
| Siêu máy tính HPC | Cả hai | Frontier (AMD x86) vs Fugaku (ARM A64FX) |
| Mac (từ 2020) | **Đã chuyển sang RISC** | Apple Silicon M1/M2/M3 (ARM) |
| Mã nguồn mở mới | **RISC-V** | SiFive, Alibaba T-Head, các IoT chip mới |

---

## 4. Quan điểm cá nhân: Kiến trúc nào phù hợp hơn cho hệ thống nhúng hiện nay?

### Câu trả lời: **RISC — đặc biệt là ARM Cortex-M và RISC-V — phù hợp hơn rất nhiều.**

### Lý do

**1. Tiêu thụ điện thấp — yếu tố sống còn của embedded**
Hệ thống nhúng thường chạy bằng pin (wearable, IoT sensor, drone) hoặc hoạt động 24/7 (gateway, controller). RISC có ít transistor, control unit hardwired, không có microcode → mỗi chu kỳ clock tiêu tốn ít năng lượng hơn. Một MCU ARM Cortex-M0+ có thể chạy ở mức μA, điều CISC không thể đạt được.

**2. Pipeline đơn giản, dự đoán được → real-time tốt**
Hệ nhúng thường yêu cầu **deterministic timing** (RTOS, motor control, ABS xe hơi). RISC có CPI gần như cố định, dễ tính WCET (Worst-Case Execution Time). CISC với lệnh biến thiên rất khó đảm bảo điều này.

**3. Diện tích chip nhỏ → giá thành thấp**
MCU embedded sản xuất hàng tỷ con/năm. Tiết kiệm vài mm² silicon nhân với sản lượng lớn = tiết kiệm hàng triệu USD. ARM Cortex-M0 chỉ ~12.000 cổng logic, trong khi một core x86 hiện đại cần hàng trăm triệu transistor.

**4. Hệ sinh thái embedded đã chín muồi quanh ARM**
- Toolchain miễn phí: GCC ARM, Clang, IAR, Keil.
- HAL / RTOS phong phú: FreeRTOS, Zephyr, Mbed.
- IDE: STM32CubeIDE, Segger Embedded Studio.
- IP core licensable, hàng trăm vendor SoC (ST, NXP, TI, Nordic, Renesas...).

**5. Sự trỗi dậy của RISC-V — tương lai mở của embedded**
RISC-V là kiến trúc **open-source, royalty-free**, đang được áp dụng nhanh chóng cho:
- IoT (ESP32-C3, C6 từ Espressif).
- Module bảo mật (Western Digital, NVIDIA dùng RISC-V làm controller).
- Học thuật và nghiên cứu (không cần license phí).

Với xu hướng "chip chủ quyền" (Trung Quốc, EU, Ấn Độ đầu tư mạnh), RISC-V càng quan trọng cho embedded.

**6. CISC vẫn cần thiết — nhưng không phải cho embedded**
x86 vẫn thống trị server và desktop nhờ:
- Hệ sinh thái phần mềm khổng lồ (Windows, hàng triệu ứng dụng).
- Hiệu năng đơn nhân vẫn rất cao.
- Tương thích ngược 40 năm.

Nhưng những ưu điểm này **không có giá trị** trong embedded — nơi thiết bị không cần chạy phần mềm legacy, không cần Windows, và yêu cầu hàng đầu là điện năng và giá.

### Kết luận

> **Trong bối cảnh embedded 2026, RISC (đặc biệt ARM Cortex-M cho ứng dụng thương mại và RISC-V cho ứng dụng mở/giáo dục) là lựa chọn tối ưu về cả kỹ thuật, kinh tế lẫn chiến lược dài hạn.**

Sự chuyển dịch của Apple từ x86 sang ARM (2020), của AWS sang Graviton (ARM), và sự bùng nổ RISC-V là minh chứng rõ ràng: **ngay cả ngoài embedded, RISC cũng đang thắng thế**. Riêng với embedded, cuộc tranh luận gần như đã ngã ngũ từ lâu — RISC là tiêu chuẩn de facto.

---

## Tài liệu tham khảo

1. Patterson, D. & Hennessy, J. — *Computer Organization and Design: The Hardware/Software Interface* (RISC-V Edition).
2. ARM Ltd. — *Cortex-M Series Technical Reference Manual*.
3. Intel — *Intel® 64 and IA-32 Architectures Software Developer's Manual*.
4. RISC-V International — *The RISC-V Instruction Set Manual*, Volume I & II.
5. Apple — "Apple Silicon: Architecture and Performance Whitepapers" (2020–2024).
