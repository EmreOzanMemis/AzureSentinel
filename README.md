# Azure Sentinel: SIEM & SOAR Çözümü için Kurulum ve Yapılandırma Rehberi

## 📖 Genel Bakış

**Azure Sentinel**, Microsoft'un bulut tabanlı bir **SIEM (Security Information and Event Management)** ve **SOAR (Security Orchestration, Automation, and Response)** çözümüdür. Sentinel, büyük ölçekli güvenlik verilerini toplayarak tehditleri tespit eder, analiz eder ve bu tehditlere otomatik yanıt verir. Bu rehber, Azure Sentinel'in kurulumu, yapılandırılması ve kullanımıyla ilgili detaylı bilgileri sağlar.

## 🚀 Başlarken

### 1. **Azure Sentinel Kurulumu**

1. **Azure Portal**'a giriş yapın ve **Azure Sentinel** hizmetini bulun.
2. Sentinel'i etkinleştirmek için bir **Log Analytics Workspace** seçin ya da oluşturun. 
   - **Log Analytics Workspace**, tüm log verilerinin toplandığı merkezi platformdur. Sentinel, bu veriler üzerinden analizler yapar ve tehditleri tespit eder.
3. Sentinel’i yapılandırmak için veri kaynaklarını bağlamaya başlayın.

### 2. **Log Analytics Workspace Önemi**

- **Log Analytics Workspace**, Azure Sentinel için güvenlik olaylarını topladığı, sakladığı ve analiz ettiği bir veri havuzudur. Veriler, çeşitli kaynaklardan bu çalışma alanında toplanır ve analiz edilir.
- **KQL (Kusto Query Language)** kullanılarak bu veriler üzerinde derinlemesine sorgulamalar yapılır ve tehditler tespit edilir.
- **Veri Saklama Süresi** ve **Maliyet Yönetimi** açısından, verilerin toplandığı ve ne kadar süreyle saklandığı Log Analytics Workspace üzerinden kontrol edilir.

## 🔌 Veri Kaynaklarını Bağlama

Aşağıdaki veri kaynaklarını Azure Sentinel ile entegre ederek güvenlik verilerini toplayabilirsiniz:

- **Azure Active Directory (Azure AD)**: Kullanıcı oturum açma logları ve kimlik doğrulama verileri.
- **Office 365**: E-posta, SharePoint ve OneDrive etkinlikleri.
- **Microsoft Defender for Endpoint**: Cihaz güvenlik olayları.
- **Azure Activity**: Azure kaynakları üzerindeki işlem ve olayların logları.
- **Üçüncü Taraf Cihazlar**: Palo Alto, Cisco, AWS CloudTrail gibi cihazlardan gelen loglar.

Her kaynağı eklemek için Sentinel’in **Data Connectors** bölümünden veri kaynağını seçin ve ilgili adımları izleyin.

## 🔍 Tehdit Tespiti ve Avı (Hunting)

Azure Sentinel, tehdit tespiti ve tehdit avı için yerleşik ve özelleştirilebilir kurallar sunar.

### 1. **Analitik Kuralların Yapılandırılması**

- Sentinel’deki **Analitik Kurallar** (Analytic Rules), güvenlik tehditlerini otomatik olarak tespit eder ve uyarılar oluşturur.
- Varsayılan kuralları kullanabilir ya da **KQL** sorguları ile kendi kurallarınızı oluşturabilirsiniz.

### 2. **KQL Sorguları ile Tehdit Avı (Hunting Queries)**

- KQL kullanarak proaktif olarak tehdit avı yapabilirsiniz. Örnek bir KQL sorgusu:
  ```kql
  SecurityEvent
  | where EventID == 4625  // Başarısız oturum açma olayları
  | where TimeGenerated >= ago(1h)
  | summarize count() by Account, Computer
  | where count_ > 5
  ```
Sentinel, önceden tanımlı Tehdit Avı Sorguları sağlar. Ayrıca, kendi sorgularınızı oluşturup sistemdeki anormallikleri arayabilirsiniz.

## 🛠 Olay Yönetimi ve Soruşturma

