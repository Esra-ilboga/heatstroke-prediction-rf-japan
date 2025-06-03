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

![Boxplot](![image](https://github.com/user-attachments/assets/aa12fd19-8423-4758-ba6f-a7c4c9a25678)
)

**Açıklamalar:**
Boxplot grafikleri, her bir sayısal değişkenin merkezi eğilimlerini, çeyrek değerlerini ve veri aralığını özetlerken; kutu dışında kalan noktalar potansiyel aykırı değerleri göstermektedir:

- **HSAD** ve **Tempmax** değişkenlerinde çok sayıda aykırı değer tespit edilmiş olup, bu noktalar özellikle sıcak dalgaları gibi olağandışı durumları temsil edebilir.
- **Rhumave** değişkeni ise daha dengeli ve simetrik bir dağılım sergilemektedir.

Aykırı değerlerin doğrudan temizlenmesi, kritik bilgilerin kaybına yol açabileceğinden, bu değerlerin dikkatle analiz edilip korunması model başarımı açısından stratejik bir yaklaşımdır.

---

## 🤖 Kullanılan Modeller

### 1. Random Forest Classifier 🌲
- `n_estimators=300`, `max_depth=30`, `min_samples_split=5`, `min_samples_leaf=2`
- GridSearchCV ile hiperparametre optimizasyonu yapılmıştır
- ROC AUC ve confusion matrix görselleştirmeleri eklenmiştir

### 2. Gradient Boosting 🌄
- `random_state=42`
- ROC AUC, confusion matrix ve classification report ile performans değerlendirmesi yapılmıştır

### 3. K-Nearest Neighbors (KNN) 📍
- `n_neighbors=5`
- `OneVsRestClassifier` ile çok sınıflı yapı desteklenmiştir
- ROC AUC hesaplaması için one-hot encoding kullanılmıştır

---

## 📊 Değerlendirme Metrikleri

Tüm modeller için aşağıdaki metrikler karşılaştırılmıştır:

- 🎯 Accuracy
- 🎯 Precision (macro)
- 🎯 Recall (macro)
- 🎯 F1-Score (macro)
- 📈 ROC AUC Eğrisi (Multi-class)
- 📉 Confusion Matrix

---

## 📈 Confusion Matrix ve ROC AUC Grafikleri 

### 🎯 Confusion Matrix Karşılaştırması

| Random Forest | Gradient Boosting | K-Nearest Neighbors (KNN) |
|---------------|-------------------|----------------------------|
| ![RF Confusion Matrix](https://github.com/user-attachments/assets/ccdefd2f-4853-4446-9fd3-c5d481340994) | ![GB Confusion Matrix](https://github.com/user-attachments/assets/246b4d3d-b3d8-48c3-bfec-e59af9b2a7d9) | ![KNN Confusion Matrix](https://github.com/user-attachments/assets/ad3a3bac-fd7a-42a0-ac07-f43f5d7d3179) |

---

### 📉 ROC AUC Eğrisi Karşılaştırması

| Random Forest | Gradient Boosting | K-Nearest Neighbors (KNN) |
|---------------|-------------------|----------------------------|
| ![RF ROC AUC](https://github.com/user-attachments/assets/e1cbc09a-ae95-41e6-abb9-a692cf4a8546) | ![GB ROC AUC](https://github.com/user-attachments/assets/bbf6a7e1-dd30-4af5-a635-4f6118ce99f9) | ![KNN ROC AUC](https://github.com/user-attachments/assets/03b076ff-4b84-4e03-8ea5-570d466a2100) |



---

## 🧠 Sonuç

- **Gradient Boosting**, ROC AUC ve sınıf dengesi açısından en iyi performansı sergilemiştir.
- **Random Forest**, istikrarlı ancak bazı sınıflarda ayrım gücü zayıf kalmıştır.
- **KNN**, doğruluk oranı ve AUC açısından en zayıf sonuçları vermiştir.

➡️ Bu analizler, sıcak hava koşullarında ısı çarpması vakalarının önceden tahmin edilmesini sağlayarak sağlık otoriteleri için önleyici stratejiler geliştirilmesine katkı sağlamayı hedefler.

---

## 💾 Kurulum ve Kullanım

```bash
git clone https://github.com/kullanici-adi/heatstroke-prediction-rf-japan.git
cd heatstroke-prediction-rf-japan
pip install -r requirements.txt
python model_training.py
