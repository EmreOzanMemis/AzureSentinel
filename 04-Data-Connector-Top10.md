Azure Sentinel'de Data Connector'lar, çeşitli kaynaklardan veri toplayarak tehdit algılama ve güvenlik analitiği için kullanılmasını sağlar. Veri kaynakları arasında Azure hizmetleri, üçüncü taraf güvenlik cihazları, bulut uygulamaları ve işletim sistemleri yer alır. Aşağıda Azure Sentinel için en çok kullanılan 10 data connector ve bu kaynakların nasıl bağlanacağını adım adım açıklıyorum:
1. Azure Active Directory (Azure AD)

    Açıklama: Azure AD, kimlik doğrulama ve yetkilendirme loglarını sağlar. Kullanıcı oturum açma etkinliklerini, başarısız giriş denemelerini ve hesap güvenliği olaylarını izler.
    Bağlantı Adımları:
        Azure Sentinel portalında Data Connectors bölümüne gidin.
        Azure Active Directory bağlantısını bulun ve Open Connector Page'e tıklayın.
        Enable butonuna tıklayarak bağlantıyı etkinleştirin.
        Azure AD logları otomatik olarak toplanmaya başlayacaktır.

2. Office 365

    Açıklama: Office 365, kullanıcı etkinlikleri, e-posta gönderim/alım logları ve SharePoint/OneDrive dosya işlemlerini izler.
    Bağlantı Adımları:
        Data Connectors bölümünden Office 365 bağlantısını seçin.
        Open Connector Page'e tıklayın ve gerekli izinleri verin.
        Mail, SharePoint ve OneDrive loglarını bağlamak için gereken kutucukları işaretleyin.
        Save butonuna basarak log toplamayı başlatın.

3. Microsoft Defender for Endpoint

    Açıklama: Endpoint cihazlarda tehdit algılama ve güvenlik olaylarını izler.
    Bağlantı Adımları:
        Data Connectors bölümünden Microsoft Defender for Endpoint seçeneğini bulun.
        Open Connector Page'e tıklayın ve gerekli izinleri sağlayın.
        Enable butonuna basarak log toplama işlemini başlatın.

4. Azure Activity

    Açıklama: Azure aboneliklerindeki etkinlikleri izler. Örneğin, kaynak oluşturma, silme, ve yapılandırma değişikliklerini takip eder.
    Bağlantı Adımları:
        Data Connectors bölümünden Azure Activity'yi seçin.
        Open Connector Page'e tıklayın ve logların toplanacağı Log Analytics çalışma alanını seçin.
        Enable butonuna tıklayın ve logların toplanmasını sağlayın.

5. Windows Security Events (via AMA)

    Açıklama: Windows sunucuları ve istemcileri üzerindeki güvenlik loglarını toplar. Özellikle başarısız giriş denemeleri ve yerel hesap etkinliklerini izler.
    Bağlantı Adımları:
        Data Connectors bölümünden Windows Security Events'i seçin.
        Install Agent on Windows talimatlarını takip ederek Azure Monitor Agent’ı sunucularınıza yükleyin.
        Configure Settings bölümünde hangi event ID’lerin izleneceğini seçin ve Save butonuna basın.

6. Azure Firewall

    Açıklama: Azure Firewall’dan gelen ağ trafiği loglarını ve tehdit algılamalarını izler.
    Bağlantı Adımları:
        Azure Firewall'u seçin ve Open Connector Page'e tıklayın.
        Log Analytics Workspace'inizi seçin ve Enable butonuna basarak log toplamayı etkinleştirin.

7. Azure Web Application Firewall (WAF)

    Açıklama: Web uygulamalarını koruyan Azure WAF loglarını toplar. SQL Injection ve XSS gibi tehditler hakkında bilgi verir.
    Bağlantı Adımları:
        Azure Web Application Firewall'u seçin.
        Azure WAF loglarının yönlendirileceği Log Analytics çalışma alanını seçin.
        Enable butonuna tıklayın.

8. Amazon Web Services (AWS) CloudTrail

    Açıklama: AWS CloudTrail logları ile AWS ortamındaki kullanıcı etkinliklerini ve güvenlik olaylarını izler.
    Bağlantı Adımları:
        Data Connectors bölümünden AWS CloudTrail seçeneğini seçin.
        Open Connector Page'e tıklayın ve gerekli AWS erişim bilgilerini girin.
        Gerekli izinleri verin ve Save butonuna tıklayarak bağlantıyı etkinleştirin.

9. Microsoft Defender for Cloud Apps

    Açıklama: Microsoft bulut uygulamalarındaki güvenlik tehditlerini izler. Özellikle bulut uygulamalarına yönelik anormal girişler ve etkinlikler hakkında bilgi verir.
    Bağlantı Adımları:
        Data Connectors bölümünden Microsoft Defender for Cloud Apps'i seçin.
        Open Connector Page'e tıklayın.
        Gerekli izinleri sağlayarak Enable butonuna basın.

10. Palo Alto Networks Firewall

    Açıklama: Palo Alto ağ güvenlik cihazlarından gelen trafiği, tehdit algılamalarını ve güvenlik olaylarını izler.
    Bağlantı Adımları:
        Data Connectors bölümünden Palo Alto Networks seçeneğini bulun.
        Open Connector Page'e tıklayın.
        Palo Alto cihazlarınızın loglarını Log Analytics Workspace'e yönlendirmek için gerekli yapılandırma talimatlarını izleyin.

Genel Bağlantı Adımları:

Azure Sentinel Portalına Giriş Yapın: Azure portalında Sentinel hizmetine gidin.
    Data Connectors Bölümüne Gidin: Azure Sentinel ana ekranında Data Connectors sekmesini açın.
    Bağlamak İstediğiniz Kaynağı Seçin: İlgili kaynağı bulun ve Open Connector Page'e tıklayın.
    Gerekli İzinleri Verin: Birçok bağlantı, Azure AD, Office 365 veya üçüncü parti entegrasyonlar için izinler gerektirir.
    Log Analytics Workspace'i Seçin: Logların toplanacağı Log Analytics çalışma alanını seçin.
    Bağlantıyı Etkinleştirin: Enable butonuna basarak veri toplamayı başlatın.

Her bir data connector, Sentinel'in tehdit algılama ve olay yönetimi yeteneklerini genişleterek farklı güvenlik kaynaklarından veri toplanmasını sağlar. Bu kaynakların doğru yapılandırılması, Sentinel’in tehditleri hızlı ve etkili bir şekilde tespit edebilmesi için kritik önemdedir.