Incidents (Olaylar): Sentinel, algıladığı tehditleri olay olarak kaydeder. Bu olaylar üzerinde detaylı analizler ve soruşturmalar yapılabilir.
Investigation Graph: Olaylarla ilişkili varlıklar arasındaki bağlantıları görsel olarak gösteren bir araçtır. Olayların nasıl yayıldığını ve hangi varlıkları etkilediğini analiz eder.

## ⚡ Otomatik Yanıtlar ve Playbook'lar

Sentinel, SOAR (Security Orchestration, Automation, and Response) yetenekleri ile tehditlere karşı otomatik yanıtlar verebilir.
1. Playbook (Logic App) Oluşturma

    Playbook'lar, güvenlik olaylarına karşı otomatik yanıtlar vermek için kullanılır. Örneğin, bir IP adresini engellemek, şüpheli bir kullanıcının oturumunu sonlandırmak veya bir uyarı tespit edildiğinde güvenlik ekibine e-posta göndermek gibi işlemler için kullanılabilir.
    Playbook oluşturmak için Sentinel portalında Automation sekmesine gidin ve Create Playbook butonuna basarak bir Logic App oluşturun.

2. E-posta Bildirimi İçin Playbook Örneği
```
Subject: Azure Sentinel Güvenlik Uyarısı - @{triggerOutputs()?['body/AlertDisplayName']}
Body:
Merhaba,

Bir kritik güvenlik olayı tespit edildi. Detaylar aşağıda:

- Olay Adı: @{triggerOutputs()?['body/AlertDisplayName']}
- Şiddet: @{triggerOutputs()?['body/AlertSeverity']}
- Olay ID: @{triggerOutputs()?['body/IncidentNumber']}
- Açıklama: @{triggerOutputs()?['body/AlertDescription']}
```
Bu Playbook, kritik güvenlik olayları tespit edildiğinde belirlediğiniz e-posta adresine bildirim gönderir.

## 💡 Maliyet Yönetimi ve Optimizasyon
  
  Veri Saklama Süresi: Toplanan log verilerinin saklama süresi (retention), maliyetleri etkileyen önemli bir faktördür. Gereksiz verileri toplayarak maliyetlerinizi artırmak yerine, ihtiyacınıza uygun saklama süreleri belirleyin.
  Maliyet Yönetimi: Sentinel ve Log Analytics Workspace birlikte kullanıldığında, topladığınız verilerin boyutu ve saklama süresi maliyetlerinizi etkiler. Gereksiz veri kaynaklarını ve logları kapatarak maliyetleri optimize edin.

## 📊 Raporlama ve İzleme

  Dashboard'lar: Sentinel’de güvenlik olaylarını ve tehditleri izlemek için özelleştirilmiş dashboard’lar oluşturabilirsiniz.
  Raporlama: Power BI gibi araçlarla entegre ederek güvenlik olaylarını ve tehditleri düzenli olarak raporlayabilirsiniz.

## 📚 Ek Kaynaklar

  Azure Sentinel Documentation
  Kusto Query Language (KQL) Documentation

## 🤝 Katkıda Bulunma

Bu proje ile ilgili geri bildirimlerde bulunmak veya katkıda bulunmak isterseniz, lütfen bir pull request oluşturun ya da issue açın


---

### **README Dosyası Açıklaması:**
1. **Genel Bakış**: Azure Sentinel’in ne olduğunu ve ne amaçla kullanıldığını açıklar.
2. **Başlarken**: Sentinel’in kurulumu, Log Analytics Workspace’in önemi ve nasıl çalıştığını anlatır.
3. **Veri Kaynakları**: Sentinel’e bağlanabilecek veri kaynakları ve nasıl bağlanacakları açıklanır.
4. **Tehdit Tespiti ve Avı**: Analitik kuralların yapılandırılması, KQL sorguları ve tehdit avı işlemleri detaylandırılır.
5. **Olay Yönetimi ve Soruşturma**: Sentinel’deki olay yönetimi ve olayları araştırma adımları.
6. **Otomatik Yanıt ve Playbook'lar**: Playbook oluşturma ve otomatik yanıtlar hakkında bilgi verilir.
7. **Maliyet Yönetimi**: Sentinel ve Log Analytics’in maliyet yönetimi üzerine ipuçları sağlar.
8. **Raporlama ve İzleme**: Sentinel'de dashboard ve rapor oluşturma sürecini kapsar.


  
