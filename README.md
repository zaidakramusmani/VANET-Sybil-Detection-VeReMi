
---

#  Sybil Attack Detection in VANETs
![Python](https://img.shields.io/badge/Python-3.10-blue)

## A Comparative Ablation Study across VeReMi Dataset Variants

A **controlled ablation study** investigating what *actually* drives Sybil attack detection performance in Vehicular Ad-hoc Networks (VANETs.

Contrary to common assumptions, results show that **trajectory-level aggregation enabled by identity persistence** is far more impactful than increasing model complexity.

---

##  Overview

This project systematically evaluates Sybil attack detection performance across **four experimental configurations**, using:

* **Raw VeReMi** (≈22 million messages)
* **VeReMi Extension** (≈3.2 million messages)

The experiments isolate the effect of:

* Dataset quality
* Identity availability
* Feature granularity (message-level vs trajectory-level)

### Central Finding

> **Trajectory-level feature aggregation—enabled by sender pseudonym preservation—is the dominant driver of detection performance, not raw model complexity.**

---

##  Datasets

| Dataset          | Size    | Messages | Sender Identity |
| ---------------- | ------- | -------- | --------------- |
| VeReMi (raw)     | ~7 GB   | 22M      |  Not available |
| VeReMi Extension | ~1.2 GB | 3.2M     |  Available     |

To handle scale safely, **raw VeReMi** is processed using **memory-safe chunked ingestion**:

* 100,000 rows per chunk
* No full-dataset RAM loading

---

##  Experimental Design

Each experiment incrementally adds **one capability at a time**, enabling clean attribution of performance gains.

### Ablation Study Results

| Exp | Dataset          | Analysis Unit | Feature Set           | Accuracy  | F1-score |
| --- | ---------------- | ------------- | --------------------- | --------- | -------- |
| 1   | Raw VeReMi       | Per message   | Physics only          | 64.0%     | 0.63     |
| 2   | VeReMi Extension | Per message   | Physics only          | 78.7%     | 0.78     |
| 3   | VeReMi Extension | Per message   | Physics + Identity*   | 82.8%     | 0.82     |
| 4   | VeReMi Extension | Per vehicle   | Trajectory aggregates | **97.0%** | **0.97** |

* **Experiment 3 is an upper-bound analysis only**.
Identity features are **not available in real-world VANET deployments** and are included solely to quantify their theoretical impact.

---

##  Model Performance (Experiment 4)

| Metric | Normal | Sybil | Macro Avg |
|---|---|---|---|
| Precision | 0.99 | 1.00 | 0.99 |
| Recall | 1.00 | 0.97 | 0.98 |
| F1 | 0.99 | 0.98 | 0.98 |

> **Zero false alarms (Sybil Precision = 1.00)** — critical for V2X safety-critical deployments.

---

##  Key Insight: Where the Gains Come From

The **33 percentage-point accuracy improvement** between Exp-1 and Exp-4 decomposes approximately as:

* **+15 pp** → Improved dataset quality (richer simulation context)
* **+4 pp** → Sender identity availability
* **+14 pp** → Trajectory-level feature aggregation

**Conclusion:**
Even *perfect models* perform poorly when forced to reason at the **per-message level** without historical context.

---

##  Why Trajectories Matter

Sybil attacks are **temporal and behavioral** by nature.

Single CAM/BSM messages often look benign.
Malicious intent emerges only when **movement consistency is violated over time**.

Trajectory aggregation allows the detector to capture:

* Physical impossibilities
* Behavioral instability
* Transmission anomalies

---

##  Trajectory-Level Features (Experiment 4)

Per-vehicle aggregates computed across the **entire transmission history**:

```python
max_pos_jump   # maximum position delta between consecutive beacons
max_spd_jump   # maximum speed delta between consecutive beacons
spd_std        # speed variance across trajectory
posx_std       # spatial dispersion (x-axis)
beacon_rate    # transmission frequency anomaly
```

These features encode **behavioral consistency**, which is the core weakness of Sybil nodes.

---

##  Methodological Takeaways

* Message-level ML is fundamentally limited for Sybil detection
* Dataset realism matters more than classifier choice
* Identity persistence enables behavior modeling
* Trajectory aggregation turns weak signals into strong evidence

---

##  Research Contribution

This work demonstrates—quantitatively—that:

> **Sybil detection is a data representation problem first, and a modeling problem second.**

The findings help explain why many VANET Sybil papers report unstable or non-generalizable results.

---

## Reproducing Results

1. Download **VeReMi Extension** from  
   https://data.mendeley.com/datasets/k62n4z9gdz

2. Place the dataset as:

data/mixalldata_clean.csv


3. Run the notebook:

VeReMi.ipynb

Execute top to bottom to reproduce all reported results.

---

##  License

MIT License — free for academic research and reproducibility.

---

## 👤 Author

**Zaid Akram Usmani**
Focused on **security-critical ML**, **adversarial systems**, and **research-grade evaluation**.

---
