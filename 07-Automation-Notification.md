Azure Sentinel'de önemli bildirimleri e-posta yoluyla almak için Playbook'lar oluşturup, bu Playbook'ları ilgili olaylara (Incidents) bağlayabilirsiniz. Bir Playbook, bir olay tetiklendiğinde otomatik olarak belirli eylemleri gerçekleştiren bir Logic App'dir. Bu adımlar, önemli olaylar veya uyarılarla ilgili size e-posta gönderecek bir yapılandırma yapmanızı sağlar.

Aşağıda, Sentinel'deki önemli olayları e-posta ile almak için gereken adımları detaylı olarak açıklıyorum:
1. Azure Sentinel Playbook (Logic App) Oluşturma
Adım 1: Playbook Oluşturma

    Azure Portal'a giriş yapın ve Azure Sentinel hizmetine gidin.
    Sol menüde Automation sekmesine tıklayın.
    Sağ üstteki Create butonuna basarak Playbook with Azure Logic Apps seçeneğini seçin.
    Yeni bir Playbook oluşturmak için:
        Playbook Adı: Playbook’a bir ad verin (örneğin, ImportantIncidentEmailAlert).
        Subscription: Doğru aboneliği seçin.
        Resource Group: Playbook'un atanacağı kaynak grubunu seçin.
        Location: Uygun bir bölge seçin.
    Review + Create butonuna tıklayarak Playbook’unuzu oluşturun.

Adım 2: Playbook İçin Logic App Yapılandırma

  Playbook oluşturduktan sonra Edit butonuna tıklayarak Logic App Designer’a gidin.
    Playbook’a bir tetikleyici ekleyin. Tetikleyici olayın başladığını algılar.
        "When a response to an Azure Sentinel alert is triggered" tetikleyicisini arayın ve seçin.
        Sentinel'de bir olay tetiklendiğinde bu Playbook devreye girer.

Adım 3: E-posta Gönderme Eylemi Ekleme

  New Step butonuna tıklayarak bir eylem ekleyin.
    Send an email (v2) işlemini arayın ve seçin. Bu, olay tetiklendiğinde belirttiğiniz e-posta adresine bir bildirim gönderir.
        Kime (To): E-posta bildirimlerinin gönderileceği adresi girin.
        Konu (Subject): E-posta konu başlığını belirleyin. Örneğin:

css:
```Alert: Azure Sentinel Olayı - @{body('AlertDisplayName')}```

Body (Gövde): E-posta içeriğine, olay hakkında detaylı bilgi ekleyebilirsiniz. Örneğin:

plaintext:
```
        Olay Adı: @{body('AlertDisplayName')}
        Şiddet: @{body('AlertSeverity')}
        Olay ID: @{body('IncidentNumber')}
        Olay Açıklaması: @{body('AlertDescription')}
```
  Save butonuna tıklayarak Playbook’u kaydedin.

2. Playbook'u Sentinel'deki Olaylarla Entegre Etme
Adım 1: Playbook'u Olaylara Bağlama

    Azure Sentinel ana sayfasına geri dönün ve sol menüde Incidents sekmesine tıklayın.
    Bir olay seçin ve üst menüde Run Playbook butonuna tıklayın.
    Açılan pencereden, oluşturduğunuz Playbook’u seçin. Olay tetiklendiğinde bu Playbook devreye girecek ve e-posta gönderecektir.

Adım 2: Otomatik Olay Yanıtı İçin Kural Ekleme

  Automation sekmesine tekrar dönün ve yeni bir Automation Rule oluşturun.
    Bu kural, belirlediğiniz olay kriterleri gerçekleştiğinde otomatik olarak Playbook'u devreye sokacaktır.
        Rule Name: Kuralınıza bir ad verin (örneğin, CriticalIncidentEmailAlert).
        Conditions: Önemli olayların hangi kriterlere göre otomatik yanıtlanacağını belirleyin (örneğin, Severity = High).
        Actions: Run Playbook seçeneğini seçin ve oluşturduğunuz Playbook'u seçin.
    Create butonuna tıklayarak kuralı kaydedin.

3. Playbook ve E-posta Bildirimlerinin Test Edilmesi

    Test Olayı: Sentinel’e manuel bir olay tetikleyebilir veya mevcut olayları test etmek için Playbook’u çalıştırabilirsiniz.
    E-posta Bildirimleri: Olaylar tetiklendiğinde, belirttiğiniz e-posta adresine olayın detaylarını içeren bir bildirim gönderildiğinden emin olun.


Azure Sentinel'de önemli bir olay tespit edildiğinde size e-posta bildirimleri gönderecek bir Playbook (Logic App) oluşturmanın detaylı adımlarını bir örnek üzerinden hazırlayalım. Bu örnek, kritik güvenlik olayları için nasıl e-posta bildirimleri alabileceğinizi gösterir.
Örnek Senaryo:

   Amacımız: Azure Sentinel’de kritik bir olay (örneğin, şiddeti "High" olan bir olay) tespit edildiğinde, sistem otomatik olarak bir e-posta gönderecek.
    Adımlar: Bir Playbook oluşturacağız, bu Playbook’u bir olay tetiklendiğinde devreye sokacağız ve e-posta ile bildirim alacağız.

