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

```R
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

```R
data(PimaIndiansDiabetes)
```

### 2. Data özeti

Öncelikle datamızın içeriğini inceleyelim.

```R
head(PimaIndiansDiabetes)
summary(PimaIndiansDiabetes)
```

Bu çalışmada amacımız bu veri setindeki değişkenlikleri kullanarak kişinin diyabet hastalığı olup olmadığını tespit etmek ve bunu tahmin eden bir makina öğrenmesi algoritması tanımlamaktır.

Her bir özellik için boxplot oluşturalım.

```R
par(mfrow=c(2,4)) for(i in 1:8) {
boxplot(PimaIndiansDiabetes[,i], main=names(PimaIndiansDiabetes)[i]) }
```
Her bir özelliğin bulunma sıklığına bakabiliriz.

```R
par(mfrow=c(2,4)) for(i in 1:8) {
plot(density(PimaIndiansDiabetes[,i]), main=names(PimaIndiansDiabetes)[i]) }
```
corrplot kütüphanesini kullanarak özellikler arasındaki korelasyona bakabiliriz.

```R
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

```R
#Eğitme planı hazırlamak
control <- trainControl(method="repeatedcv", number=10, repeats=3)
```

Aslında burada ne kadar çok tekrar yapabilirsek elde ettiğimiz değerlendirme ölçütlerimizi gerçeğe yakın ve doğruluğu yüksek bir şekilde elde edebiliriz.

Şimdi farklı modelleri deneyebiliriz.

```R
# SVM
set.seed(7)
fit.svm <- train(diabetes~., data=PimaIndiansDiabetes, method="svmRadial", metric="Accuracy", trControl=control)
print(fit.svm)

# LDA
set.seed(7)
fit.lda <- train(diabetes~., data=PimaIndiansDiabetes, method="lda", metric="Accuracy", preProc=c("center", "scale"), trControl=control)
print(fit.lda)

# Random Forest
set.seed(7)
fit.rf <- train(diabetes~., data=PimaIndiansDiabetes, method="rf", metric="Accuracy", trControl=control)
print(fit.rf)

# Naive Bayes
set.seed(7)
fit.nb <- train(diabetes~., data=PimaIndiansDiabetes, method="nb", metric="Accuracy", trControl=control)
print(fit.nb)

# Logistic Regression
set.seed(7)
fit.glm <- train(diabetes~., data=PimaIndiansDiabetes, method="glm", metric="Accuracy", preProc=c("center", "scale"), trControl=control)
print(fit.glm)

# Tekrar örnekleminin toparlanması
results <- resamples(list(LDA=fit.lda, SVM=fit.svm, NB=fit.nb, RF=fit.rf, GLM=fit.glm))
# Farklı modların özeti
summary(results)

# Farklı modellerin karşılaştırması için box ve whisker grafikleri oluşturma
scales <- list(x=list(relation="free"), y=list(relation="free")) bwplot(results, scales=scales)

# Doğruluğun nokta grafik halinde gösterimi
scales <- list(x=list(relation="free"), y=list(relation="free")) dotplot(results, scales=scales)

# İkili tahminleri dağılım grafikleri halinde gösterimi
splom(results)

# Modellerin karşılıklı olarak gösterimi
xyplot(results, models=c("LDA", "SVM"))
xyplot(results, models=c("RF", "GLM"))
```

Bu aşamada belli algoritmaların veri setimiz için öğrenme işlemini yapmasını sağladık. Farklı algoritmalarda oluşan tahminlerin kontrolünü yapabiliriz.

```R
# Model tahminleri arasındaki farklar
diffs <- diff(results)

# İkili karşılaştırmaların p değerlerinin özeti
summary(diffs)

# LDA algoritmasının kontrolü
predictions_lda <- predict(fit.lda, PimaIndiansDiabetes[,1:8])
confusionMatrix(predictions_lda, PimaIndiansDiabetes$diabetes)

# SVM algoritmasının kontrolü
predictions_svm <- predict(fit.svm, PimaIndiansDiabetes[,1:8])
confusionMatrix(predictions_svm, PimaIndiansDiabetes$diabetes)

# NB algoritmasının kontrolü
predictions_nb <- predict(fit.nb, PimaIndiansDiabetes[,1:8])
confusionMatrix(predictions_nb, PimaIndiansDiabetes$diabetes)

# RF algoritmasının kontrolü
predictions_rf <- predict(fit.rf, PimaIndiansDiabetes[,1:8])
confusionMatrix(predictions_rf, PimaIndiansDiabetes$diabetes)

# GLM algoritmasının kontrolü
predictions_glm <- predict(fit.glm, PimaIndiansDiabetes[,1:8])
confusionMatrix(predictions_glm, PimaIndiansDiabetes$diabetes)
```

### 5. Doğruluğu (Accuracy) geliştirme
   
Elde ettiğimiz sonuçlara bakılırsa en iyi doğruluğu veren Rastgele Orman (RF) algoritması ile eğitimimizi geliştirmeye devam edebiliriz.

