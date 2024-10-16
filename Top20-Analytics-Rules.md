Azure Sentinel'de kullanılan analitik kurallar (Analytics Rules), tehdit algılamayı ve olay yanıtını otomatik hale getiren temel yapı taşlarıdır. Bu kurallar, loglardan ve çeşitli veri kaynaklarından gelen bilgileri analiz ederek şüpheli etkinlikleri tespit eder ve güvenlik ekiplerine uyarılar gönderir.

Aşağıda, Azure Sentinel'de sıklıkla kullanılan 20 analitik kuralı ve nasıl yapılandırıldıklarını detaylı bir şekilde anlatıyorum:
1. Failed Logon Attempts (Başarısız Giriş Denemeleri)

    Açıklama: Belirli bir zaman diliminde başarısız oturum açma denemelerinin sayısı belirli bir eşiği aştığında uyarı verir.
    Yapılandırma: Log verilerinden başarısız oturum açma denemeleri izlenir. Örneğin, 5 dakikada 10 başarısız deneme sonrası tetikleme yapılır.

KQL Sorgusu
```
SecurityEvent
| where EventID == 4625  // 4625 Event ID, başarısız giriş denemelerini temsil eder
| where TimeGenerated >= ago(5m)  // Son 5 dakikadaki olayları alır
| summarize FailedAttempts = count() by TargetUserName, IpAddress, Computer
| where FailedAttempts > 10  // 10’dan fazla başarısız giriş denemesi varsa
| project TimeGenerated, TargetUserName, IpAddress, Computer, FailedAttempts
```

Sorgu Açıklaması:

  SecurityEvent: Güvenlik olaylarını toplamak için kullanılan tablo.
  EventID == 4625: Bu EventID, başarısız oturum açma denemelerini temsil eder.
  TimeGenerated >= ago(5m): Son 5 dakikalık logları filtreler.
  summarize FailedAttempts = count() by TargetUserName, IpAddress, Computer: Kullanıcı adı, IP adresi ve bilgisayara göre başarısız oturum açma denemelerini sayar.
  where FailedAttempts > 10: 10’dan fazla başarısız giriş denemesi varsa sonuç döner.
  project: İlgili sütunları seçerek sonuçları düzenler.

Bu KQL sorgusu, 5 dakika içinde 10 veya daha fazla başarısız oturum açma denemesi olan kullanıcıları belirler. Sentinel üzerinde bu sorgu bir analitik kural olarak yapılandırıldığında, eşik aşıldığında uyarı oluşturulacaktır.


2. Suspicious PowerShell Activity (Şüpheli PowerShell Faaliyetleri)

    Açıklama: Kötü amaçlı PowerShell komutlarının çalıştırılması tespit edildiğinde uyarı verir.
    Yapılandırma: Windows Event Logs üzerinde PowerShell komutlarının izlenmesi. Özellikle sık kullanılan zararlı komutlar (örn. Invoke-Mimikatz) belirlenir.

KQL Sorgusu
```
SecurityEvent
| where EventID == 4104  // PowerShell komutlarının kaydedildiği Event ID
| where CommandLine contains "Invoke-Mimikatz" or CommandLine contains "DownloadString" or CommandLine contains "EncodedCommand"
| project TimeGenerated, Computer, Account, CommandLine
```
Sorgu Açıklaması:

  SecurityEvent: Güvenlik olaylarının bulunduğu tablo.
  EventID == 4104: PowerShell betiklerinin çalıştırıldığı olayları izlemek için kullanılan Event ID. Bu olay, PowerShell komutlarının loglanmasını sağlar.
  CommandLine contains "Invoke-Mimikatz": Invoke-Mimikatz komutunu içeren PowerShell komutlarını arar. Mimikatz, kimlik bilgilerini ele geçiren yaygın bir araçtır.
  CommandLine contains "DownloadString": PowerShell ile uzak bir kaynaktan dosya indirme girişimlerini tespit eder. Bu, kötü amaçlı yazılımların yaygın bir davranışıdır.
  CommandLine contains "EncodedCommand": PowerShell komutlarının şifrelenmiş veya base64 olarak kodlanmış şekilde çalıştırılması. Bu, saldırganların komutları gizlemek için kullandığı yaygın bir tekniktir.
  project: Sadece ilgili sütunları göstermek için TimeGenerated, Computer, Account ve CommandLine sütunlarını seçer.

