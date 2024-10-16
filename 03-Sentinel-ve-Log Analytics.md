# Sentinel ve Log Analytics'in Birlikte Yönetimi

Azure Sentinel ve Log Analytics'in birlikte nasıl yönetileceğini anlamak için aşağıdaki adımları takip edebilirsiniz:
# 1. Log Analytics Workspace Kurulumu ve Yönetimi

  Sentinel’i Etkinleştirin: Azure Sentinel’i etkinleştirdiğinizde, otomatik olarak bir Log Analytics Workspace seçmeniz veya oluşturmanız istenir.
    Log Kaynaklarını Bağlayın: Azure AD, Office 365, Microsoft Defender for Endpoint, üçüncü taraf güvenlik cihazları gibi veri kaynaklarını Log Analytics Workspace’e bağlayın.
        Sentinel’in topladığı veriler, bu çalışma alanında depolanacaktır.
        Örneğin, Office 365 e-posta logları, Azure AD oturum açma verileri, firewall logları gibi kaynaklardan gelen veriler toplanır ve analiz için hazır hale getirilir.

# 2. Sentinel’de Analitik Kurallar Oluşturma

  Kural Yapılandırması: Log Analytics Workspace'te toplanan veriler üzerinde çalışan analitik kurallar oluşturun. Bu kurallar, belirli olaylar gerçekleştiğinde tetiklenir (örneğin, başarısız giriş denemeleri).
    KQL Sorguları ile Uyarılar: Sentinel'de, Log Analytics üzerinde KQL kullanarak özelleştirilmiş uyarılar ve analitik kurallar oluşturabilirsiniz.

    
Örnek bir KQL sorgusu:
```
SecurityEvent
| where EventID == 4625  // Başarısız oturum açma olaylarını izler
| where TimeGenerated >= ago(1h)  // Son 1 saat içindeki olayları alır
| summarize count() by Account, Computer
| where count_ > 5  // 1 saat içinde 5'ten fazla başarısız deneme varsa
```
# 3. Tehdit Avı (Hunting)

  Hunting Queries (Tehdit Avı Sorguları): Sentinel’in tehdit avı özellikleri, Log Analytics üzerinde çalışan sorgularla proaktif tehdit tespitine olanak tanır.
        Önceden tanımlı tehdit avı sorgularını kullanabilir veya KQL ile özel sorgular oluşturabilirsiniz.
        Toplanan veriler üzerinde tehdit avı yaparak şüpheli aktiviteleri manuel olarak tespit edebilirsiniz.

# 4. Olay Yönetimi ve Soruşturma

  Incidents (Olaylar): Sentinel, Log Analytics üzerinde tespit edilen tehditleri olaylara dönüştürür. Olayları Incidents sekmesinden yönetebilir ve araştırabilirsiniz.
        Olaylarla ilgili detaylar (kaynak, IP adresi, kullanıcı) Log Analytics Workspace'teki verilerden çekilir ve görsel bir şekilde sunulur.
        Investigation Graph kullanarak olayın yayılmasını ve ilişkili varlıkları analiz edebilirsiniz.

# 5. Playbook'lar ve Otomatik Yanıtlar

  Playbook (Logic App) Entegrasyonu: Sentinel'deki olaylar ve uyarılar, Log Analytics'ten gelen verilere dayanarak Playbook’lar ile otomatik olarak yanıtlanabilir.
        Örneğin, belirli bir IP adresinden şüpheli trafik tespit edilirse, bu IP adresini otomatik olarak engelleyen bir Playbook çalıştırılabilir.

# 6. Maliyet ve Veri Saklama Yönetimi

  Veri Saklama Süresi: Log Analytics Workspace üzerinde toplanan verilerin ne kadar süreyle saklanacağını belirleyin. İş gereksinimlerinize ve maliyet stratejinize bağlı olarak verilerin saklama süresini ayarlayın.
    Maliyet Optimizasyonu: Toplanan verilerin miktarına ve saklama süresine göre maliyet optimizasyonu yapın. Gereksiz verileri toplamayarak veya saklama sürelerini optimize ederek maliyetleri kontrol edin.

Azure Sentinel ve Log Analytics Workspace, birlikte çalışarak güvenlik olaylarını etkin bir şekilde yönetmek için güçlü bir çözüm sunar:

  Log Analytics Workspace, tüm güvenlik loglarının toplandığı ve işlendiği merkezi bir platformdur.
    Sentinel, bu veriler üzerinde çalışarak tehditleri tespit eder, analiz eder ve olayları yönetir.
    Birlikte çalışarak proaktif tehdit avı, otomatik yanıt ve güvenlik olaylarının etkin bir şekilde yönetilmesi sağlanır.

# Sentinel ve Log Analytics Workspace’i 

birlikte yönetmek, şirketinizin güvenlik tehditlerine karşı daha hızlı yanıt vermesini ve güvenlik durumunu iyileştirmesini sağlar.
