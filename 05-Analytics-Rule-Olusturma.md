Azure Sentinel Portalı'nda Analytics sekmesine gidin.
+ Create butonuna tıklayın ve yeni bir kural oluşturun.
Kural adı ve tetikleyici koşullarını belirleyin.

    Log query ile ilgili verileri toplayın. Örneğin:

```
SecurityEvent
| where EventID == 4625
| summarize FailedAttempts = count() by TargetUserName
| where FailedAttempts > 10

```

  Bu örnekte, 4625 (başarısız oturum açma) olaylarını izleyip, 10’dan fazla başarısız deneme olduğunda kural tetiklenir.
  Kural sıklığını ve tetikleme koşullarını belirleyin (örneğin her 5 dakikada bir).
  Uyarı seviyesi ve otomasyon yanıtı ayarlarını yapılandırın (örneğin, playbook kullanarak otomatik eylem).

Bu kurallar, Sentinel’de tehditleri etkili bir şekilde algılamaya ve yanıt vermeye yardımcı olur.