Adım 1: Playbook (Logic App) Oluşturma

  Azure Portal’a giriş yapın ve Azure Sentinel hizmetine gidin.
    Sol menüde, Automation sekmesine tıklayın.
    Sağ üstteki Create butonuna basın ve Playbook with Azure Logic Apps seçeneğini seçin.

Playbook Tanımlaması:

  Playbook Adı: CriticalIncidentEmailAlert
    Subscription: Aboneliğinizi seçin.
    Resource Group: Yeni veya mevcut bir kaynak grubunu seçin (örneğin, SentinelAlertsGroup).
    Location: Uygun bir bölge seçin.
    Review + Create butonuna tıklayın ve Playbook’u oluşturun.

Adım 2: Logic App İçin Tetikleyici Ekleme

   Playbook oluşturulduktan sonra Edit butonuna tıklayarak Logic App Designer’ı açın.
    İlk adım olarak tetikleyici ekleyeceğiz:
        New Step butonuna tıklayın.
        Arama kutusuna "When a response to an Azure Sentinel alert is triggered" yazın ve bu tetikleyiciyi seçin.
        Sentinel üzerinden bir olay tetiklendiğinde bu Playbook otomatik olarak çalışacak.

Adım 3: E-posta Gönderme Eylemi Ekleme

  Tetikleyiciyi ekledikten sonra New Step butonuna tıklayarak bir eylem ekleyin.
    Arama kutusuna "Send an email (V2)" yazın ve Office 365 Outlook veya SMTP ile e-posta gönderme seçeneğini seçin.

E-posta İçeriği Tanımlama:

  To: Bildirimin gönderileceği e-posta adresini girin (örneğin, securityteam@example.com).
    Subject: E-posta başlığını şu şekilde tanımlayın:

    scss

```Alert: Azure Sentinel Olayı - @{triggerOutputs()?['body/AlertDisplayName']}```

Body (E-posta Gövdesi): E-postanın içeriğinde olayla ilgili bilgileri sağlayın:

plaintext
```
    Merhaba,

    Bir kritik güvenlik olayı tespit edildi. Detaylar aşağıdaki gibidir:

    - Olay Adı: @{triggerOutputs()?['body/AlertDisplayName']}
    - Olay Şiddeti: @{triggerOutputs()?['body/AlertSeverity']}
    - Olay ID: @{triggerOutputs()?['body/IncidentNumber']}
    - Olay Açıklaması: @{triggerOutputs()?['body/AlertDescription']}
    - Tetiklenen Zaman: @{triggerOutputs()?['body/AlertCreationTimeUtc']}

    Hemen müdahale etmeniz gerekebilir.

    Saygılar,
    Güvenlik Ekibi
```
    Save butonuna basarak Playbook’u kaydedin.

Adım 4: Playbook'u Sentinel Olaylarına Bağlama

   Azure Sentinel ana sayfasına geri dönün.
    Sol menüde Incidents (Olaylar) sekmesine tıklayın.
    Herhangi bir olayı seçin ve Run Playbook butonuna tıklayın.
    Açılan pencerede, oluşturduğunuz CriticalIncidentEmailAlert Playbook’u seçin ve çalıştırın.

Adım 5: Automation Rule Oluşturma (Otomatik Eylem Tanımlama)

Olayları manuel olarak bağlamak yerine, belirli olay türleri tetiklendiğinde bu Playbook'un otomatik olarak çalışmasını istiyorsanız bir Automation Rule oluşturabilirsiniz.

   Sol menüden Automation sekmesine gidin.
    Sağ üst köşedeki + Create butonuna basın ve bir otomasyon kuralı oluşturun.

Kural Yapılandırması:

   Name: Critical Incident Email Alert
    Description: Critical olaylar için otomatik e-posta bildirimi
    Conditions: Şiddeti "High" olan olaylar için bu kuralı çalıştıracağız. Bu nedenle:
        Severity: Equals seçeneğini seçin ve High değeriyle eşleştirin.
    Actions: Eylemler bölümünde Run Playbook seçeneğini seçin ve daha önce oluşturduğunuz CriticalIncidentEmailAlert Playbook'unu seçin.

  Create butonuna tıklayarak kuralı kaydedin.

Adım 6: Playbook ve E-posta Bildirimlerinin Test Edilmesi

   Test Olayı: Sentinel’e manuel bir olay tetikleyebilir veya mevcut olaylardan birini kullanarak test yapabilirsiniz.
    E-posta Alımı: Olay tetiklendiğinde, belirtilen e-posta adresine olay detaylarını içeren bir bildirim gönderilip gönderilmediğini kontrol edin.

