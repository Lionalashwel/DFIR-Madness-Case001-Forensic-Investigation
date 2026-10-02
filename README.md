# 🔍 DFIR Investigation Report: The Stolen Szechuan Sauce (Case 001)

![DFIR](https://img.shields.io/badge/Domain-Digital_Forensics_%26_Incident_Response-blue?style=for-the-badge&logo=shield)
![MITRE ATT&CK](https://img.shields.io/badge/Framework-MITRE_ATT%26CK-red?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)

---

## 📌 عن المشروع وإثبات القدرات الجنائية (Professional Overview)

يقدم هذا التقرير تحليلاً جنائياً رقمياً كاملاً ومفصلاً لإحدى قضايا الاختراق المعقدة (**DFIR Madness - Case 001**). تم إعداد هذا التقرير وتوثيقه بالكامل ليعكس **القدرات المتقدمة والمهارات العملية في التحقيق الجنائي الرقمي والاستجابة للحوادث السيبرانية (DFIR)**.

### 💡 المهارات المثبتة عبر هذا التحقيق:
* **تحليل حركة الشبكة (Network Forensics):** تتبع بروتوكولات RDP و HTTP/TLS، استخراج بصمات JA3/JA4، كشف عمليات التخمين (Brute-force)، وتحديد حجم واتجاه حركة تسريب البيانات (Data Exfiltration).
* **تحليل الذاكرة المؤقتة (Memory Forensics):** فحص الـ Memory Dumps باستخدام أدوات مثل `Volatility 2/3` لكشف حقن العمليات (Process Injection)، الاتصالات النشطة، واستخراج البرمجيات الخبيثة المخبأة في الذاكرة.
* **تحليل القرص والسجلات (Disk & Log Forensics):** فحص أدلة نظام الملفات NTFS ($MFT,$UsnJrnl)، استعادة الملفات المحذوفة، كشف التلاعب بالأوقات (Timestomping)، وتحليل سجلات أحداث ويندوز (EVTX) لربط الأحداث الزمنية.
* **بناء الخط الزمني الموحد (Timeline Reconstruction):** معالجة فروق التوقيت بين الأجهزة والشبكة (Time Offset Correlation) وإنشاء مخطط زمني دقيق لكل حركة قام بها المهاجم.
* **الهندسة العكسية وتتبع التهديدات (Malware & Threat Intelligence):** تصنيف البرمجية الخبيثة (`Meterpreter/Crypter`)، تحديد بنية التحكم والسيطرة (C2)، مطابقة الهجوم مع إطار عمل **MITRE ATT&CK**، وضبط مؤشرات الاختراق (IOCs).

---

## 📑 ملخص القضية (Executive Summary)

* **الحدث:** تعرضت المؤسسة لاختراق خارجي أدى إلى سرقة الوصفة السرية لـ "Szechuan Sauce" وملفات حساسة أخرى وعرضها للبيع على الشبكة المظلمة.
* **ناقل الدخول الأولي:** هجوم تخمين كلمة مرور مكثّف (RDP Brute-Force) ضد المنفذ `3389` المكشوف على خادم النطاق `CITADEL-DC01` (10.42.85.10) انطلاقاً من عنوان المهاجم `194.61.24.102`.
* **البرمجية الخبيثة المستخدمة:** حمولة `Meterpreter` باسم `coreupdater.exe` تم تنزيلها عبر HTTP وحقنها داخل العملية الشرعية `spoolsv.exe`.
* **التحرك الجانبي (Lateral Movement):** الانتقال من خادم النطاق إلى محطة العمل `DESKTOP-SDN1RPT` (10.42.85.115) عبر جلسة RDP داخلية بنفس بيانات الاعتماد المخترقة.
* **الاستمرارية (Persistence):** إنشاء خدمة ويندوز تلقائية (`7045`) ومفاتيح تشغيل تلقائي في السجل (Registry Run Keys) على كلا الجهازين.
* **تسريب البيانات:** ضغط الملفات الحساسة في أرشيفات (`secret.zip`, `loot.zip`) وسحبها عبر قناة C2 مشفرة (`203.78.103.109:443`).

---

## 🛠️ الأدوات والمنهجية المستخدمة (Tools & Methodology)

| المجال | الأدوات المستخدمة |
| :--- | :--- |
| **Network Analysis** | Wireshark, NetworkMiner, Zeek |
| **Memory Analysis** | Volatility 2, Volatility 3 |
| **Disk & File System** | Autopsy, FTK Imager, Magnet AXIOM, Eric Zimmerman Tools (`MFTECmd`) |
| **Log Analysis** | EvtxECmd, Timeline Explorer, Windows Event Viewer |
| **Registry & Persistence** | RegRipper, Registry Explorer, Autorunsc |
| **Threat Intel & Hashing** | VirusTotal, AbuseIPDB, CyberChef |

---

## 🎯 مؤشرات الاختراق الرئيسية (IOCs)

* **Malignant IPs:**
  * `194.61.24.102` (Attacker / Initial Access / Staging)
  * `203.78.103.109` (C2 Server - Port 443)
* **File Artifacts:**
  * File Name: `coreupdater.exe` (Path: `C:\Windows\System32\coreupdater.exe`)
  * SHA-256: `10f3b92002bb98467334161cf85d0b1730851f9256f83c27db125e9a0c1cfda6`
  * MD5: `eed41b4500e473f97c50c7385ef5e374`
* **Compromised Accounts:** `CITADEL\Administrator`
* **Injected Process:** `spoolsv.exe`

---

## 🗺️ مطابقة الهجوم مع إطار MITRE ATT&CK

* **Reconnaissance:** Network Service Discovery (`T1046`)
* **Initial Access:** Password Brute Force (`T1110.001`), Valid Accounts (`T1078`)
* **Execution & Transport:** Ingress Tool Transfer (`T1105`)
* **Persistence:** Windows Service (`T1543.003`), Registry Run Keys (`T1547.001`)
* **Defense Evasion:** Process Injection (`T1055`), Indicator Removal / File Deletion (`T1070.004`), Timestomping (`T1070.006`)
* **Lateral Movement:** Remote Desktop Protocol (`T1021.001`)
* **Exfiltration:** Exfiltration Over C2 Channel (`T1041`), Archive Collected Data (`T1560`)

---

## 📁 محتويات المستودع (Repository Structure)

```text
├── Case001_Final_Report.pdf     # التقرير الجنائي المكتمل والمدعم بالأدلة
├── README.md                    # توثيق المشروع ومهارات التحقيق
└── Artifacts/                   # (اختياري) السجلات والـ IOCs وقواعد IDS المقترحة
