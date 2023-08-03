# Genombilimde Makine Öğrenmesi

(Bu eğitim Genombilim Eğitiminde, 2023 kullanılmak üzere hazırlanmıştır.)

# Makine Öğrenmesi Uygulaması

1. Probleme hazırlık

a) Kütüphaneleri yükleme

b) Veriseti yükleme

c) Verisetini doğrulama kümelerine bölme

3. Data özeti

a) Tanımlayıcı istatistiklere bakma

b) Data görüntüleme

4. Datayı hazırlama

a) Data temizliği

b) Özellik seçimi

c) Data dönüştürme

5. Algoritmaları değerlendirme

a) Test denemeleri değerlendirme metrikleri 

b) Algoritma kontrolü

c) Algoritmaları karşılaştırma

6. Doğruluğu (Accuracy) geliştirme

a) Algoritmayı düzenleme

b) Toparlama

7. Modeli sonuçlandırma

a) Doğrulama veri setiyle ilgili tahminler

b) Eğitim veri setinin tamamında bağımsız model oluşturun 

c) Sonraki kullanımlar için modeli kaydetme

Biz bu çalışmadan genomik çalışmalarda genellikle tercih edilen sınıflandırma algoritmalarını test edeceğiz. Kullanacağımız veri seti de bu amaç için uygundur.

R programlama dili kullanılarak makine öğrenmesi uygulamak mümkündür. Bunu uygulamak için caret, mlbench, randomforest gibi bir çok kütüphane bulunmaktadır.


Kullanacağımız R kütüphanelerini öncelikle yüklememiz gerekmektedir.

```
library(caret)
library(mlbench)
library(randomForest)
```

#  1. Probleme hazırlık Pima Indians Diabetes

Bu veri kümesi aslen Ulusal Diyabet, Sindirim ve Böbrek Hastalıkları Enstitüsü’ndendir. Veri setinin amacı, veri setinde yer alan belirli teşhis ölçümlerine dayalı olarak bir hastanın diyabet hastası olup olmadığını teşhis amaçlı olarak tahmin etmektir. Özellikle, buradaki tüm hastalar en az 21 yaşında Pima Kızılderili mirasına sahip kadınlardır (Smith et al., 1988).

Pima Kızılderelileri
• Pregnancies: Hamile kalma sayısı
• Glucose: Oral glukoz tolerans testinde 2 saatlik plazma glukoz konsantrasyonu
• BloodPressure: Diyastolik kan basıncı (mm Hg)
• SkinThickness: Triceps cilt kıvrım kalınlığı (mm)
• Insulin: 2 saatlik serum insülini (mu U/ml)
• BMI: Vücut kitle indeksi (kg cinsinden ağırlık/(m cinsinden boy)^2)
• DiabetesPedigreeFunction: Diyabet soyağacı işlevi
• Age: Yaş (yıl)
• Outcome: Sınıf değişkeni (0 veya 1)



# Referanslar

Smith, J.W., Everhart, J.E., Dickson, W.C., Knowler, W.C., & Johannes, R.S. (1988). Using the ADAP learning algorithm to forecast the onset of diabetes mellitus. In Proceedings of the Symposium on Computer Applications and Medical Care (pp. 261--265). IEEE Computer Society Press.
