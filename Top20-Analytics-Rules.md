Azure Sentinel'de kullanılan analitik kurallar (Analytics Rules), tehdit algılamayı ve olay yanıtını otomatik hale getiren temel yapı taşlarıdır. Bu kurallar, loglardan ve çeşitli veri kaynaklarından gelen bilgileri analiz ederek şüpheli etkinlikleri tespit eder ve güvenlik ekiplerine uyarılar gönderir.

Aşağıda, Azure Sentinel'de sıklıkla kullanılan 20 analitik kuralı ve nasıl yapılandırıldıklarını detaylı bir şekilde anlatıyorum:

# 1. Failed Logon Attempts (Başarısız Giriş Denemeleri)

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


# 2. Suspicious PowerShell Activity (Şüpheli PowerShell Faaliyetleri)

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

# 3. Mass Download of Files (Toplu Dosya İndirme)**

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

# 4. Multiple Failed VPN Logins (Birden Fazla Başarısız VPN Girişi)

Açıklama: Bir kullanıcı tarafından başarısız VPN giriş denemeleri belirli bir eşiği aştığında tetiklenir.
Yapılandırma: VPN erişim logları incelenerek başarısız deneme sayısı eşiği belirlenir.

VPN erişim logları genellikle "VPN başarısız giriş" olaylarını içerir. Bu loglar, belirli bir event ID veya log kaynağına (örneğin, NetworkSecurityEvent) bağlı olabilir. Aşağıdaki örnek KQL sorgusu, VPN başarısız giriş denemelerini tespit eder ve belirli bir eşikten fazla deneme olduğunda uyarı verir.

KQL Sorgusu
```
SigninLogs
| where ResultType == "50126" or ResultType == "50076"  // VPN başarısız giriş denemelerini temsil eden hata kodları
| where TimeGenerated >= ago(10m)  // Son 10 dakika içindeki logları alır
| summarize FailedAttempts = count() by UserPrincipalName, IPAddress
| where FailedAttempts > 5  // 5'ten fazla başarısız giriş denemesi varsa
| project TimeGenerated, UserPrincipalName, IPAddress, FailedAttempts
```
Sorgu Açıklaması:

SigninLogs: Azure AD üzerinde VPN ve diğer giriş denemelerinin loglarını içerir.
ResultType == "50126" or ResultType == "50076": Başarısız oturum açma girişimlerini temsil eden hata kodlarıdır. (Bu kodlar kullanılan VPN altyapısına göre değişebilir; kullanılan VPN sistemine özgü kodlar araştırılmalıdır).
        50126: Yanlış kimlik bilgileri hatası.
        50076: Çok faktörlü kimlik doğrulama hatası.
TimeGenerated >= ago(10m): Son 10 dakikalık logları filtreler.
summarize FailedAttempts = count() by UserPrincipalName, IPAddress: Kullanıcı kimliği (UserPrincipalName) ve IP adresine göre başarısız giriş denemelerini sayar.
where FailedAttempts > 5: 5'ten fazla başarısız giriş denemesi varsa sonuçları filtreler.
project: İlgili sütunları (zaman, kullanıcı kimliği, IP adresi, başarısız deneme sayısı) görüntüler.

Kullanım Senaryosu:

UserPrincipalName: VPN'e bağlanmaya çalışan kullanıcının kimliği.
IPAddress: Kullanıcının bağlandığı IP adresi.
FailedAttempts: Kısa sürede tekrarlayan başarısız giriş denemeleri.

Bu KQL sorgusu, belirli bir süre zarfında (örneğin 10 dakika) bir kullanıcı tarafından yapılan başarısız VPN giriş denemelerinin sayısını toplar ve belirli bir eşikten (örneğin 5 deneme) fazla deneme olduğunda uyarı verir. Bu kuralı Azure Sentinel'de bir analitik kural olarak yapılandırarak, VPN girişleri üzerindeki anormal etkinlikleri izleyebilirsiniz.


# 5. Unusual Logon Locations (Olağandışı Giriş Konumları)

KQL Sorgusu
```
let known_locations = 
SigninLogs
| where ResultType == "0"  // Başarılı giriş denemelerini filtreler
| summarize LastKnownLocation = arg_max(TimeGenerated, Location) by UserPrincipalName
| project UserPrincipalName, LastKnownLocation;

SigninLogs
| where ResultType == "0"  // Başarılı giriş denemelerini filtreler
| where TimeGenerated >= ago(1d)  // Son 24 saatteki giriş denemelerini alır
| join kind=leftanti (
    known_locations 
    ) on UserPrincipalName, Location  // Bilinen konumlarla eşleşmeyen giriş denemelerini bulur
| project TimeGenerated, UserPrincipalName, Location, IPAddress
```
Sorgu Açıklaması:

let known_locations: Kullanıcının daha önce başarılı giriş yaptığı konumları tanımlar.
SigninLogs: Azure AD üzerindeki giriş loglarını içerir.
ResultType == "0": Başarılı oturum açma girişimlerini filtreler.
arg_max(TimeGenerated, Location): Kullanıcının son başarılı oturum açma girişini ve bölgesini alır.
project: Kullanıcı adı ve son bilinen giriş bölgesini seçer.