Bir algoritmayı ayarlarken, parametrelerin oluşturduğunuz model üzerindeki etkisini bilmek için algoritmanızı iyi anlamanız önemlidir.

Rastgele orman modelimiz üzerinde aşağıdaki etkileri olan mtry ve ntree parametreleri olmak üzere iki parametreyi ayarlamaya bağlı kalacağız. Başka pek çok parametre var, ancak bu iki parametre belki de nihai doğruluğunuz üzerinde en büyük etkiye sahip olanlardır.

R’deki randomForest() işlevi için doğrudan yardım sayfasından:

mtry: Her bölmede aday olarak rastgele örneklenen değişken sayısı. ntree: Büyüyecek ağaç sayısı.

```R
# Veri seti test ve train olmak üzere rastgele doğrulama kümelerine böleceğiz.
set.seed(7)
split <- createDataPartition(y=PimaIndiansDiabetes$diabetes, p=0.66, list=FALSE)
trainPima <- PimaIndiansDiabetes[split,] testPima <- PimaIndiansDiabetes[-split,]

# mtry değerini 2 ile 5 arasında deneyeceğiz.
set.seed(7)
metric <- "Accuracy"
# tunegrid <- expand.grid(.mtry=c(sqrt(ncol(trainPima)))) tunegrid <- expand.grid(.mtry=c(2:5))
rf_gridsearch <- train(diabetes~., data=trainPima, method="rf", metric=metric, tuneGrid=tunegrid, trControl=control)
bestmtry = rf_gridsearch$bestTune

print(rf_gridsearch)

plot(rf_gridsearch)

print(bestmtry$mtry)

# mtry değerini belirledik. ntree değerini de 1000, 1500, 2000, 2500 değerleri için deneyeceğiz.
tunegrid <- expand.grid(.mtry=bestmtry$mtry)
modellist <- list()
for (ntree in c(1000, 1500, 2000, 2500)) {
set.seed(7)
fit <- train(diabetes~., data=trainPima, method="rf", metric=metric,
tuneGrid=tunegrid, trControl=control, ntree=ntree) key <- toString(ntree)
modellist[[key]] <- fit
}

# Sonuçları karşılaştırma
results <- resamples(modellist) summary(results)

dotplot(results)
```

### 6. Modeli sonuçlandırma
   
Rastgele orman algoritmasını kullanarak en iyi parametreleri belirledik.

```R
# En iyi parametreler:
chosenNTree <- 2500 chosenmtry <- bestmtry$mtry

rf_pima <- randomForest(trainPima[,1:7], y=trainPima$diabetes, data=trainPima, proximity=TRUE, ntree=chosenNTree, nodesize=5, importance=TRUE, mtry=chosenmtry)

print(rf_pima)
```

Ortalama azalma doğruluğu (Mean decrease accuracy), her parametrenin olmadan modelin performansının ölçüsüdür. Daha yüksek bir değer, grubu (diyabetik ve sağlıklı) tahmin etmede o parametrenin önemini gösterir. Bu parametrenin çıkarılması, modelin tahmindeki doğruluğunu kaybetmesine neden olur.

```R
importance(rf_pima, type=1)
```

Görüldüğü üzere glikoz değeri diyabet hastası olma durumunun sınıflandırılmasında en yüksek öneme sahip parametre olarak bulunmuştur, ki bu da bekleyebildiğimiz bir sonuç olacaktır.

```R
predictions <- predict(rf_pima, newdata = testPima[,1:8])
confusionMatrix(predictions, testPima$diabetes)

table(predictions, testPima$diabetes)
```

Bu analizler örneklem, deneme sayılarının artırılması ile daha güvenilir sonuçlar verecektir. Bunun için yüksek işlemci gücüne sahip TRUBA gibi serverlar kullanılarak makine öğrenmesi analizleri gerçekleştirilebilir.

Sonuç olarak modelin kullanmadığı bir test veri seti verdiğimizde en iyi parametreleri seçerek oluşturduğumuz modelimiz bize bireyleri diyabet olma durumlarını söyleyecektir.

Kendi çalışmalarınızda kullanacağınız verilerinize, sorularınıza göre algoritmalar, kullandığınız parametreler değişecektir. En iyisini bulmak ve en iyi makine öğrenme yöntemini belirlemek için denemeler yapmak en önemli aşamadır.

Umarım bu çalışma size genomik vb. çalışmalarda makine öğrenmesi analizlerinin uygulanabilir olduğunu göstermiştir. Korkmayın, bir sürü hata alsanız da denemeye devam edin.

Bana dilediğiniz zaman mckemahli@gmail.com mail adresinden ulaşabilirsiniz. Kodlarınızla kalın :)


## Referanslar

Smith, J.W., Everhart, J.E., Dickson, W.C., Knowler, W.C., & Johannes, R.S. (1988). Using the ADAP learning algorithm to forecast the onset of diabetes mellitus. In Proceedings of the Symposium on Computer Applications and Medical Care (pp. 261--265). IEEE Computer Society Press.
