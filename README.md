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
---

### Scatter Plotlar

Aşağıdaki grafikler, HSAD hedef değişkeni ile bazı önemli özelliklerin ilişkisini göstermektedir.

| ![HSAD vs Tempmax](path_to_scatter_tempmax.png) | ![HSAD vs Rhumave](path_to_scatter_rhumave.png) |
|:-----------------------------------------------:|:----------------------------------------------:|
| *HSAD ile Maksimum Sıcaklık arasında pozitif ilişki vardır. 30°C üzerindeki sıcaklıklarda çağrılar artmaktadır.* | *Ortalama nem ile HSAD arasında hafif pozitif ilişki gözlenir. Nem, sıcaklıkla birlikte değerlendirilmelidir.* |

| ![HSAD vs Holiday](path_to_scatter_holiday.png) | ![HSAD vs Month](path_to_scatter_month.png) |
|:-----------------------------------------------:|:------------------------------------------:|
| *Tatil olmayan günlerde ambulans çağrısı daha fazladır.* | *Temmuz ve Ağustos aylarında çağrılar yoğunlaşmıştır, mevsimsel etkiler vardır.* |

---

### Histogramlar

Aşağıdaki histogramlar sayısal sütunların veri dağılımını göstermektedir.

| ![Histogram Tempmax](path_to_hist_tempmax.png) |
|:----------------------------------------------:|
| *Tempmax sütunu sağa çarpık bir dağılıma sahiptir, yüksek sıcaklıklar nadir fakat kritik önemdedir.* |

| ![Histogram Rhumave](path_to_hist_rhumave.png) |
|:-----------------------------------------------:|
| *Nem değerleri çoğunlukla %40-80 aralığında yoğunlaşmıştır.* |

| ![Histogram HSAD](path_to_hist_hsad.png) |
|:-----------------------------------------:|
| *HSAD çoğunlukla 0 civarında toplanmıştır; düşük ve yüksek çağrılar nispeten azdır.* |

---

### Boxplot

![Boxplot](path_to_boxplot.png)

*Boxplot analizi özellikle Tempmax ve HSAD sütunlarında aykırı değerler olduğunu göstermektedir. Bu aykırı değerler kritik olayları temsil ettiğinden veri setinden çıkarılmamıştır.*

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