join kind=leftanti: Yeni girişleri, daha önce bilinen giriş konumlarıyla karşılaştırır. Eğer kullanıcı, daha önce giriş yaptığı konumlardan farklı bir yerden oturum açıyorsa bu giriş sorgu sonucu olarak döner.

project: Zaman, kullanıcı adı, giriş bölgesi ve IP adresi gibi bilgileri seçer.

Sonuç:

Bu sorgu, kullanıcının normalde giriş yaptığı bölge (Location) dışındaki girişleri tespit eder. Eğer bir kullanıcı bilinen konumunun dışındaki bir bölgeden giriş yaparsa, bu olağandışı giriş olarak kabul edilir ve uyarı tetiklenir.
Kullanım Senaryosu:

UserPrincipalName: Giriş yapan kullanıcının kimliği.
Location: Kullanıcının giriş yaptığı coğrafi bölge veya ülke.
IPAddress: Giriş yapılan cihazın IP adresi.
TimeGenerated: Giriş zamanını gösterir.

Bu KQL sorgusu, Azure Sentinel üzerinde olağandışı bölgeden yapılan girişleri tespit etmek için kullanılır. Eğer bir kullanıcı genellikle belirli bir ülkeden oturum açıyorsa ve aniden farklı bir ülkeden giriş yaparsa, bu potansiyel olarak güvenlik ihlali olarak değerlendirilebilir ve uyarı oluşturulabilir.

Açıklama: Kullanıcının normal giriş yaptığı bölge dışında başka bir bölgeden giriş yapılırsa uyarı verir.
Yapılandırma: IP adresi ve bölge bilgisi loglarından analiz edilir. Normalde giriş yapılan bölgeler tanımlanır.

# 6. RDP Brute Force Attack (RDP Brute Force Saldırısı)

Açıklama: Bir IP adresinden birçok başarısız RDP giriş denemesi yapıldığında uyarı verir.
Yapılandırma: Belirli bir zaman diliminde belirli bir IP'den gelen başarısız RDP giriş denemelerini izler.

KQL Sorgusu
```
SecurityEvent
| where EventID == 4625  // Başarısız oturum açma olaylarını izler (4625 EventID RDP başarısız oturum açma)
| where TimeGenerated >= ago(1h)  // Son 1 saatlik giriş denemelerini izler
| where LogonType == 10  // LogonType 10, RDP oturumlarını temsil eder
| summarize FailedAttempts = count() by IpAddress, Account, Computer
| where FailedAttempts > 5  // 1 saat içinde 5'ten fazla başarısız deneme varsa
| project TimeGenerated, IpAddress, Account, Computer, FailedAttempts
```
Sorgu Açıklaması:

  SecurityEvent: Windows güvenlik olaylarını toplamak için kullanılan tablo.
  EventID == 4625: Bu EventID, başarısız oturum açma denemelerini temsil eder. Brute force saldırıları genellikle başarısız giriş denemelerinden oluşur.
  TimeGenerated >= ago(1h): Son 1 saat içindeki giriş denemelerini filtreler.
  LogonType == 10: LogonType 10, uzak masaüstü protokolü (RDP) üzerinden yapılan oturum açma denemelerini belirtir.
  summarize FailedAttempts = count() by IpAddress, Account, Computer: Belirli bir IP adresi, kullanıcı hesabı ve bilgisayara göre başarısız giriş denemelerini sayar.
  where FailedAttempts > 5: Aynı IP adresinden 5'ten fazla başarısız giriş denemesi olduğunda sonuç döner. Eşik değer ihtiyaca göre ayarlanabilir.
  project: İlgili sütunları (zaman, IP adresi, hesap, bilgisayar adı ve başarısız giriş sayısı) görüntüler.

Kullanım Senaryosu:

  IpAddress: Brute force saldırısını gerçekleştiren kaynağın IP adresi.
  Account: Saldırganın oturum açmaya çalıştığı kullanıcı hesabı.
  Computer: Saldırının hedeflendiği bilgisayar adı.
  FailedAttempts: Belirlenen zaman diliminde (1 saat) yapılan başarısız giriş denemelerinin sayısı.

Uyarı Tetikleme:

Bu KQL sorgusu, Azure Sentinel'de bir analitik kural olarak yapılandırıldığında, aynı IP adresinden belirli bir süre (örneğin 1 saat) içerisinde çok sayıda başarısız RDP giriş denemesi yapıldığında uyarı oluşturur. Bu tür bir saldırı, brute force saldırılarının yaygın bir göstergesidir ve erken müdahale edilmesi gereken bir güvenlik olayıdır.



# 7. Suspicious Administrative Activity (Şüpheli Yönetici Faaliyetleri)

  Açıklama: Yönetici yetkilerine sahip kullanıcılar tarafından yapılan şüpheli işlemler tespit edildiğinde uyarı verir.
  Yapılandırma: Yöneticilerin gerçekleştirdiği olağandışı işlemleri (örn. birden fazla hesap oluşturma) takip eder.