Sorgu ile Tespit Edilecek Örnek Şüpheli Faaliyetler:

  Invoke-Mimikatz: Kimlik bilgilerini ele geçirmeye yönelik araç olan Mimikatz'ın PowerShell aracılığıyla çalıştırılması.
  DownloadString: Uzak sunuculardan PowerShell komutları veya kötü amaçlı dosyalar indirilmeye çalışılması.
  EncodedCommand: Komutların base64 gibi şifrelenmiş bir formatta çalıştırılarak zararlı kodun gizlenmeye çalışılması.

Bu KQL sorgusu, kötü niyetli PowerShell komutlarının çalıştırıldığını tespit etmek ve şüpheli etkinlikleri raporlamak için Sentinel üzerinde bir analitik kural olarak kullanılabilir.

3. Mass Download of Files (Toplu Dosya İndirme)

    Açıklama: Kısa sürede çok sayıda dosya indirilmesi durumunda uyarı verir.
    Yapılandırma: Office 365 veya SharePoint loglarında büyük veri indirme etkinliklerini izleyerek yapılandırılır.

KQL Sorgusu
```
OfficeActivity
| where OfficeWorkload == "SharePoint" or OfficeWorkload == "OneDrive"
| where Operation == "FileDownloaded"
| where TimeGenerated >= ago(10m)  // Son 10 dakikadaki dosya indirmelerini izler
| summarize DownloadCount = count() by UserId, SiteUrl, IpAddress
| where DownloadCount > 100  // 10 dakikada 100'den fazla dosya indirildiğinde tetiklenir
| project TimeGenerated, UserId, SiteUrl, IpAddress, DownloadCount
```
Sorgu Açıklaması:

  OfficeActivity: Office 365 hizmetleri (SharePoint, OneDrive, vb.) için olay kayıtlarını içerir.
  OfficeWorkload == "SharePoint" or OfficeWorkload == "OneDrive": SharePoint veya OneDrive'daki dosya indirme işlemlerini filtreler.
  Operation == "FileDownloaded": Dosya indirme işlemlerini filtreler.
  TimeGenerated >= ago(10m): Son 10 dakikalık logları alır.
  summarize DownloadCount = count() by UserId, SiteUrl, IpAddress: Kullanıcı kimliği (UserId), site URL’si (SiteUrl) ve IP adresine (IpAddress) göre indirme sayısını toplar.
  where DownloadCount > 100: 10 dakikada 100’den fazla dosya indirildiyse sorgu tetiklenir.
  project: İlgili sütunları görüntüler (indirme zamanı, kullanıcı kimliği, site URL'si, IP adresi, indirme sayısı).

Senaryolar:

  UserId ile kim tarafından indirme yapıldığı belirlenir.
  SiteUrl ile hangi site veya dosya paylaşım alanından indirme yapıldığı izlenir.
  DownloadCount ile kısa sürede çok sayıda dosya indiren kullanıcılar tespit edilir.

Bu sorgu, belirlenen bir zaman diliminde (örneğin, 10 dakika) büyük miktarda dosya indirildiğinde uyarı verilmesini sağlar ve Sentinel üzerinde bir analitik kural olarak yapılandırılabilir.

5. Multiple Failed VPN Logins (Birden Fazla Başarısız VPN Girişi)

    Açıklama: Bir kullanıcı tarafından başarısız VPN giriş denemeleri belirli bir eşiği aştığında tetiklenir.
    Yapılandırma: VPN erişim logları incelenerek başarısız deneme sayısı eşiği belirlenir.

6. Unusual Logon Locations (Olağandışı Giriş Konumları)

    Açıklama: Kullanıcının normal giriş yaptığı bölge dışında başka bir bölgeden giriş yapılırsa uyarı verir.
    Yapılandırma: IP adresi ve bölge bilgisi loglarından analiz edilir. Normalde giriş yapılan bölgeler tanımlanır.

7. RDP Brute Force Attack (RDP Brute Force Saldırısı)

    Açıklama: Bir IP adresinden birçok başarısız RDP giriş denemesi yapıldığında uyarı verir.
    Yapılandırma: Belirli bir zaman diliminde belirli bir IP'den gelen başarısız RDP giriş denemelerini izler.

8. Suspicious Administrative Activity (Şüpheli Yönetici Faaliyetleri)

    Açıklama: Yönetici yetkilerine sahip kullanıcılar tarafından yapılan şüpheli işlemler tespit edildiğinde uyarı verir.
    Yapılandırma: Yöneticilerin gerçekleştirdiği olağandışı işlemleri (örn. birden fazla hesap oluşturma) takip eder.

9. Unusual File Sharing Activity (Olağandışı Dosya Paylaşımı)

    Açıklama: Kısa sürede birden fazla dosya paylaşımı yapıldığında uyarı verir.
    Yapılandırma: SharePoint ve OneDrive logları üzerinden çok sayıda dosya paylaşımı tespit edilir.

10. New User Account Creation (Yeni Kullanıcı Hesabı Oluşturulması)

    Açıklama: Sistem üzerinde yeni bir kullanıcı hesabı oluşturulduğunda uyarı verir.
    Yapılandırma: Azure Active Directory logları izlenir ve her yeni hesap oluşturulduğunda uyarı tetiklenir.

11. Elevated Privileges Assignment (Yüksek Yetki Ataması)

    Açıklama: Bir kullanıcıya yönetici yetkisi atandığında uyarı verir.
    Yapılandırma: Yetki atama işlemlerinin logları incelenir ve yöneticilik yetkisi verildiğinde tetiklenir.

12. Malicious URL Detected (Zararlı URL Tespit Edildi)

    Açıklama: Kullanıcının kötü amaçlı bir URL'ye erişmeye çalışması durumunda uyarı verir.
    Yapılandırma: DNS logları ve güvenlik kaynaklarından gelen tehdit istihbaratını kullanarak zararlı URL'ler izlenir.

13. Unusual Outbound Traffic (Olağandışı Çıkış Trafiği)

    Açıklama: Ağı terk eden alışılmadık miktarda veri tespit edildiğinde uyarı verir.
    Yapılandırma: Ağ trafiği logları incelenir, olağan dışı yüksek miktarda veri çıkışı olduğunda tetiklenir.

14. Impersonation of User Accounts (Kullanıcı Hesabı Taklidi)

    Açıklama: Bir kullanıcının kimliğini taklit eden giriş denemeleri tespit edildiğinde uyarı verir.
    Yapılandırma: Kullanıcı logon davranışlarını analiz ederek normal dışı etkinlikleri tespit eder.

15. Multiple File Deletions (Çoklu Dosya Silinmesi)

    Açıklama: Kısa sürede birden fazla dosyanın silinmesi durumunda uyarı verir.
    Yapılandırma: Windows Event Log ve dosya sistem loglarını izleyerek anormal dosya silme etkinliklerini izler.

16. Azure Resource Deletion (Azure Kaynaklarının Silinmesi)

    Açıklama: Kritik bir Azure kaynağının silinmesi durumunda uyarı verir.
    Yapılandırma: Azure aktiviteleri üzerinden kaynakların silinmesi takip edilir.

17. Unusual Azure Resource Activity (Olağandışı Azure Kaynağı Etkinliği)

    Açıklama: Azure üzerinde olağandışı etkinlikler tespit edildiğinde uyarı verir.
    Yapılandırma: Azure kaynağı üzerindeki işlem logları analiz edilerek, anormal etkinlikler tespit edilir.

18. Email Forwarding Rules Added (E-posta Yönlendirme Kuralları Eklenmesi)

    Açıklama: Bir kullanıcının e-postalarını başka bir hesaba yönlendirme kuralı eklenirse uyarı verir.
    Yapılandırma: Office 365 loglarında yeni yönlendirme kuralları eklendiğinde tespit edilir.

19. Suspicious Process Execution (Şüpheli Süreç Çalıştırma)

    Açıklama: Şüpheli bir uygulama veya süreç çalıştırıldığında uyarı verir.
    Yapılandırma: Windows Event Log'larından veya endpoint güvenlik verilerinden süreç yürütmeleri izlenir.

20. Data Exfiltration Detection (Veri Sızdırma Tespiti)

    Açıklama: Şüpheli veri sızdırma faaliyetleri tespit edildiğinde uyarı verir.
    Yapılandırma: Ağ trafiği ve dosya hareketleri izlenerek büyük veri çıkışı tespit edilir.

21. Suspicious Application Installation (Şüpheli Uygulama Yükleme)

    Açıklama: Şüpheli bir uygulamanın sisteme yüklenmesi durumunda uyarı verir.
    Yapılandırma: Uygulama yükleme logları takip edilir ve normal dışı bir uygulama yüklemesi tespit edildiğinde tetiklenir.