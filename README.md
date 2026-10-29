Telco Müşteri Kaybı (Churn) Tahmini
[Ad Soyad] · Makine Öğrenmesi Projesi
Problem
Bir telekomünikasyon şirketinde müşterinin aboneliğini iptal edip etmeyeceğini (churn) tahmin eden ikili bir sınıflandırma modeli geliştirildi. Amaç, ayrılma riski yüksek müşterileri önceden bulup kampanya sunabilmektir. Ana başarı ölçütü churn sınıfı için F1 skorudur; recall ve ROC-AUC de raporlandı. Müşterilerin %73,5'i kaldığı için accuracy tek başına yanıltıcıdır.
Veri
Kaggle'daki Telco Customer Churn veri seti kullanıldı: 7.043 müşteri, 21 sütun (müşteri profili, aldığı hizmetler, sözleşme ve ödeme bilgileri), churn oranı %26,5. Veri kalitesi kontrolünde TotalCharges sütununda 11 gizli eksik bulundu (hepsi henüz ilk ayını doldurmamış müşteriler) ve 0 ile dolduruldu. customerID ve gender modelden çıkarıldı, kategorik değişkenler sayısallaştırıldı, service_count ve tenure_group özellikleri türetildi. Veri %70 / %15 / %15 (eğitim / doğrulama / test) olarak, sınıf oranı korunarak bölündü.
Model
Önce "hep ayrılmaz" diyen bir baseline kuruldu (F1 = 0). Ardından Lojistik Regresyon, Random Forest ve Gradient Boosting 5 parçalı çapraz doğrulama ile karşılaştırıldı. Random Forest eğitim verisini ezberlediği (train F1 0,995, doğrulama F1 0,545) için elendi. Gradient Boosting hiperparametre araması ile ayarlandı ve doğrulama setinde en iyi sonucu verdiği için final model seçildi (F1 0,647, recall 0,839).
Sonuç
Final model test setinde yalnızca bir kez değerlendirildi: accuracy 0,763, F1 0,635, recall 0,776, ROC-AUC 0,842. Ayrılan 281 müşterinin 218'i yakalandı, 63'ü kaçırıldı; yaklaşık 188 müşteriye yanlış alarm verildi. Skorlar doğrulama setine yakın olduğu için model yeni verilerde de benzer çalışıyor.
Çıkarım
Model en çok sözleşme türüne, müşterilik süresine ve fiber optik internet kullanımına bakıyor. Bu, veri analizi bulgularıyla tutarlıdır: aylık sözleşmelide churn %42,7, iki yıllıkta %2,8; fiber optik müşterilerde %41,9. Öneri: aylık sözleşmeli, yeni ve fiber optik müşterilere elde tutma kampanyası yapmak ve uzun dönemli sözleşmeye geçişi teşvik etmek.
Sınırlılıklar: Veri eğitim amaçlı örnek bir veridir ve tek bir şirketi temsil eder. Test setinde yalnızca 281 ayrılan müşteri olduğu için skorlar birkaç puan oynayabilir. Model, uzun süreli müşterilerin ayrılmasını yakalamakta zayıftır. Yanlış alarmın ucuz olduğu varsayımı gerçek maliyet verisiyle doğrulanmamıştır.