KQL Sorgusu
```
AuditLogs
| where OperationName == "Add user"  // Kullanıcı ekleme işlemi
| where TimeGenerated >= ago(1h)  // Son 1 saat içindeki işlemleri izler
| where InitiatedBy.role == "Global Administrator" or InitiatedBy.role == "User Administrator"  // Yönetici rolüne sahip kullanıcılar
| summarize UserCreations = count() by InitiatedBy.userPrincipalName, InitiatedBy.role
| where UserCreations > 3  // 1 saat içinde 3'ten fazla kullanıcı ekleme varsa
| project TimeGenerated, InitiatedBy.userPrincipalName, InitiatedBy.role, UserCreations
```

Sorgu Açıklaması:

  AuditLogs: Azure Active Directory’de gerçekleştirilen işlemleri izleyen tablo. Yönetici faaliyetleri burada loglanır.
  OperationName == "Add user": Kullanıcı ekleme işlemlerini filtreler.
  TimeGenerated >= ago(1h): Son 1 saat içindeki işlemleri izler.
  InitiatedBy.role == "Global Administrator" or InitiatedBy.role == "User Administrator": İşlemi başlatan kullanıcının rolünü kontrol eder. Sadece Global Administrator veya User Administrator gibi yönetici rolleriyle yapılan işlemleri izler.
  summarize UserCreations = count() by InitiatedBy.userPrincipalName, InitiatedBy.role: Her bir yönetici tarafından kaç kullanıcı oluşturulduğunu toplar.
  where UserCreations > 3: 1 saat içinde 3’ten fazla kullanıcı ekleyen yöneticileri filtreler. (Bu eşik ihtiyaca göre ayarlanabilir.)
  project: Zaman, yönetici kullanıcı adı, rol ve kullanıcı oluşturma sayısını gösterir.

Senaryolar:

  userPrincipalName: Yönetici yetkisine sahip kullanıcı adı.
  role: Yöneticinin sahip olduğu rol (örn. Global Administrator, User Administrator).
  UserCreations: Belirtilen zaman diliminde yönetici tarafından eklenen kullanıcı sayısı.
  TimeGenerated: İşlemin gerçekleştiği zaman.

# 8. Unusual File Sharing Activity (Olağandışı Dosya Paylaşımı)

  Açıklama: Kısa sürede birden fazla dosya paylaşımı yapıldığında uyarı verir.
  Yapılandırma: SharePoint ve OneDrive logları üzerinden çok sayıda dosya paylaşımı tespit edilir.

KQL Sorgusu
```
OfficeActivity
| where OfficeWorkload == "SharePoint" or OfficeWorkload == "OneDrive"  // SharePoint veya OneDrive işlemlerini filtreler
| where Operation == "FileShared"  // Dosya paylaşım işlemlerini izler
| where TimeGenerated >= ago(1h)  // Son 1 saat içindeki işlemleri izler
| summarize ShareCount = count() by UserId, OfficeWorkload, ClientIP
| where ShareCount > 10  // 1 saat içinde 10'dan fazla dosya paylaşımı varsa
| project TimeGenerated, UserId, OfficeWorkload, ClientIP, ShareCount
```
Sorgu Açıklaması:

  OfficeActivity: Office 365 hizmetlerine ait logları içerir. SharePoint ve OneDrive etkinlikleri burada kaydedilir.
  OfficeWorkload == "SharePoint" or OfficeWorkload == "OneDrive": SharePoint veya OneDrive ile ilişkili işlemleri filtreler.
  Operation == "FileShared": Sadece dosya paylaşım işlemlerini izler.
  TimeGenerated >= ago(1h): Son 1 saat içindeki dosya paylaşım etkinliklerini alır.
  summarize ShareCount = count() by UserId, OfficeWorkload, ClientIP: Kullanıcı kimliği (UserId), hizmet (OfficeWorkload: SharePoint veya OneDrive) ve IP adresine göre dosya paylaşım sayısını toplar.
  where ShareCount > 10: 1 saat içinde 10’dan fazla dosya paylaşımı varsa sonucu filtreler. (Bu eşik ihtiyaca göre ayarlanabilir.)
  project: İlgili bilgileri (zaman, kullanıcı kimliği, hizmet, IP adresi, paylaşım sayısı) gösterir.

Kullanım Senaryosu:

  UserId: Dosya paylaşımı yapan kullanıcının kimliği.
  OfficeWorkload: Dosya paylaşımının yapıldığı hizmet (SharePoint veya OneDrive).
  ClientIP: Dosya paylaşımının yapıldığı cihazın IP adresi.
  ShareCount: Kısa bir süre içinde yapılan dosya paylaşım sayısı.

Olası Şüpheli Durumlar:

  Bir kullanıcı kısa bir süre içinde olağandışı sayıda dosya paylaştığında, bu sorgu bu kullanıcıyı işaret eder. Bu, bir veri ihlalinin veya yetkisiz dosya paylaşımının işareti olabilir.

Uyarı Tetikleme:

Bu KQL sorgusu, Azure Sentinel üzerinde kısa süre içinde çok sayıda dosya paylaşım etkinliğini izler. Bir kullanıcının belirli bir zaman diliminde (örneğin 1 saat) olağandışı sayıda dosya paylaşımı yapması durumunda, bu sorgu şüpheli bir durumu tespit ederek uyarı tetiklenmesini sağlar.

Bu kuralı Azure Sentinel üzerinde bir analitik kural olarak yapılandırarak, SharePoint ve OneDrive üzerindeki şüpheli dosya paylaşım etkinliklerini izleyebilirsiniz.


# 9. New User Account Creation (Yeni Kullanıcı Hesabı Oluşturulması)

  Açıklama: Sistem üzerinde yeni bir kullanıcı hesabı oluşturulduğunda uyarı verir.
  Yapılandırma: Azure Active Directory logları izlenir ve her yeni hesap oluşturulduğunda uyarı tetiklenir.

KQL Sorgusu
```
AuditLogs
| where OperationName == "Add user"  // Kullanıcı ekleme işlemini izler
| where Result == "success"  // Başarılı işlemleri filtreler
| where TimeGenerated >= ago(1h)  // Son 1 saat içindeki işlemleri izler
| project TimeGenerated, TargetResources.displayName, InitiatedBy.userPrincipalName, InitiatedBy.role, TargetResources.userPrincipalName
```
Sorgu Açıklaması:

  AuditLogs: Azure Active Directory üzerindeki tüm denetim ve yönetim işlemlerini içeren tablo.
  OperationName == "Add user": Yeni bir kullanıcı hesabı ekleme işlemini izler.
  Result == "success": Sadece başarılı kullanıcı ekleme işlemlerini filtreler.
  TimeGenerated >= ago(1h): Son 1 saat içinde yapılan kullanıcı ekleme işlemlerini getirir.
  project: İlgili sütunları seçer:
        TimeGenerated: Hesabın oluşturulduğu zamanı gösterir.
        TargetResources.displayName: Yeni oluşturulan kullanıcının adını gösterir.
        InitiatedBy.userPrincipalName: Kullanıcıyı oluşturan yönetici ya da kullanıcı.
        InitiatedBy.role: Kullanıcıyı oluşturan kişinin rolü.
        TargetResources.userPrincipalName: Yeni kullanıcının kullanıcı adı.

Kullanım Senaryosu:

  TargetResources.displayName: Yeni oluşturulan kullanıcı hesabının adı.
  InitiatedBy.userPrincipalName: Yeni kullanıcıyı oluşturan kişi veya yönetici.
  InitiatedBy.role: Kullanıcıyı oluşturan kişinin rolü (örneğin, Global Administrator).
  TimeGenerated: Kullanıcı oluşturulma zamanı.

Olası Şüpheli Durumlar:

  Yetkisiz bir yönetici veya beklenmedik bir zaman diliminde yeni bir kullanıcı oluşturulduğunda şüphe uyandırabilir.
  Yönetici hesapları üzerinden izinsiz kullanıcı hesapları oluşturulması, kötü amaçlı bir saldırganın kimlik bilgilerine erişmiş olabileceğini gösterebilir.

Uyarı Tetikleme:

Bu KQL sorgusu, Azure Sentinel'de yeni bir kullanıcı hesabı oluşturulduğunda uyarı vermek için kullanılabilir. Azure AD'de her yeni hesap oluşturulduğunda bu sorgu tetiklenir ve güvenlik ekiplerine bilgi sağlar.

Bu kural, Sentinel’de bir analitik kural olarak yapılandırıldığında, beklenmedik kullanıcı hesaplarının oluşturulmasını tespit ederek hızlı bir şekilde güvenlik ihlallerine yanıt vermenizi sağlar.

# 10. Elevated Privileges Assignment (Yüksek Yetki Ataması)

  Açıklama: Bir kullanıcıya yönetici yetkisi atandığında uyarı verir.
  Yapılandırma: Yetki atama işlemlerinin logları incelenir ve yöneticilik yetkisi verildiğinde tetiklenir.

KQL Sorgusu
```
AuditLogs
| where OperationName == "Add member to role"  // Yetki atama işlemlerini izler
| where Result == "success"  // Başarılı atamaları filtreler
| where TimeGenerated >= ago(1h)  // Son 1 saat içindeki işlemleri izler
| where TargetResources[0].modifiedProperties[0].newValue contains "Admin"  // Atanan rolün yönetici yetkisi içerdiğini kontrol eder
| project TimeGenerated, InitiatedBy.userPrincipalName, InitiatedBy.role, TargetResources[0].displayName, TargetResources[0].modifiedProperties[0].newValue
```
Sorgu Açıklaması:

  AuditLogs: Azure Active Directory üzerindeki denetim ve yönetim işlemlerini izler.
  OperationName == "Add member to role": Bir kullanıcının bir role atandığını izler. Yönetici yetkisi atama işlemleri bu olay ile gerçekleşir.
  Result == "success": Başarılı yetki atama işlemlerini filtreler.
  TimeGenerated >= ago(1h): Son 1 saat içinde gerçekleşen yetki atama işlemlerini alır.
  TargetResources[0].modifiedProperties[0].newValue contains "Admin": Atanan rolün adında "Admin" ifadesi geçip geçmediğini kontrol eder. Bu, kullanıcının bir yönetici rolüne (örneğin Global Admin, User Admin vb.) atandığını tespit eder.
  project: İlgili bilgileri seçer:
        TimeGenerated: Yetki atama işleminin gerçekleştiği zaman.
        InitiatedBy.userPrincipalName: Yetki atamasını yapan kişi.
        InitiatedBy.role: Yetki atamasını yapan kişinin rolü.
        TargetResources[0].displayName: Yetki ataması yapılan kullanıcının adı.
        TargetResources[0].modifiedProperties[0].newValue: Atanan yeni rol.

