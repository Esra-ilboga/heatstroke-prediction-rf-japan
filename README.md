# 🌡️ Heatstroke Risk Prediction Using Random Forest  
**Japonya Ambulans Verisi Üzerine Çok Sınıflı Sıcak Çarpması Tahmini**

## 📌 Proje Tanımı

Bu proje, Japonya'dan alınan ısıya bağlı sıcak çarpması ambulans çağrısı verilerini (HSAD) kullanarak, farklı sıcaklık koşullarında ısı çarpması vakalarının sınıflandırılmasını hedeflemektedir. Bu kapsamda, özellikle yaz aylarında artan sağlık risklerinin tahmini için **Random Forest**, **Gradient Boosting** ve **K-Nearest Neighbors (KNN)** algoritmaları uygulanmış ve karşılaştırmalı analiz yapılmıştır.

---

## 📁 Veri Kümesi

Kullanılan veri seti: **HSAD.csv**

Veri setinde yer alan bazı sütunlar:

- `Date` – Olay tarihi (silinmiştir)
- `Prefecturename` – Japonya’daki il adı (Label Encoding uygulanmıştır)
- `HSAD` – Ambulans çağrısı oranı (0 ile 1 arasında)
- `Tempmax`, `Rhumave` – Günlük sıcaklık ve nem ortalamaları
- `Year`, `Month`, `Day`, `Dow`, `Holiday` gibi zaman ve bağlamsal veriler

---

## ⚙️ Veri Ön İşleme

- `Date`, `Prefecture`, `Prefecturename` sütunları temizlenmiştir.
- `HSAD` oranına göre 5 sınıfa ayrılmıştır:  
  **0** – Yok, **1** – Düşük, **2** – Orta, **3** – Yüksek, **4** – Çok Yüksek
- Sayısal sütunlara **Min-Max normalization** uygulanmıştır.
- Sınıf dengesizliği **SMOTE** yöntemiyle giderilmiştir.
- Korelasyon, dağılım ve boxplot analizleri yapılmıştır.

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

## 📈 Örnek Görselleştirmeler

### 🎯 Confusion Matrix
![Confusion Matrix örneği](./assets/confusion_matrix_sample.png)

### 📈 ROC AUC Eğrisi
![ROC AUC örneği](./assets/roc_auc_sample.png)

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
