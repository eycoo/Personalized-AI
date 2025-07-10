# Personalized-AI

# AI Discount Recommendation System

README ini menjelaskan cara menggunakan keseluruhan pipeline AI kamu, mulai dari preprocessing, training, batch inference, realtime inference, hingga deployment.

---

## 1. Struktur Project

```
project/
├─ data/
│  ├─ raw/                   # file mentah (.csv, dsb.)
│  └─ processed_data/        # sequence_data_full.csv hasil pipeline
├─ models/
│  └─ transformer_discount.pt  # model hasil training
├─ app/
│  ├─ main.py                # FastAPI entry point
│  ├─ model.py               # load + predict helper
│  └─ schemas.py             # request/response Pydantic
├─ notebooks/
│  └─ pipeline.ipynb         # notebook all-in-one: preprocessing, training, inference
├─ requirements.txt          # dependencies
├─ Dockerfile                # container setup
├─ Model_TeamX.txt           # Hugging Face model URL
├─ Dataset_TeamX.txt         # Hugging Face dataset URL
├─ Kode_TeamX.txt            # GitHub repo URL
└─ README.md                 # file ini
```

---

## 2. Prasyarat

* Python ≥3.9
* Git, Git LFS
* CUDA (opsional untuk training GPU)
* Token Hugging Face CLI

Instalasi dependencies:

```bash
pip install -r requirements.txt
```

di mana `requirements.txt` memuat:

```
pandas==1.5.3
numpy==1.24.2
scikit-learn==1.2.2
torch==2.0.1
fastapi==0.95.1
uvicorn==0.22.0
huggingface_hub==0.17.1
python-dotenv==1.0.0
pyyaml==6.0
```

---

## 3. Notebook All-in-One (`pipeline.ipynb`)

1. **Preprocessing**: baca folder `data/raw/`, bersihkan semua tabel (transaksi, sessions, userprofile, dll.), kemudian merge & build sequence lalu simpan ke `data/processed_data/sequence_data_full.csv`.
2. **Training**: jalankan sel definisi model & training loop; output model tersimpan di `models/transformer_discount.pt`.
3. **Batch Inference**: sel `run_inference_batch` menghasilkan file `data/predictions/discount_preds.csv`.
4. **Realtime Inference**: sel `realtime_inference_user()` dan `realtime_recommendation()` untuk mem-proses on-the-fly.
5. **Discount Strategy**: mapping prediksi→% diskon dan rules tambahan, output `data/strategies/discount_strategy.csv`.
6. **Recommendation**: fungsi `recommend_favorite_item()` untuk notif item favorit di window waktu.

---

## 4. Cara Pakai REST API (FastAPI)

1. **Build dan Run**:

   ```bash
   cd app
   uvicorn main:app --reload --host 0.0.0.0 --port 8000
   ```
2. **Endpoint**:

   * `POST /predict`  → prediksi kelas diskon

     * Request JSON:

       ```json
       {
         "seq_item_ids": [...],
         "seq_time_of_day": [...],
         "seq_price_bin": [...],
         "seq_weather_lvl": [...],
         "seq_disc_class": [...],
         "seq_promo_main_enc": [...]
       }
       ```
     * Response JSON:

       ```json
       {
         "pred_class": 2,
         "probs": [0.1, 0.2, 0.6, 0.1]
       }
       ```

---

## 5. Deployment dengan Docker

1. **Build image**:

   ```bash
   ```

docker build -t discount-api .

````
2. **Run container**:
   ```bash
docker run -d -p 8000:8000 discount-api
````

---

## 6. Mengakses Model & Dataset

* **Model**: URL pada `Model_TeamX.txt` (Hugging Face Hub)
* **Dataset**: URL pada `Dataset_TeamX.txt`
* **Code**: URL pada `Kode_TeamX.txt` (GitHub)

---

## 7. Contoh Pemanggilan (cURL)

```bash
curl -X POST http://localhost:8000/predict \
  -H "Content-Type: application/json" \
  -d '{
    "seq_item_ids": [12,3,7,...],
    "seq_time_of_day": [2,2,1,...],
    "seq_price_bin": [0,1,2,...],
    "seq_weather_lvl": [1,1,2,...],
    "seq_disc_class": [0,0,1,...],
    "seq_promo_main_enc": [5,5,3,...]
}'
```

---

## 8. Kontak & Lisensi

* **Tim**: \[Nama Tim]
* **Email**: [your-team@example.com](mailto:your-team@example.com)
* **License**: MIT License