Kullanım Senaryosu:

  InitiatedBy.userPrincipalName: Yetki atama işlemini gerçekleştiren kişi.
  InitiatedBy.role: Yetki atamasını yapan kullanıcının rolü.
  TargetResources[0].displayName: Yüksek yetki verilen kullanıcının adı.
  newValue: Yeni atanmış olan yönetici rolü (örn. Global Admin, User Admin).

Olası Şüpheli Durumlar:

  Bir kullanıcının yüksek yetki (admin) alması, özellikle beklenmedik bir zamanda veya yetkisiz kişilerce yapıldığında dikkat edilmesi gereken bir güvenlik olayıdır.
  Bir saldırgan, sistemdeki başka bir kullanıcıya yönetici yetkisi vererek sistemde daha geniş erişim elde etmeye çalışabilir.

Uyarı Tetikleme:

Bu KQL sorgusu, yönetici yetkisi atamaları izleyerek Sentinel üzerinde bir analitik kural olarak kullanılabilir. Bir kullanıcıya admin rolü atandığında bu sorgu çalışır ve yetki atama işlemiyle ilgili detayları gösterir. Azure Sentinel üzerinde bu kural yapılandırıldığında, yüksek yetki atamaları sırasında güvenlik ekiplerine hızlı bir şekilde bilgi verir ve olası tehditlere karşı erken müdahale imkanı sağlar.

# 11. Malicious URL Detected (Zararlı URL Tespit Edildi)

  Açıklama: Kullanıcının kötü amaçlı bir URL'ye erişmeye çalışması durumunda uyarı verir.
  Yapılandırma: DNS logları ve güvenlik kaynaklarından gelen tehdit istihbaratını kullanarak zararlı URL'ler izlenir.

