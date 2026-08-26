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
