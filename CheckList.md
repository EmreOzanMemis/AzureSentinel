Azure Sentinel ile çalışırken kullanılabilecek bir kontrol listesi (checklist), sistemin doğru şekilde yapılandırıldığından ve tehdit algılama yeteneklerinin verimli bir şekilde çalıştığından emin olmak için kritik bir araçtır. Aşağıda, Azure Sentinel'in kurulumu, yapılandırılması ve yönetimi sırasında kullanabileceğiniz kapsamlı bir kontrol listesi oluşturuyorum.
Azure Sentinel Kontrol Listesi
1. Kurulum ve Genel Yapılandırma

Azure Sentinel’i etkinleştirin: Azure Portal üzerinden Sentinel’i etkinleştirin ve Log Analytics Workspace’i yapılandırın.

Kaynak Grubu: Doğru kaynak grubunun seçildiğinden emin olun.
    Workspace: Log Analytics çalışma alanı doğru bir şekilde yapılandırıldı mı?

Veri Kaynaklarını Bağlayın: Sentinel'deki önemli veri kaynaklarını bağlayın (Data Connectors).

Azure Active Directory (Azure AD): Kimlik doğrulama ve oturum açma olayları için.
Office 365: E-posta, SharePoint, OneDrive loglarını izleyin.
Microsoft Defender for Endpoint: Cihaz güvenlik verilerini toplayın.
Azure Activity: Azure kaynaklarını ve değişikliklerini izleyin.
Windows Security Events: Sunucu ve istemci loglarını izleyin.

Diğer Üçüncü Parti Kaynaklar: AWS CloudTrail, Palo Alto Networks vb. dış kaynakları bağladınız mı?

2. Tehdit Algılama ve Uyarı (Detection & Alerts)

Analitik Kurallarını Yapılandırın: Sistem üzerindeki şüpheli aktiviteleri algılayan analitik kurallar ekleyin.

Varsayılan kural setleri etkinleştirildi mi?
Özel kurallar oluşturuldu mu (örneğin, başarısız giriş denemeleri, olağandışı işlem)?

Kuralların sıklığı ve şiddet seviyeleri doğru yapılandırıldı mı?

KQL Sorguları ile Özel Algılama Kuralları: Özel sorgular oluşturup, sisteme uygun tehdit algılama yapılandırıldı mı?

Kendi KQL sorgularınızı yazdınız mı?

Özelleştirilmiş uyarılar tanımladınız mı?

Uyarı Tetiklemelerini Test Edin: Olayların başarılı şekilde tetiklenip tetiklenmediğini doğrulamak için test yapın.

Analitik kurallar tetiklendikten sonra doğru şekilde uyarı üretiyor mu?

3. Olay Yönetimi ve Soruşturma (Incidents & Investigations)

Olay (Incident) Yönetimi: Sentinel’in otomatik oluşturduğu olayları düzenli olarak kontrol edin.

Olayların durumu incelendi mi (Açık, Kapatıldı, İnceleme Aşamasında)?

Olayların şiddet seviyesine göre (Low, Medium, High) öncelik verildi mi?

Olay Soruşturma Aracını Kullanın: Olayların detaylı soruşturulması için Investigation Graph kullanılıyor mu?

Şüpheli varlıklar arasında grafiksel bağlantılar doğru görünüyor mu?

Zaman çizelgesi (timeline) kullanılarak olayların hangi aşamada gerçekleştiği izleniyor mu?

Otomatik Yanıt Kuralları: Kritik olaylara otomatik yanıt için Playbook'lar yapılandırıldı mı?

Otomatik yanıt tetikleyicileri (örneğin, e-posta gönderme, IP engelleme) kuruldu mu?

Otomatik yanıt kuralları test edildi mi?

4. Tehdit Avı (Hunting)

Tehdit Avı (Hunting) Yapılandırması: Sentinel’de mevcut olan tehdit avı sorgularını etkinleştirdiniz mi?

Varsayılan av sorguları çalıştırıldı mı?

Özelleştirilmiş av sorguları oluşturuldu mu?

Proaktif Tehdit Avı: Potansiyel tehditleri proaktif olarak taramak için sorgular düzenli olarak çalıştırılıyor mu?

Olası tehdit ve anormallikler av sorguları ile tespit ediliyor mu?

Tehdit Avı Sonuçlarını Olaylara Dönüştürün: Tespit edilen anormallikler, olay yönetim sistemine aktarıldı mı?

5. Playbooks ve Otomatik Yanıtlar (Playbooks & Automated Responses)

Playbook (Logic App) Kurulumu: Otomatik yanıtlar için Playbook oluşturuldu mu?

E-posta bildirimleri, IP engelleme gibi otomatik eylemler yapılandırıldı mı?

Playbook'lar Olaylarla Entegre Edildi mi?

Önemli olaylar veya tetikleyiciler için Playbook’lar olaylara atandı mı?

 Playbook’lar manuel veya otomatik çalıştırılıyor mu?

Playbook Testleri Yapıldı mı?

Playbook’un tetiklenip doğru çalıştığı test edildi mi?

6. Güvenlik Politikaları ve İzinler (Security Policies & Permissions)

Sentinel İzinleri Kontrolü: Sentinel’e erişim sağlayan kullanıcıların doğru rollerle atanmış olduğundan emin olun.

Kullanıcılar, Sentinel’deki rollerine göre doğru yetkilere sahip mi (örneğin, Reader, Contributor)?

Yalnızca yetkili kullanıcılar Sentinel'e erişebiliyor mu?

Log Retention ve Veri Yönetimi: Verilerin saklama süresi ve yönetimi yapılandırıldı mı?

Log saklama süreleri (retention) iş gereksinimlerine göre ayarlandı mı?

Veri ve log kullanım maliyetleri izleniyor mu?

7. Kural Güncellemeleri ve Optimizasyon

Analitik ve Av Sorgularının Optimizasyonu: Kullanılan kurallar ve sorgular düzenli olarak güncelleniyor mu?

Yanlış pozitif sonuçları en aza indirmek için kurallar ve sorgular optimize ediliyor mu?

Raporlama ve Dashboards: Sentinel’deki olaylar ve performanslar için raporlama yapılandırıldı mı?

Güvenlik olaylarını takip etmek için dashboard’lar oluşturuldu mu?

8. Performans ve Maliyet İzleme (Monitoring & Cost Management)

Sentinel Performansını İzleyin: Sentinel’in performansı izleniyor mu?

Log toplama ve işlem süreleri izleniyor mu?

Sentinel'in kaynak tüketimi ve performansı optimize ediliyor mu?

Maliyet Yönetimi: Sentinel maliyetlerinin izlenmesi için maliyet yönetimi yapılandırıldı mı?

Sentinel kullanımının ve log boyutlarının maliyet üzerindeki etkisi düzenli olarak inceleniyor mu?

Maliyet optimizasyonu için gereksiz log kaynakları temizlendi mi?

Bu Azure Sentinel Check List, Sentinel’in kurulumu ve yönetimi sırasında güvenlik önlemlerinin doğru bir şekilde yapılandırıldığından emin olmak için kullanılabilir. Bu listeyi düzenli olarak gözden geçirip, tehdit algılama ve olay müdahale süreçlerinizin etkili çalıştığından emin olabilirsiniz.