KQL Sorgusu
```
DnsEvents
| where TimeGenerated >= ago(1h)  // Son 1 saatlik DNS sorgularını izler
| where QueryType == "Query"  // Sadece DNS sorgularını filtreler
| join kind=inner (
    ThreatIntelligenceIndicator
    | where Active == true  // Aktif tehdit istihbaratlarını kullanır
    | where ExpirationDateTime > now()  // Süresi dolmamış istihbarat verilerini alır
    | where ThreatType == "malicious-url"  // Tehdit türü zararlı URL olanları seçer
    ) on $left.QueryName == $right.NetworkIPv4  // DNS sorgusundaki URL ile tehdit istihbaratı verisini karşılaştırır
| project TimeGenerated, QueryName, ClientIP, ThreatType, Description, ExpirationDateTime
```
Sorgu Açıklaması:

  DnsEvents: DNS sorgularını içeren tablo. Bu tablo, kullanıcıların ziyaret ettiği alan adlarını ve DNS sorgularını içerir.
  TimeGenerated >= ago(1h): Son 1 saat içinde yapılan DNS sorgularını filtreler.
  QueryType == "Query": Sadece DNS sorgularını izler.
  join kind=inner (ThreatIntelligenceIndicator): DNS sorgularını tehdit istihbaratı ile birleştirir. ThreatIntelligenceIndicator tablosu, zararlı URL'ler ve IP adresleri gibi tehdit bilgilerini içerir.
  where ThreatType == "malicious-url": Tehdit istihbaratında zararlı URL olarak tanımlanmış alan adlarını seçer.
  on $left.QueryName == $right.NetworkIPv4: DNS sorgusundaki alan adı ile tehdit istihbaratı verisindeki zararlı URL'leri karşılaştırır.
  project: İlgili bilgileri seçer:
        TimeGenerated: DNS sorgusunun zaman damgası.
        QueryName: Kullanıcının erişmeye çalıştığı URL.
        ClientIP: DNS sorgusunu başlatan kullanıcının IP adresi.
        ThreatType: Tespit edilen tehdit türü (zararlı URL).
        Description: Tehdit hakkında ek bilgiler (örneğin, URL'nin zararlı olduğu tanımı).
        ExpirationDateTime: Tehdit istihbaratının geçerlilik süresi.

Kullanım Senaryosu:

  QueryName: Kullanıcının DNS sorgusu aracılığıyla erişmeye çalıştığı zararlı URL.
  ClientIP: Bu DNS sorgusunu yapan cihazın IP adresi.
  ThreatType: Tespit edilen tehdidin türü (zararlı URL).
  Description: Tehdit hakkında daha fazla bilgi.

Olası Şüpheli Durumlar:

  Bir kullanıcı zararlı bir URL'ye erişmeye çalıştığında bu sorgu, DNS logları ve tehdit istihbaratını karşılaştırarak zararlı URL'lere erişim girişimlerini tespit eder.
  Zararlı URL'ler, phishing siteleri veya kötü amaçlı yazılım barındıran siteler olabilir.

Uyarı Tetikleme:

Bu KQL sorgusu, Azure Sentinel üzerinde zararlı URL'lere erişim girişimlerini izlemek için kullanılabilir. Sentinel üzerinde bir analitik kural olarak yapılandırıldığında, kullanıcıların kötü amaçlı URL'lere erişim girişiminde bulunduğu durumlarda uyarı tetiklenir. Bu, güvenlik ekiplerine olası tehditler hakkında hızlı bilgi verir ve erken müdahale imkanı sağlar.

# 12. Unusual Outbound Traffic (Olağandışı Çıkış Trafiği)

  Açıklama: Ağı terk eden alışılmadık miktarda veri tespit edildiğinde uyarı verir.
  Yapılandırma: Ağ trafiği logları incelenir, olağan dışı yüksek miktarda veri çıkışı olduğunda tetiklenir.

KQL Sorgusu
```
CommonSecurityLog
| where DeviceAction == "Allow"  // Trafiğin dışarı çıkmasına izin verilen olayları filtreler
| where TimeGenerated >= ago(1h)  // Son 1 saat içindeki trafiği izler
| where DestinationIPType == "Public"  // Yalnızca dış IP'lere (kamuya açık IP'ler) yapılan trafiği izler
| summarize OutboundTraffic = sum(TotalBytesSent) by SourceIP, DestinationIP, DestinationPort
| where OutboundTraffic > 100000000  // 1 saat içinde 100MB'den fazla veri çıkışı varsa
| project TimeGenerated, SourceIP, DestinationIP, DestinationPort, OutboundTraffic
```
Sorgu Açıklaması:

  CommonSecurityLog: Genellikle güvenlik cihazları (firewall, proxy, vb.) tarafından oluşturulan ağ trafiği loglarını içerir.
  DeviceAction == "Allow": Dışarıya doğru veri çıkışına izin verilen trafiği filtreler (izin verilen olaylar).
  TimeGenerated >= ago(1h): Son 1 saat içinde gerçekleşen trafik olaylarını getirir.
  DestinationIPType == "Public": Yalnızca dış IP adreslerine yönelik trafiği (public IP) izler, yerel ağdaki trafiği hariç tutar.
  summarize OutboundTraffic = sum(TotalBytesSent) by SourceIP, DestinationIP, DestinationPort: Kaynak IP adresi, hedef IP adresi ve hedef port ile gruplandırarak, gönderilen toplam veri miktarını hesaplar.
  where OutboundTraffic > 100000000: 1 saat içinde 100 MB'den fazla veri çıkışı olduğunda bu trafiği filtreler. Bu eşik değeri ihtiyaca göre ayarlanabilir.
  project: İlgili bilgileri (zaman, kaynak IP, hedef IP, hedef port, veri miktarı) gösterir.

Kullanım Senaryosu:

  SourceIP: Verinin çıktığı kaynağın IP adresi (içeriden dışarıya doğru olan trafik).
  DestinationIP: Trafiğin yönlendirildiği hedef IP adresi (kamuya açık IP).
  DestinationPort: Trafiğin gittiği hedef port numarası.
  OutboundTraffic: Gönderilen toplam veri miktarı (bayt cinsinden).

Olası Şüpheli Durumlar:

  Kısa sürede olağandışı miktarda veri dışarıya gönderiliyorsa, bu bir veri sızdırma girişimi veya kötü amaçlı yazılımın büyük miktarda veri çıkışı yaptığı anlamına gelebilir.
  Verinin kamuya açık bir IP adresine gitmesi, yetkisiz veri aktarımı anlamına gelebilir.

Uyarı Tetikleme:

Bu KQL sorgusu, Azure Sentinel üzerinde olağandışı çıkış trafiğini izlemek için kullanılabilir. Bir kaynaktan dışarıya kısa süre içinde yüksek miktarda veri çıktığında bu sorgu tetiklenir ve Sentinel üzerinde bir uyarı oluşturur. Bu, güvenlik ekiplerine şüpheli bir duruma erken müdahale etme imkanı sağlar.


# 13. Impersonation of User Accounts (Kullanıcı Hesabı Taklidi)

  Açıklama: Bir kullanıcının kimliğini taklit eden giriş denemeleri tespit edildiğinde uyarı verir.
  Yapılandırma: Kullanıcı logon davranışlarını analiz ederek normal dışı etkinlikleri tespit eder.

KQL Sorgusu
```
let known_logins = 
SigninLogs
| where ResultType == "0"  // Başarılı giriş denemelerini filtreler
| summarize LastKnownLocation = arg_max(TimeGenerated, Location, IPAddress, DeviceDetail) by UserPrincipalName
| project UserPrincipalName, LastKnownLocation, IPAddress, DeviceDetail;

SigninLogs
| where ResultType == "0"  // Başarılı giriş denemelerini izler
| where TimeGenerated >= ago(1d)  // Son 24 saat içindeki giriş denemelerini alır
| join kind=leftanti (
    known_logins 
    ) on UserPrincipalName, IPAddress, DeviceDetail  // Bilinen IP ve cihazla eşleşmeyen giriş denemelerini bulur
| project TimeGenerated, UserPrincipalName, IPAddress, Location, DeviceDetail
```

Sorgu Açıklaması:

  let known_logins: Daha önce kullanıcı tarafından başarılı giriş yapılan bilinen IP adresleri, konumlar ve cihazları belirler.
  SigninLogs: Azure AD giriş loglarını içerir.
  ResultType == "0": Başarılı giriş denemelerini filtreler.
  arg_max: Kullanıcının en son giriş yaptığı yeri, IP adresini ve cihaz bilgilerini alır.

  join kind=leftanti: Kullanıcının bilinen logon davranışları (IP adresi, cihaz, konum) ile uyumsuz giriş denemelerini bulur. Eğer giriş denemesi daha önce hiç kullanılmayan bir IP adresi, cihaz veya bölgeden yapılıyorsa bu bir taklit girişim olabilir.

  project: İlgili bilgileri seçer:
        TimeGenerated: Giriş denemesinin gerçekleştiği zaman.
        UserPrincipalName: Giriş yapan kullanıcının kimliği.
        IPAddress: Girişin yapıldığı IP adresi.
        Location: Girişin yapıldığı coğrafi konum.
        DeviceDetail: Girişin yapıldığı cihaz bilgileri.

Kullanım Senaryosu:

  UserPrincipalName: Giriş yapan kullanıcının kimliği.
  IPAddress: Şüpheli girişin yapıldığı IP adresi.
  Location: Girişin yapıldığı coğrafi konum.
  DeviceDetail: Girişin yapıldığı cihazın ayrıntıları.

Olası Şüpheli Durumlar:

  Kullanıcı daha önce hiç kullanmadığı bir IP adresi, cihaz veya konumdan giriş yapıyorsa, bu bir kimlik taklit girişimi olabilir.
  Bir kullanıcının kimlik bilgileri ele geçirildiğinde, saldırgan farklı cihaz ve IP'ler kullanarak sisteme giriş yapmaya çalışabilir.

Uyarı Tetikleme:

Bu KQL sorgusu, Azure Sentinel üzerinde kullanıcı hesabı taklidini izlemek için kullanılır. Kullanıcının daha önce giriş yapmadığı IP adresleri, cihazlar veya konumlardan giriş denemesi yapıldığında bu sorgu çalışır ve uyarı oluşturur. Bu tür uyarılar, kullanıcı hesaplarının ele geçirilmesi girişimlerine karşı hızlı müdahale edilmesini sağlar.


# 14. Multiple File Deletions (Çoklu Dosya Silinmesi)

  Açıklama: Kısa sürede birden fazla dosyanın silinmesi durumunda uyarı verir.
  Yapılandırma: Windows Event Log ve dosya sistem loglarını izleyerek anormal dosya silme etkinliklerini izler.

KQL Sorgusu
```
SecurityEvent
| where EventID == 4663  // Dosya silme işlemlerini temsil eden Event ID
| where ObjectType == "File"  // Nesne türü dosya olan olayları filtreler
| where AccessMask has_any ("0x2", "DELETE")  // Silme izni verilen olaylar
| where TimeGenerated >= ago(1h)  // Son 1 saat içindeki olayları izler
| summarize FileDeletions = count() by SubjectUserName, FileName, Computer
| where FileDeletions > 10  // 1 saat içinde 10'dan fazla dosya silme işlemi
| project TimeGenerated, SubjectUserName, FileName, Computer, FileDeletions
```
Sorgu Açıklaması:

  SecurityEvent: Windows güvenlik olaylarını içeren tablo.
  EventID == 4663: Bu EventID, bir dosya veya nesneye erişim sağlandığını gösterir. Dosya silme işlemleri bu olayla kaydedilir.
  ObjectType == "File": Sadece dosya türünde olan nesnelere erişim sağlandığında sonuç döndürür.
  AccessMask has_any ("0x2", "DELETE"): Silme işlemi yapılmış dosyaları tespit eder (Windows'ta silme izinleri 0x2 ve DELETE olarak işaretlenir).
  TimeGenerated >= ago(1h): Son 1 saat içinde gerçekleşen dosya silme işlemlerini izler.
  summarize FileDeletions = count() by SubjectUserName, FileName, Computer: Kullanıcı, dosya adı ve bilgisayar bazında silme işlemlerini sayar.
  where FileDeletions > 10: 1 saat içinde 10’dan fazla dosya silinmişse bu durum şüpheli olarak değerlendirilir.
  project: İlgili sütunları seçer (silme işlemi zamanı, kullanıcı adı, silinen dosya adı, bilgisayar adı ve silme sayısı).

Kullanım Senaryosu:

  SubjectUserName: Dosya silme işlemini gerçekleştiren kullanıcının adı.
  FileName: Silinen dosyanın adı veya yolu.
  Computer: Silme işleminin yapıldığı bilgisayar.
  FileDeletions: Belirtilen zaman diliminde gerçekleştirilen dosya silme işlemlerinin sayısı.

Olası Şüpheli Durumlar:

  Bir kullanıcı kısa süre içinde çok sayıda dosya siliyorsa, bu bir ransomware saldırısının veya yetkisiz bir kullanıcının kritik verileri yok etmeye çalıştığının göstergesi olabilir.
  Çok sayıda dosyanın kısa sürede silinmesi, özellikle hassas veya sistem dosyalarıysa, acil müdahale gerektiren bir duruma işaret edebilir.

Uyarı Tetikleme:

Bu KQL sorgusu, Azure Sentinel üzerinde çoklu dosya silme etkinliklerini izlemek için kullanılır. Kısa bir süre içinde bir kullanıcı tarafından çok sayıda dosya silinmesi durumunda bu sorgu tetiklenir ve uyarı oluşturur. Bu, güvenlik ekiplerine olası veri ihlali veya kötü amaçlı etkinlikler hakkında hızlı bilgi verir ve müdahale etmelerini sağlar.

# 15. Azure Resource Deletion (Azure Kaynaklarının Silinmesi)

  Açıklama: Kritik bir Azure kaynağının silinmesi durumunda uyarı verir.
  Yapılandırma: Azure aktiviteleri üzerinden kaynakların silinmesi takip edilir.

KQL Sorgusu
```
AzureActivity
| where OperationNameValue == "Microsoft.Resources/subscriptions/resourceGroups/delete" or OperationNameValue contains "delete"  // Silme işlemlerini filtreler
| where ActivityStatusValue == "Succeeded"  // Başarılı silme işlemlerini izler
| where TimeGenerated >= ago(1h)  // Son 1 saat içinde gerçekleşen silme işlemlerini izler
| project TimeGenerated, Caller, ResourceGroup, ResourceId, OperationNameValue, ActivityStatusValue, SubscriptionId
```

Sorgu Açıklaması:

AzureActivity: Azure kaynaklarının oluşturulması, değiştirilmesi ve silinmesi gibi işlemleri izleyen tablo.
OperationNameValue == "Microsoft.Resources/subscriptions/resourceGroups/delete" or OperationNameValue contains "delete": Kaynak grubu veya bireysel Azure kaynaklarının silinmesini izler. "delete" anahtar kelimesiyle, silme işlemleri genel olarak filtrelenir.
ActivityStatusValue == "Succeeded": Başarıyla tamamlanmış silme işlemlerini filtreler. Böylece başarısız denemeleri dışarıda bırakır.
TimeGenerated >= ago(1h): Son 1 saat içinde gerçekleşen kaynak silme işlemlerini getirir.
project: İlgili bilgileri seçer:
TimeGenerated: Silme işleminin gerçekleştiği zaman.
Caller: Kaynağı silen kullanıcı veya hizmet hesabı.
ResourceGroup: Silinen kaynağın ait olduğu kaynak grubu.
ResourceId: Silinen kaynağın tam kimliği (ID).
OperationNameValue: Gerçekleştirilen işlemin adı (kaynak silme).
SubscriptionId: Silinen kaynağın bulunduğu abonelik ID’si.

Kullanım Senaryosu:

  Caller: Kaynak silme işlemini gerçekleştiren kişi ya da hizmet.
  ResourceGroup: Silinen kaynağın ait olduğu kaynak grubu.
  ResourceId: Silinen kaynağın tam kimliği.
  OperationNameValue: Gerçekleştirilen işlem (kaynak silme).
  ActivityStatusValue: Silme işleminin başarı durumu.

Olası Şüpheli Durumlar:

  Beklenmeyen bir zamanda kritik bir Azure kaynağı veya kaynak grubunun silinmesi, yetkisiz erişim veya hatalı yapılandırma göstergesi olabilir.
  Yanlışlıkla veya kötü niyetli bir şekilde önemli kaynakların silinmesi, iş sürekliliği açısından kritik bir risktir ve acil müdahale gerektirebilir.

Uyarı Tetikleme:

Bu KQL sorgusu, Azure Sentinel üzerinde Azure kaynaklarının silinme olaylarını izlemek için kullanılır. Kritik bir kaynak silindiğinde bu sorgu tetiklenir ve güvenlik ekiplerine bilgi sağlar. Bu sayede, özellikle yanlış yapılandırmalar, siber saldırılar veya insan hatası sonucu silinmiş olabilecek önemli kaynakların silinmesi durumunda hızlı müdahale edilmesi sağlanır.


# 16. Unusual Azure Resource Activity (Olağandışı Azure Kaynağı Etkinliği)

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
