Azure Sentinel, Microsoft’un bulut tabanlı güvenlik bilgi ve olay yönetimi (SIEM) ve güvenlik otomasyonu çözümüdür. Aşağıda adım adım Azure Sentinel kurulumu ve yapılandırma sürecini detaylı bir şekilde açıklıyorum:
Adım 1: Azure Portalına Giriş

  Azure Portalına (portal.azure.com) gidin ve giriş yapın.
  Giriş yaptıktan sonra, "All services" bölümüne tıklayın ve arama çubuğuna "Sentinel" yazarak Azure Sentinel hizmetini bulun.

  ![image](https://github.com/user-attachments/assets/3dba218d-9ddf-4c3b-847f-af79e5b52eac)

Adım 2: Log Analytics Workspace Oluşturma

Azure Sentinel, bir Log Analytics Workspace (Günlük Analitik Çalışma Alanı) üzerine kuruludur. Eğer zaten bir çalışma alanınız varsa, bu adımı atlayabilirsiniz.

  Azure Sentinel hizmetine tıkladıktan sonra, ekranın üstünde + Create (Oluştur) butonuna tıklayın.
  Eğer mevcut bir çalışma alanınız yoksa, yeni bir Log Analytics Workspace oluşturmanız istenir. Create a new workspace (Yeni çalışma alanı oluştur) seçeneğini seçin.
  
  ![image](https://github.com/user-attachments/assets/cf5cd577-815a-4cdb-8dc7-f6d52c9c7de3)

  Çalışma alanı için bir Resource Group (Kaynak Grubu) ve Workspace Name (Çalışma Alanı Adı) seçin.
  Bölgeyi (Region) belirleyin. Azure Sentinel’in bu verileri işlemesi için uygun bir bölge seçmek önemlidir.
  Gerekli alanları doldurduktan sonra Create butonuna tıklayın ve yeni çalışma alanınızın oluşturulmasını bekleyin.

  ![image](https://github.com/user-attachments/assets/967fb1da-ede5-4a48-91ce-5b763417e6d1)

  Adım 3: Azure Sentinel’i Log Analytics Workspace’e Ekleme

  Çalışma alanı oluşturulduktan sonra, Azure Sentinel sayfasına geri dönün.
  Log Analytics Workspace sayfasında oluşturduğunuz çalışma alanını seçin ve Add butonuna tıklayın.
  Azure Sentinel artık bu çalışma alanı ile entegre olacaktır.

  ![image](https://github.com/user-attachments/assets/fc7188a2-24e4-40b3-9f17-61e2229d4dcf)

 Sentinel'i ilk kez aktif ediyorsanız ilk 31 gün ücretsiz olarak kullanabilirsiniz. Deneme süresi boyunca, hem Microsoft Sentinel hem de Log Analytics için günde 10 GB'a kadar veri ücretsizdir. Günlük 10 GB'lık dahil edilen miktarın üzerindeki veri kullanımı faturalandırılacaktır.

  ![image](https://github.com/user-attachments/assets/19cb341e-7b3c-4f8d-8efb-bd694e9928d2)


Adım 4: Veri Kaynaklarını Bağlama

Azure Sentinel, çeşitli veri kaynaklarından bilgi toplayarak tehdit algılama ve güvenlik yönetimi sağlar. Bu veri kaynakları arasında Azure kaynakları, diğer bulut hizmetleri ve yerel sistemler olabilir.

  Azure Sentinel sayfasındaki Data connectors (Veri Bağlayıcıları) sekmesine gidin.

![image](https://github.com/user-attachments/assets/2c75f97b-7f78-42b4-8a71-d61f59c587f0)

  Microsoft Entra ID, Office 365, Windows Security Events gibi sık kullanılan veri kaynaklarını göreceksiniz. İstediğiniz kaynağı seçin ve yapılandırma talimatlarını takip edin.
      Örneğin, Azure Active Directory'yi seçip, Connect butonuna tıklayın ve gerekli izinleri vererek bu kaynağı Sentinel’e bağlayın.

![image](https://github.com/user-attachments/assets/752a5394-f4ae-44a9-b228-ebea0f21afe0)

Veri kaynaklarını Sentinele bağlarken yapılandırmayı unutmayın Örneğin Microsoft Entra ID connectorunu bağladığınızda 77 yüklenen içerik nesnesi olsada bunların 75 tanesi yapılandırma gerektirir. Bu içerikleri inceleyerek yapılandırmanzı önemlidir. 

![image](https://github.com/user-attachments/assets/1ae35e9a-fbdf-4187-bf9a-8be94c9b77e4)


Adım 5: Analitik Kurallarını Oluşturma

Veri kaynaklarını bağladıktan sonra, olayların ve tehditlerin algılanması için analitik kurallar oluşturabilirsiniz.

  Analytics (Analitik) sekmesine gidin.
  Create butonuna tıklayarak yeni bir kural oluşturun.

![image](https://github.com/user-attachments/assets/a76b4afc-e116-4c23-b446-a9e534b225b5)

  Kural adını, tetikleyici koşulları ve olayın ne zaman çalışacağını belirleyin. Örneğin, şüpheli bir IP adresinden gelen başarısız oturum açma girişimlerini izleyen bir kural oluşturabilirsiniz.
  Kural ayarlarını yaptıktan sonra Create butonuna tıklayarak kuralı aktif hale getirin.

Adım 6: Tehdit Avı (Hunting) ve Soruşturmalar

Azure Sentinel, tehdit avı ve olay araştırması yapmanıza olanak sağlar.

  Hunting sekmesine giderek önceden tanımlı sorgularla tehdit avına başlayabilirsiniz. Bu sorgular, sisteme dair tehditleri ve anormallikleri tanımlamak için kullanılır.
  Incidents (Olaylar) sekmesinden Sentinel’in oluşturduğu güvenlik olaylarını izleyebilir ve derinlemesine araştırma yapabilirsiniz.

![image](https://github.com/user-attachments/assets/85de10be-ef7e-4a1e-b28a-1875830b1667)

Adım 7: Otomasyon ve Olay Yanıtı

Azure Sentinel’de olaylara otomatik olarak yanıt vermek için Playbooks (Otomasyon Oynatma Kitapları) oluşturabilirsiniz. Bu kitaplar, bir olay gerçekleştiğinde otomatik olarak belirlenen adımları takip eder.

  Automation sekmesine gidin ve Create a new playbook seçeneğine tıklayın.
  Logic App adı altında bir çalışma akışı oluşturun ve olaylar gerçekleştiğinde belirli aksiyonların alınmasını sağlayın (örneğin, bir uyarı e-posta ile bildirilir veya şüpheli IP adresi engellenir).

  ![image](https://github.com/user-attachments/assets/4c3a271c-df89-4184-9ed6-9e6e2b168ee5)


Adım 8: İzleme ve Raporlama

  Workbooks (Çalışma Kitapları) sekmesi altında, güvenlik olayları ve tehditler hakkında görselleştirilmiş raporlar oluşturabilirsiniz.
  Önceden yapılandırılmış çalışma kitaplarını kullanarak güvenlik olaylarının raporlarını görebilir ve daha fazla içgörü elde edebilirsiniz.

  ![image](https://github.com/user-attachments/assets/0f81b615-7720-4eee-b52e-1af15679ca3e)


Adım 9: Maliyet Yönetimi

Azure Sentinel kullanımına bağlı olarak maliyetler ortaya çıkabilir. Cost Management sekmesinden Sentinel’in kullanımını izleyebilir ve maliyetleri optimize edebilirsiniz.

Azure Sentinel’i kurduktan sonra düzenli olarak veri kaynaklarını kontrol etmek ve analiz etmek, güvenlik tehditlerine hızlı yanıt vermek için önemlidir.

