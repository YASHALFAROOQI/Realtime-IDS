AI-Based Lightweight Web Intrusion Detection System

Real-time network + web intrusion detection dashboard. Sniffs live traffic with Scapy, classifies it using a hybrid ML pipeline (Random Forest for known attacks, Isolation Forest for zero-day anomalies), and flags web-specific attacks (SQLi, XSS, path traversal) via lightweight regex signatures on HTTP payloads. Alerts stream live to a Flask-SocketIO dashboard with IP geolocation.

Requirements
Python 3.9+
Administrator/root privileges — packet sniffing needs elevated access
Windows: Npcap installed (https://npcap.com) — Scapy needs it to capture packets
CICIDS2017 dataset CSVs (not included — see Data setup below)
bash
pip install -r requirements.txt
Data setup (not included in this repo)

The trained model and merged dataset are excluded from GitHub — CICIDS2017 is several GB and merged_dataset.csv alone can exceed GitHub's file size limits. You need to rebuild them locally:

Download the CICIDS2017 CSVs (e.g. from https://www.unb.ca/cic/datasets/ids-2017.html) into a data/ folder.
Merge them:
bash
   python merge_csvs.py

→ produces merged_dataset.csv 3. Optionally add synthetic DDoS samples and clean columns:

bash
   python prepare_data.py
Train the models:
bash
   python train_models.py

→ produces models/ids_pipeline.joblib (Random Forest + Isolation Forest + preprocessing pipeline). This step can take 5–10+ minutes depending on dataset size.

app.py will fail to start without models/ids_pipeline.joblib present.

Running the dashboard

Must run with admin/root privileges (required for packet sniffing):

Windows (as Administrator):

bash
python app.py

macOS/Linux:

bash
sudo python app.py

Open http://localhost:5000.

To test detection, send SQLi/XSS payloads to http://YOUR_IP:5000 (e.g. ?id=1' UNION SELECT-- or <script>alert(1)</script> in a query string).

What's excluded from git (add to .gitignore)
data/
merged_dataset.csv
models/
__pycache__/
*.pyc

The raw dataset, merged CSV, and trained model are all regenerable from the scripts above — don't fight to get them onto GitHub. If you want the trained model shareable without making others retrain, use Git LFS or upload it separately (e.g. a release asset or Google Drive link in this README) rather than committing it directly.
