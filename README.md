# Telco Customer Churn Prediction

**Makine Öğrenmesi ile Müşteri Kaybı (Churn) Tahmini**

## Proje Hakkında

Bu projede, bir telekomünikasyon şirketinin müşterilerinin aboneliklerini iptal etme olasılığını tahmin etmek amacıyla ikili sınıflandırma modelleri geliştirilmiştir.

Projenin temel amacı, ayrılma riski yüksek müşterileri önceden belirleyerek şirketin müşteri kaybını azaltmaya yönelik kampanyalar geliştirmesine yardımcı olmaktır.

Müşterilerin %73,5'inin aboneliğini sürdürmesi nedeniyle, model değerlendirmesinde **F1-score** temel başarı ölçütü olarak kullanılmış; Recall ve ROC-AUC metrikleri de incelenmiştir. Bu yaklaşım, yalnızca Accuracy değerine odaklanarak yanıltıcı sonuçlara ulaşılmasını önlemeyi amaçlamaktadır.

## Veri Seti

Projede Kaggle üzerinde yayımlanan **Telco Customer Churn** veri seti kullanılmıştır.

- **Müşteri sayısı:** 7.043
- **Özellik sayısı:** 21 sütun
- **Churn oranı:** %26,5
- **Problem türü:** İkili sınıflandırma (Binary Classification)

Veri seti; müşteri profili, kullanılan hizmetler, sözleşme türleri, ödeme yöntemleri ve abonelik süresi gibi değişkenleri içermektedir.

### Veri Ön İşleme ve Özellik Mühendisliği

Modelleme öncesinde aşağıdaki işlemler gerçekleştirilmiştir:

- **Eksik değer analizi:** `TotalCharges` sütununda bulunan 11 gizli eksik değer tespit edilmiş ve 0 ile doldurulmuştur. Bu kayıtların henüz ilk ayını tamamlamamış müşterilere ait olduğu belirlenmiştir.
- **Gereksiz sütunların çıkarılması:** `customerID` ve `gender` sütunları model girdilerinden çıkarılmıştır.
- **Kategorik değişkenlerin dönüştürülmesi:** Kategorik özellikler sayısal değerlere dönüştürülmüştür.
- **Özellik mühendisliği:** Müşterilerin kullandığı hizmet sayısını temsil eden `service_count` ve abonelik süresini gruplandıran `tenure_group` özellikleri oluşturulmuştur.
- **Veri setinin bölünmesi:** Veri, sınıf oranları korunarak (stratified split) %70 eğitim, %15 doğrulama ve %15 test seti olacak şekilde ayrılmıştır.

## Kullanılan Modeller ve Yöntem

Model geliştirme sürecinde önce temel bir karşılaştırma noktası oluşturmak amacıyla tüm müşterilerin aboneliğini sürdüreceğini tahmin eden bir baseline model kurulmuştur. Bu modelin Churn sınıfı için F1-score değeri 0 olarak elde edilmiştir.

Ardından aşağıdaki modeller karşılaştırılmıştır:

1. **Logistic Regression**
2. **Random Forest**
3. **Gradient Boosting**

Modellerin performansları 5-fold Cross-Validation (5 parçalı çapraz doğrulama) ile değerlendirilmiştir.

Random Forest modeli eğitim verisinde yüksek başarı gösterirken doğrulama verisinde belirgin performans düşüşü sergilemiştir. Eğitim F1-score değeri 0,995, doğrulama F1-score değeri ise 0,545 olarak ölçülmüştür. Bu durum aşırı öğrenmeye (overfitting) işaret ettiğinden model final model olarak seçilmemiştir.

Gradient Boosting modeli üzerinde hiperparametre araması gerçekleştirilmiş ve doğrulama setindeki performansı nedeniyle final model olarak seçilmiştir.

## Model Performansı

### Final Model Sonuçları

Gradient Boosting modelinin test setindeki performansı aşağıdaki gibidir:

| Metrik | Test Sonucu |
|---|---:|
| Accuracy | 0,763 |
| F1-score | 0,635 |
| Recall | 0,776 |
| ROC-AUC | 0,842 |

Final model, model seçimi ve hiperparametre ayarlaması tamamlandıktan sonra test seti üzerinde yalnızca bir kez değerlendirilmiştir.

### Doğrulama Seti Sonuçları

