\# Vehicle Telemetry and Fault Monitoring System

Bu proje, araç içi ECU (Electronic Control Unit) haberleşmesi, CAN veri yolu sinyal spesifikasyonları, araç durum modellemesi ve yazılımsal doğrulama (validation) süreçlerini içeren bir otomotiv telemetry ve arıza izleme sistemidir.

\---

\#\# 📌 Proje Gelişim Süreci (İlk 3 Gün)

\#\#\# 🔹 1\. Gün: Çalışma Ortamı ve Git Yapılandırması  
\- Python sanal ortamı (\`.venv\`) ve \`pip\` bağımlılık yönetimi yapılandırıldı.  
\- Sürüm kontrolü için \`.gitignore\` oluşturuldu ve güncellendi.  
\- Test altyapısı olarak \`pytest\` kütüphanesi ortama entegre edildi ve \`requirements.txt\` dosyasına eklendi.

\#\#\# 🔹 2\. Gün: Python Temelleri ve Araç Durum Modeli  
\- Modüler Python paket yapısı (\`src/vehicle\_simulator\` ve \`tests\`) kurgulandı.  
\- Araç sinyalleri için veri tipleri (\`float\`, \`int\`, \`bool\`), \`dataclass\` mimarisi ve \`post\_init\` doğrulama mantığı çalışıldı.  
\- Sınır değer kontrolleri, alanlar arası tutarlılık kuralları (kontak kapalıyken motor çalışmama kuralı vb.) ve \`math.isfinite\` ile özel sayısal değer (\`NaN\`, \`inf\`) denetimleri incelendi.  
\- Unit Test (AAA yaklaşımı, Boundary Value Testing) ve hata yönetimi (\`Exception Handling\`) prensipleri belirlendi.

\#\#\# 🔹 3\. Gün: Araç ve ECU Mimarisi Spesifikasyonları  
\- Powertrain, ABS, BCM ve Instrument Cluster ECU'larının görev dağılımları ile sensör/aktüatör ilişkileri tanımlandı.  
\- 8 adet temel eğitim sinyali için kaynak ECU, veri tipi, geçerli aralıklar, periyot (Cycle Time), \`INVALID\` ve \`TIMEOUT\` davranışları tanımlandı.  
\- Test edilebilir sistem kabul kriterlerini oluşturan sinyal gereksinimleri (\`REQ-SIG-001\` \- \`REQ-SIG-007\`) kaleme alındı.  
\- Detaylı mimari dokümantasyon \`docs/signals.md\` dosyasına işlendi.

\---

\#\# 🚘 Sinyal Spesifikasyon Tablosu

| Sinyal Adı | Kaynak ECU | Veri Tipi | Birim | Geçerli Aralık | Periyot | Timeout Süresi |  
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |  
| \`VehicleSpeed\` | ABS | \`float\` | km/h | 0.0 / 250.0 | 10 ms | 50 ms |  
| \`EngineSpeed\` | Powertrain | \`float\` | RPM | 0.0 / 8000.0 | 10 ms | 50 ms |  
| \`EngineCoolantTemp\` | Powertrain | \`float\` | °C | \-40.0 / 150.0 | 500 ms | 2500 ms |  
| \`AcceleratorPedalPos\` | Powertrain | \`float\` | % | 0.0 / 100.0 | 20 ms | 100 ms |  
| \`BrakePedalStatus\` | ABS | \`bool\` | \- | False / True | 20 ms | 100 ms |  
| \`IgnitionStatus\` | BCM | \`bool\` | \- | False / True | 100 ms | 500 ms |  
| \`FuelLevel\` | BCM | \`float\` | % | 0.0 / 100.0 | 1000 ms | 5000 ms |  
| \`OdometerValue\` | Cluster | \`float\` | km | 0.0 / 999999.9 | 1000 ms | 5000 ms |

\---

\#\# 📂 Proje Klasör Yapısı

\`\`\`text  
Vehicle-Telemetry-and-Fault-Monitoring-System/  
├── docs/  
│   └── signals.md           \# ECU Mimarisi, Sinyal Spesifikasyonları ve Gereksinimler  
├── vehicle-simulator/  
│   ├── src/  
│   │   └── vehicle\_simulator/  
│   │       ├── \_\_init\_\_.py      \# Paket tanım dosyası  
│   │       ├── constants.py     \# Sinyal sınırları ve sabitler (UPPER\_SNAKE\_CASE)  
│   │       ├── exceptions.py    \# Özel doğrulama ve timeout istisnaları  
│   │       ├── validation.py    \# Sinyal ve veri doğrulama fonksiyonları  
│   │       └── vehicle\_state.py \# Araç durum modeli (Dataclass)  
│   ├── tests/  
│   │   └── test\_vehicle\_state.py \# Birim testler (Pytest)  
│   └── requirements.txt     \# Proje bağımlılıkları (pytest)  
├── .gitignore               \# Git takibine alınmayacak dosyalar (.venv vb.)  
└── README.md                \# Proje ana dokümantasyonu  
# 🛠️ Daily Progress Log

## 4. Gün: Araç State Machine, Normal Sürüş Simülasyonu ve CAN Bus Mantığı

### 📌 Konu Özeti & Öğrenilenler
- **State Machine Architecture:** Araç durumları (`OFF`, `IGNITION_ON`, `ENGINE_RUNNING`, `DRIVING`, `SHUTDOWN`) ve durumlar arası geçiş kuralları (`Guard Conditions`) kurgulandı.
- **Deterministik Simülasyon:** Rastgele veri üretimi yerine `normal_drive.json` senaryo dosyası üzerinden adım adım çalışan sürüş simülatörü geliştirildi.
- **Birim Testler (`pytest`):** RPM sıfırlanması, gaz-hız/RPM tutarlılığı ve deterministik çalışma kabul kriterleri doğrulandı.

---

### 🟢 JSON ve YAML Farkları
| Özellik | JSON (JavaScript Object Notation) | YAML (YAML Ain't Markup Language) |
| :--- | :--- | :--- |
| **Sözdizimi** | Süslü `{}` ve köşeli `[]` parantezler | Girintiler (Indentation / Tab-Space) |
| **Okunabilirlik** | Makine/API odaklı | İnsan odaklı, daha temiz |
| **Yorum Satırı** | Desteklemez | Destekler (`#`) |
| **Kullanım Alanı** | REST API'ler, Telemetri verisi | Konfigürasyon, CI/CD, Simülasyon senaryoları |

---

### 🚗 CAN Bus Haberleşme Hızı ve Hat Uzunluğu İlişkisi
- **CAN Hızı Neye Göre Değişir?**
  - **High-Speed CAN (ISO 11898-2):** 500 kbps – 1 Mbps (Motor, ABS, ESP gibi kritik ECU'larda).
  - **Low-Speed CAN (ISO 11898-3):** 125 kbps'e kadar (Karakoser, klima gibi konfor sistemlerinde).
  - **CAN FD:** 5 Mbps+ (Büyük veri paketleri için).

- **Hat Uzunluğunun Hıza Etkisi:**
  - Baud Rate (Hız) arttıkça sinyalin kablo üzerindeki **gecikme süresi (Propagation Delay)** nedeniyle maksimum **Hat Uzunluğu zorunlu olarak düşer**.
  
| Baud Rate (Hız) | Maksimum Bus Uzunluğu |
| :--- | :--- |
| **1 Mbit/s** | ~25 - 40 metre |
| **500 kbit/s** | ~100 metre |
| **250 kbit/s** | ~250 metre |
| **125 kbit/s** | ~500 metre |

---

## 5. Gün: Sanal ECU'lar, Modüler Mimariler ve Periyodik CAN Mesaj Üretimi

### 📌 1. Öğrenim Hedefleri ve Mimari Kavramlar
- **Modüler Tasarım ve Single Responsibility Principle:** Araç sistemleri tek parça kod yerine sorumluluk alanlarına göre ayrılmış sanal ECU'lar (Powertrain, Body, Diagnostic) şeklinde tasarlandı.
- **Periyodik Mesaj Döngüsü (Task Period & Message Cycle):** CAN ağındaki veri trafiğini yönetmek için mesaj türlerine göre zaman aralıkları (100 ms, 500 ms, 1000 ms) tanımlandı.
- **Zaman Damgası ve Canlılık Sayacı (Timestamp & Alive Counter):** Paketlere `time.time()` eklenerek gecikmeler izlendi; `0-15` arası döner sayaç ile verinin donmadığı doğrulandı.

---

### 📡 2. CAN Standard ID vs Extended ID
| Özellik | Standard CAN (CAN 2.0A) | Extended CAN (CAN 2.0B / SAE J1939) |
| :--- | :--- | :--- |
| **ID Uzunluğu** | **11-bit** | **29-bit** |
| **Kombinasyon Sayısı** | $2^{11} = 2.048$ farklı mesaj ID'si | $2^{29} \approx 536.870.912$ farklı mesaj ID'si |
| **Kullanım Alanı** | Gerçek zamanlı motor, fren, şanzıman haberleşmesi | OBD-II teşhis (Diagnostic) mesajları, ağır vasıta sistemleri |
| **Örnek Mesaj ID** | `0x100`, `0x123` | `0x18DAF110` |

---

### 👤 3. Özel CAN Mesaj ve Sinyal Tanımı
- **Mesaj Adı:** `MERT_INFO`
- **CAN ID:** `0x123` *(Standard 11-bit ID)*
- **Sinyaller:**
  1. `Age`: Geliştiricinin yaş verisi *(8-bit Unsigned Integer)*.
  2. `Developer`: Geliştirici metin bilgisi ("Mert").
  3. `AliveCounter`: Her gönderimde 0-15 arası artan periyodik sayaç.

---

### 🏗️ 4. Sanal ECU Uygulama Mimarisi ve Periyot Tablosu

```text
 Zaman (ms) ───►  0ms      100ms     200ms     ...     500ms     ...     1000ms
                  │        │         │                 │                 │
PowertrainStatus  ├───────►├────────►├────────────────►├────────────────►│ (100 ms)
PedalStatus       ├───────►├────────►├────────────────►├────────────────►│ (100 ms)
BodyStatus        ├───────────────────────────────────►│                 │ (500 ms)
DiagnosticStatus  ├─────────────────────────────────────────────────────►│ (1000 ms)