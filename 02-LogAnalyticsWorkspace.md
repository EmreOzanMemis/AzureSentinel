Log Analytics Workspace, Azure Monitor'ün bir parçası olan ve çeşitli kaynaklardan toplanan log verilerini depolamak, sorgulamak ve analiz etmek için kullanılan bir merkezi veri deposudur. Bu çalışma alanı, Azure Sentinel ile doğrudan entegre edilir ve Sentinel'in tüm verileri topladığı, analiz ettiği ve güvenlik olaylarını tespit ettiği temel platformdur.
Log Analytics Workspace Nedir?

Log Analytics Workspace, verilerin toplanması, saklanması ve analiz edilmesi için kullanılan bir platformdur. Temel olarak:

  Log Verileri: Azure, bulut hizmetleri, sanal makineler, güvenlik cihazları ve diğer kaynaklardan gelen veriler burada toplanır.
    Sorgulama: Veriler üzerinde sorgulama yapmak için Kusto Query Language (KQL) kullanılır.
    Analiz ve Raporlama: Toplanan loglar üzerinden analizler yapılarak çeşitli raporlar ve görselleştirmeler oluşturulur.

Log Analytics Workspace'in Kullanım Alanları

Log Analytics Workspace, bir dizi Azure hizmeti ve güvenlik aracıyla birlikte kullanılır. Aşağıda, çalışma alanının yaygın kullanım alanları listelenmiştir:

  Log Toplama ve Depolama: Farklı kaynaklardan gelen logları toplar ve saklar.
        Sanal makinelerden gelen güvenlik logları.
        Azure hizmetlerinden gelen performans ve tanılama verileri.
        Bulut tabanlı uygulamalardan gelen operasyonel veriler.

  Sorgulama ve Analiz: KQL ile veriler üzerinde derinlemesine analiz yapma imkanı sunar.
        Verilerin hızlıca sorgulanması ve anlamlı sonuçlara ulaşılması.
        Olaylar, hata raporları, güvenlik tehditleri gibi verilerin analiz edilmesi.

  Güvenlik ve Uygulama İzleme: Log verileri üzerinden güvenlik olaylarının ve uygulama performansının izlenmesi sağlanır.
        Uygulama performansı ve hataların izlenmesi.
        Güvenlik ihlallerinin ve tehditlerin tespit edilmesi.

  Raporlama ve Görselleştirme: Log verilerini görselleştirmek ve raporlamak için kullanılan araçlarla entegre olur.
        Power BI gibi araçlarla entegre edilerek raporlamalar yapılabilir.
        Azure portalı üzerinden özelleştirilmiş dashboard’lar oluşturulabilir.

Azure Sentinel İçin Log Analytics Workspace'in Önemi

Azure Sentinel, bir SIEM (Security Information and Event Management) çözümü olarak, güvenlik loglarını toplar, analiz eder ve tehditleri tespit eder. Log Analytics Workspace, Sentinel’in merkezi veri platformu olarak kritik bir rol oynar. İşte Sentinel için Log Analytics Workspace’in önemi:

# 1. Veri Toplama ve Depolama

Azure Sentinel, güvenlik verilerini ve logları Log Analytics Workspace'te toplar ve saklar. Aşağıdaki kaynaklardan gelen veriler burada toplanır:

  Azure AD, Office 365 ve diğer Microsoft hizmetlerinden gelen güvenlik olayları.
    Üçüncü taraf cihazlardan gelen veriler (örn. firewall logları, VPN logları).
    Kendi sistemlerinizdeki loglar (örn. Windows ve Linux sunucularından gelen loglar).

Bu verilerin merkezi olarak depolanması, Sentinel'in tüm güvenlik verilerini tek bir yerde tutmasını sağlar ve analiz süreçlerini kolaylaştırır.

# 2. KQL (Kusto Query Language) ile Güvenlik Analizi

Sentinel, Log Analytics Workspace üzerinde KQL kullanarak güvenlik tehditlerini ve olayları analiz eder.

  Kural ve Uyarı Oluşturma: KQL ile belirli tehdit türlerini tespit eden sorgular yazabilir ve bu sorgulara dayalı uyarılar (alerts) oluşturabilirsiniz. Örneğin, başarısız oturum açma girişimleri veya zararlı yazılım tespit edildiğinde uyarı veren kurallar.
    Tehdit Avı (Hunting): Tehdit avı sorguları, Log Analytics Workspace'teki veriler üzerinde KQL kullanarak potansiyel güvenlik tehditlerini proaktif olarak aramanıza olanak tanır.

# 3. Olay Yönetimi ve Soruşturma

Sentinel, güvenlik olayları oluşturduğunda bu olaylar, Log Analytics Workspace'teki veriler üzerinden analiz edilir.

  Olay Detayları: Log verileri üzerinde yapılan analizlerle olaylar hakkında detaylı bilgi sağlanır (örneğin, hangi kullanıcıdan veya hangi cihazdan kaynaklandığı).
    Soruşturma ve Yanıt: Sentinel, olayları daha iyi anlamak için Log Analytics Workspace’ten gelen verileri kullanır ve bu verilere dayanarak olayın yayılmasını engellemek için önlemler alınabilir.

# 4. Veri Saklama Süresi

Log Analytics Workspace, toplanan verilerin ne kadar süreyle saklanacağını yönetir. Bu, hem yasal zorunluluklar hem de güvenlik analizlerinin devamlılığı açısından önemlidir.

  Retention Period (Veri Saklama Süresi): Log verilerinin ne kadar süreyle saklanacağı Log Analytics Workspace üzerinde yapılandırılabilir. Varsayılan olarak, bu süre 30 gündür, ancak iş gereksinimlerine göre artırılabilir.

# 5. Maliyet Yönetimi

Log Analytics Workspace, veri toplama ve sorgulama için maliyetler yaratır. Bu nedenle:

  Maliyet Optimizasyonu: Hangi kaynaklardan log topladığınız ve ne kadar süreyle sakladığınız, Sentinel’in maliyet yönetimi açısından önemlidir. Gereksiz log kaynakları ve uzun veri saklama süreleri maliyetleri artırabilir.

# 6. Veri Kaynaklarının Bağlanması

Azure Sentinel’e bağladığınız tüm veri kaynakları, Log Analytics Workspace ile entegre edilir. Sentinel, bu kaynaklardan gelen logları otomatik olarak toplar ve analiz eder. Bu, güvenlik verilerinin merkezi bir noktada toplanmasını sağlar ve sistemdeki güvenlik olaylarının etkin bir şekilde izlenmesine yardımcı olur.



# Log Analytics Workspace, 

Azure Sentinel'in güvenlik verilerini topladığı, analiz ettiği ve depoladığı temel altyapıdır. Sentinel, güvenlik olaylarını tespit etmek, analiz etmek ve tehditlere karşı yanıt vermek için bu çalışma alanını kullanır. Bu yüzden Sentinel’i etkin bir şekilde kullanabilmek için Log Analytics Workspace'in doğru yapılandırılması, performansı ve maliyet optimizasyonu büyük önem taşır.

Log Analytics Workspace, Sentinel'in gücünü artıran merkezi bir bileşen olduğu için hem güvenlik ekipleri hem de yönetim açısından dikkatle izlenmeli ve optimize edilmelidir.
