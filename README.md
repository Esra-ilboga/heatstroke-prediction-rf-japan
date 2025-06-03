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

## 📈 Confusion Matrix ve ROC AUC Grafikleri 

### 🎯 Confusion Matrix Karşılaştırması

| Random Forest | Gradient Boosting | K-Nearest Neighbors (KNN) |
|---------------|-------------------|----------------------------|
| ![RF Confusion Matrix](![image](https://github.com/user-attachments/assets/9dc329c3-23dd-4efc-b127-abbd0197ae38)) | ![GB Confusion Matrix](![image](https://github.com/user-attachments/assets/079a43e0-a326-47e2-ba72-1c1689f0d821)) | ![KNN Confusion Matrix](![image](https://github.com/user-attachments/assets/1da583cc-3d84-4554-867d-bd7c211c4663)) |

---

### 📉 ROC AUC Eğrisi Karşılaştırması

| Random Forest | Gradient Boosting | K-Nearest Neighbors (KNN) |
|---------------|-------------------|----------------------------|
| ![RF ROC AUC](![image](https://github.com/user-attachments/assets/65cb12db-3407-40d8-8256-4d22470843d7)) | ![GB ROC AUC](![image](https://github.com/user-attachments/assets/e406d312-4fb3-4bdc-8d67-520812d8c942)) | ![KNN ROC AUC](![image](https://github.com/user-attachments/assets/fd40c092-4620-4a8e-80f0-7baee3d4ac4c)) |



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
