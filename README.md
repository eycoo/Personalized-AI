# AI Discount Recommendation System


## 1. Struktur Project

```
project/
├─ data/                     # CSV mentah: transaksi, sessions, userprofile, dll.
│  └─ processed_data/        # sequence_data_full.csv hasil preprocessing
├─ models/
│  └─ transformer_discount.pt  # checkpoint model
├─ notebooks/
│  └─ main.ipynb             # notebook all-in-one: preprocessing, training, inference
├─ requirements.txt          # dependencies global
└─ README.md                 # file ini
```

---

## 2. Prasyarat

* Python ≥3.9
* Git & Git LFS
* Token Hugging Face CLI (untuk push model/dataset)

Install dependencies:

```bash
pip install -r requirements.txt
```

---

## 3. Notebook All-in-One (`pipeline.ipynb`)

1. **Preprocessing**

   * Jalankan sel Python untuk load CSV di `data/raw/`
   * Bersihkan (timestamp, LabelEncoding, agregasi) dan merge semua tabel
   * Simpan sequence ke `data/processed_data/sequence_data_full.csv`
2. **Training**

   * Definisikan `TransformerDiscount` + PositionalEncoding
   * Training loop dengan class‐weighted CrossEntropyLoss
   * Simpan model ke `models/transformer_discount.pt`
3. **Batch Inference**

   * Fungsi `run_inference_batch()` menghasilkan `data/predictions/discount_preds.csv`
4. **Realtime Inference & Recommendation**

   * Fungsi `realtime_inference_user()` + `realtime_recommendation()`
   * Kirim notifikasi diskon + rekomendasi item favorit di time window (lunch/dinner)
5. **Discount Strategy**

   * Mapping kelas → % diskon + rule tambahan (misal recency)

---

## 4. Deploy ke Hugging Face Spaces (Gradio)

1. **Buat Space** di [https://huggingface.co/spaces](https://huggingface.co/spaces) → klik New Space → pilih template Gradio → set public
2. **Copy ke folder `app/spaces/`**:

   * `app.py`   → Gradio UI + inference logic (lihat contoh di notebook)
   * `requirements.txt` → hanya dependencies untuk Spaces
3. **Koneksikan model dari HF Hub**:

   ```python
   from huggingface_hub import hf_hub_download
   pt_path = hf_hub_download(
       repo_id="your-username/your-model-name",
       filename="transformer_discount.pt"
   )
   ```
4. **Commit & push** ke repo Space → Space akan build & live

Akses URL Space:

```
https://huggingface.co/spaces/your-username/discount-recommender
```

---

## 5. Link Publik

* **Model:** isi di `Model_TeamX.txt` → misal `https://huggingface.co/your-username/your-model-name`
* **Dataset:** isi di `Dataset_TeamX.txt` → misal `https://huggingface.co/datasets/your-username/your-dataset-name`
* **Code:** isi di `Kode_TeamX.txt` → misal `https://github.com/your-username/datathon-discount-model`

---

## 6. Contoh Panggilan API Spaces

Kamu bisa panggil endpoint inference Spaces:

```bash
curl -X POST https://huggingface.co/spaces/your-username/discount-recommender/run/predict_fn \
  -H "Content-Type: application/json" \
  -d '{
    "seq_item_ids": [12,3,...],
    "seq_time_of_day": [2,1,...],
    "seq_price_bin": [0,1,...],
    "seq_weather_lvl": [1,2,...],
    "seq_disc_pct": [0,0.05,...],
    "seq_promo_main_enc": [4,4,...]
}'
```

---

