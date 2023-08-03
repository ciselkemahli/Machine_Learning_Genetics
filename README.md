# Genombilimde Makine Öğrenmesi

(Bu eğitim Genombilim Eğitiminde, 2023 kullanılmak üzere hazırlanmıştır.)

## Makine Öğrenmesi Uygulaması

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

```ruby
library(caret)
library(mlbench)
library(randomForest)
```

###  1. Probleme hazırlık Pima Indians Diabetes

Bu veri kümesi aslen Ulusal Diyabet, Sindirim ve Böbrek Hastalıkları Enstitüsü’ndendir. Veri setinin amacı, veri setinde yer alan belirli teşhis ölçümlerine dayalı olarak bir hastanın diyabet hastası olup olmadığını teşhis amaçlı olarak tahmin etmektir. Özellikle, buradaki tüm hastalar en az 21 yaşında Pima Kızılderili mirasına sahip kadınlardır (Smith et al., 1988).

Pima Kızılderelileri

- Pregnancies: Hamile kalma sayısı
- Glucose: Oral glukoz tolerans testinde 2 saatlik plazma glukoz konsantrasyonu
- BloodPressure: Diyastolik kan basıncı (mm Hg)
- SkinThickness: Triceps cilt kıvrım kalınlığı (mm)
- Insulin: 2 saatlik serum insülini (mu U/ml)
- BMI: Vücut kitle indeksi (kg cinsinden ağırlık/(m cinsinden boy)^2)
- DiabetesPedigreeFunction: Diyabet soyağacı işlevi
- Age: Yaş (yıl)
- Outcome: Sınıf değişkeni (0 veya 1)

Veri setini yükleme

```ruby
data(PimaIndiansDiabetes)
```

### 2. Data özeti

Öncelikle datamızın içeriğini inceleyelim.

```ruby
head(PimaIndiansDiabetes)
summary(PimaIndiansDiabetes)
```

Bu çalışmada amacımız bu veri setindeki değişkenlikleri kullanarak kişinin diyabet hastalığı olup olmadığını tespit etmek ve bunu tahmin eden bir makina öğrenmesi algoritması tanımlamaktır.

Her bir özellik için boxplot oluşturalım.

```ruby
par(mfrow=c(2,4)) for(i in 1:8) {
boxplot(PimaIndiansDiabetes[,i], main=names(PimaIndiansDiabetes)[i]) }
```
Her bir özelliğin bulunma sıklığına bakabiliriz.

```ruby
par(mfrow=c(2,4)) for(i in 1:8) {
plot(density(PimaIndiansDiabetes[,i]), main=names(PimaIndiansDiabetes)[i]) }
```
corrplot kütüphanesini kullanarak özellikler arasındaki korelasyona bakabiliriz.

```ruby
#par(mfrow=c(1,1))
library(corrplot)
# Korelasyonun hesaplanması
correlations <- cor(PimaIndiansDiabetes[,1:8]) # Korelasyon grafiği oluşturma corrplot(correlations, method="circle")
```
### 3. Datayı hazırlama

- Bu veri setinde ‘NA’ ve 0 değerleri önceden temizlenmiş ve hazırdır.
- ‘Diabetes’ özelliği hariç hepsi sayısal girdiler olduğu için korelasyon, ve grafiksel görüntüleme için kullanılabilmektedir.
- Bu çalışmada amaç bu özellikleri bireyin diyabet olması ile ilişkisini bulmak ve yeni bir veri girdiğimizde kişinin diyabet olup olmadığını test etmektir.
- Verimizi test ve train olmak üzere ikiye böleceğiz. Test setini en iyi algoritmayı bulmak için kullanacağız. Train setini de bu algoritmaları tahmin edilebilirliğini test etmek için kullanacağız.

### 4. Algoritmaları değerlendirme

Makine öğrenmesi için bir çok farklı algoritma mevcuttur. Temel olarak sınıflandırma ve regresyon analizleri için bir çok farklı algoritma kullanılabilir.

Aralarından seçim yapabileceğiniz birçok olası değerlendirme ölçütü vardır. 

Sınıflandırma:
- Doğruluk (Accuracy): x doğru bölü y toplam örnek. Anlaşılması kolay ve yaygın olarak kullanılır.
- Kappa: sınıfların temel dağılımını hesaba katan doğruluk olarak kolayca anlaşılır. 

Regresyon:
- RMSE: Ortalama hatanın karekökü (root mean squared error). Yine, anlaşılması kolay ve yaygın olarak kullanılıyor.
- Rsquared: uyumun iyiliği veya belirleme katsayısı (the goodness of fit or coefficient of determination).

```ruby
#Eğitme planı hazırlamak
control <- trainControl(method="repeatedcv", number=10, repeats=3)
```

Aslında burada ne kadar çok tekrar yapabilirsek elde ettiğimiz değerlendirme ölçütlerimizi gerçeğe yakın ve doğruluğu yüksek bir şekilde elde edebiliriz.

Şimdi farklı modelleri deneyebiliriz.


## Referanslar

Smith, J.W., Everhart, J.E., Dickson, W.C., Knowler, W.C., & Johannes, R.S. (1988). Using the ADAP learning algorithm to forecast the onset of diabetes mellitus. In Proceedings of the Symposium on Computer Applications and Medical Care (pp. 261--265). IEEE Computer Society Press.
