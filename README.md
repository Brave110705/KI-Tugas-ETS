# Tugas ETS Keamanan Informasi: Key Distribution Scenario (KDC) & Manual AES

### 👥 Anggota Kelompok

|    NRP     |          Name          |
| :--------: | :--------------------: |
| 5025241135 | Farikh Muhammad Fauzan |
| 5025241140 |     Brave Juliada      |

Implementasi skenario distribusi kunci simetris (**Key Distribution Center - KDC**) 5 langkah (Needham-Schroeder) dan komunikasi pesan dua arah (*full-duplex*) menggunakan algoritma **Advanced Encryption Standard (AES-128) Murni** tanpa library kriptografi eksternal.

---

## 🎯 Spesifikasi & Arsitektur Sistem

1. **3 Entitas Berbeda (3 IP Address):**
   - **KDC (`172.28.0.10:5000`):** Server pihak ketiga tepercaya pemegang *master key* $K_a$ dan $K_b$ serta generator *session key* $K_s$.
   - **Alice (`172.28.0.11`):** Initiator (Node A) yang memulai request ke KDC dan membuka sesi ke Bob.
   - **Bob (`172.28.0.12:5001`):** Responder (Node B) yang memvalidasi tiket KDC dan melakukan *mutual authentication*.

2. **Protokol Key Distribution & Mutual Authentication (5 Langkah):**
   - **Langkah 1 ($A \rightarrow KDC$):** `IDA || IDB || N1` (Plaintext ASCII)
   - **Langkah 2 ($KDC \rightarrow A$):** $E(K_a, [K_s \parallel ID_A \parallel ID_B \parallel N_1]) \parallel E(K_b, [K_s \parallel ID_A])$ (Hex)
   - **Langkah 3 ($A \rightarrow B$):** $E(K_b, [K_s \parallel ID_A])$ (Hex)
   - **Langkah 4 ($B \rightarrow A$):** $E(K_s, N_2)$ *(Authentication Challenge - Hex)*
   - **Langkah 5 ($A \rightarrow B$):** $E(K_s, f(N_2))$ dengan $f(N_2) = N_2 - 1$ *(Challenge Response - Hex)*

3. **Komunikasi Dua Arah & Interaktivitas Tanpa Restart:**
   - Setelah Langkah 5 terverifikasi, Alice dan Bob dapat saling mengirim dan menerima pesan secara simultan (*full-duplex*) dengan enkripsi AES-128 menggunakan $K_s$.
   - Mendukung perintah interaktif *live* di terminal:
     - `/keys` : Melihat kunci aktif ($K_a / K_b / K_s$).
     - `/setkey <ks_baru>` : Mengubah Session Key $K_s$ secara dinamis tanpa mematikan program.
     - `/setmaster <k_baru>` : Mengubah Master Key $K_a$ / $K_b$ secara dinamis.
     - `/rekey` : **(Fitur Bonus)** Meminta Session Key $K_s$ baru ke KDC dan menjalankan ulang Langkah 1–5 secara *live*.
