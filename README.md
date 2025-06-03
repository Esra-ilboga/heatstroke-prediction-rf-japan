# 🌡️ Heatstroke Risk Prediction Using Random Forest  
**Japonya Ambulans Verisi Üzerine Çok Sınıflı Sıcak Çarpması Tahmini**

## 📌 Proje Tanımı

Bu proje, Japonya'dan alınan ısıya bağlı sıcak çarpması ambulans çağrısı verilerini (HSAD) kullanarak, farklı sıcaklık koşullarında ısı çarpması vakalarının sınıflandırılmasını hedeflemektedir. Bu kapsamda, özellikle yaz aylarında artan sağlık risklerinin tahmini için **Random Forest**, **Gradient Boosting** ve **K-Nearest Neighbors (KNN)** algoritmaları uygulanmış ve karşılaştırmalı analiz yapılmıştır.

---

## 📁 Veri Kümesi Hakkında Bilgi

**Kullanılan Veri Seti:** `HSAD.csv`  
**Makale:** "Datasets for quantifying association between short-term exposure to maximum temperature and heatstroke-related ambulance dispatches in Japan: A time-stratified case-crossover design"  
**Yazar:** Keita Wagatsuma  
**Yayın Platformu:** Data in Brief  
**Makale Linki:** [ScienceDirect](https://www.sciencedirect.com/science/article/pii/S2352340925000393)

---

### 📊 Genel Bilgiler

| Özellik                | Açıklama                                                      |
|------------------------|--------------------------------------------------------------|
| **Dönem**              | 2010 - 2019                                                  |
| **Ülke**               | Japonya                                                      |
| **Toplam Gözlem Sayısı** | 28.200 satır                                                |
| **Sayısal Özellikler** | Prefecture, HSAD, Tempmax, Rhumave, Dow, Holiday, Month, Day, Year |
| **Kategorik Özellikler** | Date, Prefecturename                                        |
| **Hedef Değişken (Target)** | HSAD (Ambulans çağrısı oranı veya sayısı)                  |

---

### 🔍 Özellikler ve Açıklamaları

| Özellik          | Açıklama                                                         |
|------------------|------------------------------------------------------------------|
| **Date**         | Olayın gerçekleştiği tarih (orijinalde bulunan, ancak silinmiş) |
| **Prefecture**    | Japonya’daki bölge ID’si (örn. Hokkaido=1, Kanagawa=14, Hiroshima=34) |
| **Prefecturename**| Bölge adı (Label Encoding uygulanmış)                           |
| **HSAD**         | Ambulans çağrısı oranı veya sayısı (0 ile 1 arasında)            |
| **Year**         | Tarihin yıl bilgisi                                              |
| **Month**        | Tarihin ay bilgisi                                              |
| **Day**          | Tarihin gün bilgisi                                             |
| **Dow**          | Haftanın günü (Sayısal, Pazar=1, Cumartesi=7)                   |
| **Holiday**      | Tatil durumu (0 = Tatil değil, 1 = Tatil)                       |
| **Tempmax**      | Günün en yüksek sıcaklık değeri                                 |
| **Rhumave**      | Günlük ortalama nem değeri                                      |

---

---

## ⚙️ Veri Ön İşleme

- `Date`, `Prefecture`, `Prefecturename` sütunları temizlenmiştir.
- `HSAD` oranına göre 5 sınıfa ayrılmıştır:  
  **0** – Yok, **1** – Düşük, **2** – Orta, **3** – Yüksek, **4** – Çok Yüksek
- Sayısal sütunlara **Min-Max normalization** uygulanmıştır.
- Sınıf dengesizliği **SMOTE** yöntemiyle giderilmiştir.
- Korelasyon, dağılım ve boxplot analizleri yapılmıştır.

---

### Scatter Plot Grafikleri

| ![HSAD vs Tempmax](https://github.com/user-attachments/assets/e3830def-998e-49da-a69d-ba82a2899047) | ![HSAD vs Rhumave](https://github.com/user-attachments/assets/369a8ddf-a5ad-4174-9f95-a5184bb62905) |
|:--------------------------------------------------------------------------------------------------:|:---------------------------------------------------------------------------------------------------:|
| ![HSAD vs Holiday](https://github.com/user-attachments/assets/bcb6c69f-acbe-4068-8802-1de4c7e029ee) | ![HSAD vs Month](https://github.com/user-attachments/assets/a1472c2a-54b8-4e1b-8370-e3cdef148aba) |
| ![HSAD vs Year](https://github.com/user-attachments/assets/df2977e2-e5b2-464d-ae46-897f8f5c0043) | ![HSAD vs Day](https://github.com/user-attachments/assets/29ebeb28-c6bd-41a4-9b64-b436dd929a72) |
| ![HSAD vs Dow](https://github.com/user-attachments/assets/275e98e3-0ed1-4ac1-bdc3-156af68ad1eb) | ![HSAD vs Prefecturename_encoded](https://github.com/user-attachments/assets/c0bcf4f6-a70f-445f-ab00-87496284b413) |


---

**Açıklamalar:**

- **HSAD ile maksimum sıcaklık (Tempmax)** arasında doğrusal olmayan fakat güçlü bir pozitif ilişki gözlenmiştir. Özellikle **30°C üzerindeki sıcaklıklarda** çağrılarda belirgin artışlar meydana gelmektedir.  
- **Ortalama nem (Rhumave)** ile HSAD arasında zayıf bir pozitif ilişki vardır. Ancak bu etkinin sıcaklıkla birlikte değerlendirildiğinde daha anlamlı olabileceği düşünülmektedir.  
- **Tatil olmayan günlerde (Holiday = 0)** ambulans çağrısı sayısı daha fazladır. Bu durum, iş ortamı ve dış mekân maruziyetinin etkisini yansıtabilir.  
- **Ay değişkeni (Month)**, mevsimsellik etkisini göstermektedir. **Temmuz ve Ağustos** aylarında ambulans çağrılarında ciddi artışlar gözlemlenmiştir.  
- **Yıllar (Year)** arasında farklılıklar belirgindir; özellikle **2018 ve 2019** yıllarında çağrılar artmıştır. Bu durum iklim değişikliği ve toplumsal farkındalıkla ilişkili olabilir.  
- **Ayın günleri (Day)** incelendiğinde belirgin bir farklılık gözlenmemiştir; HSAD verileri günlere göre stabil görünmektedir.  
- **Haftanın günleri (Dow)** incelendiğinde **Pazartesi ve Salı** günlerinde çağrıların daha yoğun olduğu görülmektedir; bu, haftalık çalışma düzeniyle ilişkili olabilir.  
- **Prefecture adlarının sayısallaştırılmış hali (Prefecturename_encoded)**, bölgesel farklılıkları yansıtmaktadır. Coğrafi konumların HSAD üzerinde anlamlı etkileri olabilir.  

---

### Histogramlar

| |
|:--:|
| ![Histogram Tempmax](https://github.com/user-attachments/assets/34938487-cc6e-427d-9b70-7d1077cd5538) |
| ![Histogram Rhumave](https://github.com/user-attachments/assets/cfc3c9a3-a548-4f71-9a34-cb550bf7cfa5) |
| ![Histogram HSAD](https://github.com/user-attachments/assets/ae080994-52cf-4ba9-bed0-576c5e8f0f1f) |
---

**Açıklamalar:**
Histogramlar, veri setindeki sayısal değişkenlerin dağılımını görselleştirerek hangi değer aralıklarında yoğunlaştıklarını göstermektedir:

- **Tempmax** değişkeni yaklaşık normal dağılırken, sağa çarpık yapısı aşırı sıcaklık günlerini işaret etmektedir.  
- **Rhumave** verisi %40–%80 arasında yoğunlaşarak nemli iklim koşullarını yansıtır.  
- **HSAD** değişkeninde veriler büyük ölçüde 0 çevresinde toplanmış olup, veri setinde ciddi bir sınıf dengesizliği bulunduğunu göstermektedir.  

Bu dağılımlar, değişkenler arasındaki ölçek farklılıklarını da ortaya koymakta ve normalizasyon ihtiyacını desteklemektedir.

---

### Boxplot

![Boxplot](https://github.com/user-attachments/assets/aa12fd19-8423-4758-ba6f-a7c4c9a25678)


**Açıklamalar:**
Boxplot grafikleri, her bir sayısal değişkenin merkezi eğilimlerini, çeyrek değerlerini ve veri aralığını özetlerken; kutu dışında kalan noktalar potansiyel aykırı değerleri göstermektedir:

- **HSAD** ve **Tempmax** değişkenlerinde çok sayıda aykırı değer tespit edilmiş olup, bu noktalar özellikle sıcak dalgaları gibi olağandışı durumları temsil edebilir.
- **Rhumave** değişkeni ise daha dengeli ve simetrik bir dağılım sergilemektedir.

Aykırı değerlerin doğrudan temizlenmesi, kritik bilgilerin kaybına yol açabileceğinden, bu değerlerin dikkatle analiz edilip korunması model başarımı açısından stratejik bir yaklaşımdır.

---

## 🤖 Kullanılan Modeller

Bu projede üç farklı makine öğrenmesi algoritması uygulanmış ve performansları karşılaştırılmıştır. Her bir model, hiperparametre optimizasyonu ve uygun ön işleme adımlarıyla değerlendirilmiştir.

---

### 1. 🌲 Random Forest Classifier

**Açıklama:**  
Random Forest, çok sayıda karar ağacının topluluk öğrenmesi (ensemble learning) yaklaşımıyla bir araya getirilmesiyle oluşan güçlü bir sınıflandırma algoritmasıdır. Ağaçlar rastgele örnek ve özellik alt kümeleriyle eğitilir; sonuçlar oylama ile birleştirilir. Bu yaklaşım overfitting’i azaltır ve modelin genelleme başarımını artırır.

**Uygulanan Hiperparametreler (GridSearchCV ile seçildi):**
- `n_estimators = 300`: 300 adet karar ağacı oluşturulmuştur.
- `max_depth = 30`: Ağaçların maksimum derinliği sınırlanarak aşırı öğrenme önlenmiştir.
- `min_samples_split = 5`: Düğümlerin bölünebilmesi için minimum örnek sayısı.
- `min_samples_leaf = 2`: Her yaprakta bulunması gereken minimum örnek sayısı.
- `random_state = 42`: Sonuçların tekrar üretilebilirliğini sağlar.

**Değerlendirme:**
- ROC AUC eğrisi ile model ayrım gücü analiz edilmiştir.
- Confusion Matrix ile sınıflandırma başarısı görselleştirilmiştir.

---

### 2. 🌄 Gradient Boosting

**Açıklama:**  
Gradient Boosting, zayıf öğrenicilerden (örneğin karar ağaçları) oluşan ardışık bir modelleme yöntemidir. Her yeni model, önceki modellerin hatalarını minimize edecek şekilde eğitilir. Bu yaklaşım, doğruluk oranını artırırken overfitting riskini kontrollü şekilde yönetir.

**Uygulanan Parametreler:**
- `random_state = 42`: Sabit rastgelelik ile tekrarlanabilirlik sağlanmıştır.

**Değerlendirme:**
- ROC AUC ve confusion matrix ile modelin ayrım gücü ve hata tipi analizi yapılmıştır.
- Classification Report ile precision, recall ve F1 skorları özetlenmiştir.

---

### 3. 📍 K-Nearest Neighbors (KNN)

**Açıklama:**  
KNN, sezgisel ve örnek tabanlı bir sınıflandırma yöntemidir. Yeni bir gözlem, eğitim verisindeki en yakın K komşusunun sınıfına göre sınıflandırılır. Bu projede çok sınıflı yapı için `OneVsRestClassifier` stratejisi uygulanmıştır.

**Uygulanan Parametreler:**
- `n_neighbors = 5`: Tahminler en yakın 5 komşu üzerinden yapılmıştır.

**Ek Özellikler:**
- ROC AUC hesaplamaları için çoklu sınıflar one-hot encoding ile dönüştürülmüş ve her sınıf için ayrı ROC eğrisi çizilmiştir.

---

Her model, veri setindeki sınıf dengesizliği, aykırı değerler ve özellik ölçekleme ihtiyaçları göz önünde bulundurularak dikkatlice değerlendirilmiş ve yorumlanmıştır.


---

## 📊 Değerlendirme Metrikleri

Aşağıda **Random Forest**, **Gradient Boosting** ve **K-Nearest Neighbors (KNN)** modellerinin sınıflandırma başarımı, çeşitli metrikler kullanılarak karşılaştırılmıştır:

| Metrik                | Random Forest 🌲 | Gradient Boosting 🌄 | KNN 📍       |
|------------------------|------------------|------------------------|--------------|
| **Accuracy**           | %55              | **%56**                | %48          |
| **Precision (Macro)**  | %53              | **%54**                | %46          |
| **Recall (Macro)**     | %56              | **%57**                | %44          |
| **F1-Score (Macro)**   | %54              | **%55**                | %45          |
| **En Başarılı Sınıf**  | Sınıf 4 (%72 recall) | Sınıf 4 (%77 recall)  | Sınıf 1 (%59 recall) |
| **En Zayıf Sınıf**     | Sınıf 3 (%41 recall) | Sınıf 3 (%42 recall) | Sınıf 4 (%32 recall) |
| **ROC AUC Skoru**      | Orta, dengesiz   | **Daha belirgin ve dengeli** | Düşük ve dengesiz |
---

## 📈 Confusion Matrix ve ROC AUC Grafikleri 

### 🎯 Confusion Matrix Karşılaştırması

| Random Forest | Gradient Boosting | K-Nearest Neighbors (KNN) |
|---------------|-------------------|----------------------------|
| ![RF Confusion Matrix](https://github.com/user-attachments/assets/ccdefd2f-4853-4446-9fd3-c5d481340994) | ![GB Confusion Matrix](https://github.com/user-attachments/assets/246b4d3d-b3d8-48c3-bfec-e59af9b2a7d9) | ![KNN Confusion Matrix](https://github.com/user-attachments/assets/ad3a3bac-fd7a-42a0-ac07-f43f5d7d3179) |

#### 1. **Confusion Matrix Karşılaştırması**  
- **Random Forest**: Sınıf 0 ve 4 için güçlü tahmin, ancak sınıf 2 ve 3 için zayıf.
- **Gradient Boosting**: Sınıf 4 için en güçlü ayrım, genel olarak daha dengeli dağılım.
- **KNN**: Tüm sınıflarda zayıf tahmin performansı, özellikle sınıf 2, 3 ve 4 için.
---

### 📉 ROC AUC Eğrisi Karşılaştırması

| Random Forest | Gradient Boosting | K-Nearest Neighbors (KNN) |
|---------------|-------------------|----------------------------|
| ![RF ROC AUC](https://github.com/user-attachments/assets/e1cbc09a-ae95-41e6-abb9-a692cf4a8546) | ![GB ROC AUC](https://github.com/user-attachments/assets/bbf6a7e1-dd30-4af5-a635-4f6118ce99f9) | ![KNN ROC AUC](https://github.com/user-attachments/assets/03b076ff-4b84-4e03-8ea5-570d466a2100) |

#### 2. **ROC AUC Eğrileri**
- **Random Forest**: Sınıflar arası ayrım gücü orta seviyede, dengesizlik mevcut.
- **Gradient Boosting**: ROC eğrisi daha belirgin, AUC değerleri sınıflar arasında daha dengeli.
- **KNN**: ROC eğrileri zayıf, modelin ayrım kapasitesi düşük.

---

## 🧠 Değerlendirme ve Sonuç

Bu çalışmada, sıcak hava koşullarında ısı çarpması vakalarının erken tahmini amacıyla sınıflandırma problemini çözmek üzere **Random Forest**, **Gradient Boosting** ve **K-Nearest Neighbors (KNN)** algoritmaları uygulanmış; modeller, aynı veri seti üzerinde çeşitli performans metrikleri kullanılarak kapsamlı bir şekilde değerlendirilmiştir. Analizlerde doğruluk (accuracy), precision, recall, F1-score, confusion matrix ve ROC-AUC gibi ölçütler kullanılmıştır.

### Model Performansları

- **Random Forest:**  
  Genel doğruluk oranı yaklaşık %55, makro F1 skoru %54 civarında gerçekleşmiştir. Model, özellikle yüksek destekli sınıflar olan 0 ve 4 numaralı sınıflarda başarılı tahminler yaparken, sınıf 2 ve 3’te ayırt edici gücü belirgin şekilde düşmüştür. Confusion matrix ve ROC eğrileri, modelin sınıflar arasında orta düzeyde fakat dengeli bir ayrım yeteneğine sahip olduğunu ortaya koymuştur.

- **Gradient Boosting:**  
  Doğruluk açısından Random Forest ile benzer (%56) bir sonuç elde edilmiş olmasına rağmen, ROC AUC ve sınıf bazlı performans analizlerinde daha üstün bir performans sergilemiştir. Sınıf 4’te %77 gibi yüksek bir recall değeri ile öne çıkmış, bu da özellikle az örnekli ve zorlayıcı sınıflarda yüksek başarıya işaret etmektedir. Sınıf 0 ve diğer sınıflarda da dengeli ve güçlü tahminler gerçekleştirmiştir. Genel olarak, Gradient Boosting modeli daha stabil ve tutarlı bir sınıflandırma kabiliyeti göstermiştir.

- **K-Nearest Neighbors (KNN):**  
  Diğer iki modele kıyasla daha düşük bir doğruluk (%48) ve makro performans metriklerinde zayıf sonuçlar elde edilmiştir. Sınıflar arası ayrım gücü sınırlı kalmış, özellikle sınıf 2, 3 ve 4’te precision ve recall değerleri anlamlı şekilde düşmüştür. ROC eğrileri de bu zayıflığı doğrulamaktadır; KNN, sınıf ayrımında yetersiz kalmış ve genel tahmin gücü düşük olmuştur.

### Genel Değerlendirme

- **🔝 En Başarılı Model:**  
  **Gradient Boosting**, metrikler arasında daha dengeli bir dağılım ve güçlü sınıf ayrımı ile öne çıkmış, özellikle zor ve az destekli sınıflarda yüksek başarı sağlamıştır.

- **🛠️ İyileştirme Gerektiren Model:**  
  **KNN**, düşük doğruluk ve sınıflar arası dengesiz tahmin gücü nedeniyle performans açısından diğer modellerin gerisinde kalmıştır.

### Öneriler ve Gelecek Çalışmalar

Model performansını artırmak için aşağıdaki stratejilerin uygulanması tavsiye edilir:

- **Özellik mühendisliği:** Daha ayırt edici ve anlamlı özellikler çıkarılarak modellerin genel başarısı artırılabilir.  
- **Gelişmiş modellerin kullanımı:** XGBoost, LightGBM gibi güçlü gradyan artırma algoritmaları denenebilir.  
- **Ensemble ve model stacking:** Modellerin kombinasyonu ile tahmin performansı daha da güçlendirilebilir.  
- **Hiperparametre optimizasyonu:** Grid search, random search veya Bayesian optimizasyonu gibi yöntemlerle modellerin parametreleri ince ayar yapılabilir.

---