| Metrik | Sonuç |
|---|---:|
| F1-score | 0,647 |
| Recall | 0,839 |

Test sonuçlarının doğrulama sonuçlarına yakın olması, iki veri bölümü arasında belirgin bir performans farkı bulunmadığını göstermektedir. Bununla birlikte, bu durum tek başına modelin tüm yeni verilerde aynı performansı göstereceğini garanti etmez.

### Churn Sınıfının Tespit Edilmesi

Test setinde:

- **218 müşteri:** Gerçekten ayrılan ve model tarafından doğru tespit edilen müşteriler
- **63 müşteri:** Ayrılan ancak model tarafından tespit edilemeyen müşteriler
- **Yaklaşık 188 müşteri:** Ayrılmayacak olmasına rağmen ayrılma riski yüksek tahmin edilen müşteriler

Bu sonuçlar, modelin ayrılan müşterilerin önemli bir bölümünü belirleyebildiğini; ancak hem kaçırılan müşteriler hem de yanlış alarmlar bulunduğunu göstermektedir.

## Önemli Bulgular

Veri analizi ve model sonuçları incelendiğinde müşteri kaybıyla ilişkili öne çıkan özellikler şunlardır:

- **Sözleşme türü:** Aylık sözleşmeli müşterilerde churn oranı %42,7 iken iki yıllık sözleşmeli müşterilerde %2,8'dir.
- **Fiber optik internet:** Fiber optik internet kullanan müşterilerde churn oranı %41,9 olarak gözlemlenmiştir.
- **Müşterilik süresi:** Abonelik süresi, müşterilerin ayrılma olasılığını değerlendirmede önemli özelliklerden biridir.

Modelin özellik önemleri ile veri analizinde gözlemlenen ilişkiler, müşteri kaybını anlamlandırmak için birlikte değerlendirilmiştir. Bu ilişkiler tek başına nedensellik kanıtı değildir.

## İşletme Açısından Öneriler

Elde edilen bulgular doğrultusunda aşağıdaki aksiyonlar önerilmektedir:

1. Aylık sözleşmeli müşteriler için müşteri elde tutma kampanyaları geliştirilmesi.
2. Yeni müşterilere yönelik ilk aylarda memnuniyet ve destek süreçlerinin güçlendirilmesi.
3. Fiber optik internet kullanan müşterilerin ihtiyaçlarının ve memnuniyet düzeylerinin ayrıca incelenmesi.
4. Uygun müşteriler için uzun dönemli sözleşme seçeneklerinin teşvik edilmesi.
5. Kampanya maliyetlerinin, yanlış alarmların ve önlenen müşteri kaybının birlikte değerlendirilmesi.

Bu öneriler, model tahminlerinin doğrudan uygulanması yerine işletme maliyetleri ve müşteri deneyimi dikkate alınarak değerlendirilmelidir.

## Sınırlılıklar

- Veri seti eğitim ve analiz amacıyla kullanılan örnek bir veri setidir; tek bir telekomünikasyon şirketinin müşteri yapısını temsil etmektedir.
- Test setindeki ayrılan müşteri sayısının 281 olması, performans ölçümlerinde örnekleme bağlı değişkenliğe neden olabilir.
- Modelin uzun süreli müşterilerin ayrılmasını tespit etme performansı zayıftır.
- Yanlış alarmın maliyetinin düşük olduğu varsayımı gerçek işletme verileriyle doğrulanmamıştır.
- Özellikler ile müşteri kaybı arasındaki ilişkiler nedensellik olarak yorumlanmamalıdır.

## Kullanılan Teknolojiler

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-learn
- Jupyter Notebook / Google Colab

## Projenin Amacı ve Öğrenme Çıktıları

Bu proje kapsamında veri ön işleme, keşifsel veri analizi (EDA), özellik mühendisliği, sınıflandırma modellerinin karşılaştırılması, Cross-Validation, hiperparametre optimizasyonu ve model değerlendirme metrikleri üzerinde çalışılmıştır.

Proje, makine öğrenmesi yöntemlerinin müşteri kaybı tahmini problemine nasıl uygulanabileceğini ve model sonuçlarının iş kararları açısından nasıl yorumlanabileceğini incelemektedir.

---

**Proje Türü:** Supervised Machine Learning · Binary Classification  
**Problem Alanı:** Customer Churn Prediction  
**Final Model:** Gradient Boosting
