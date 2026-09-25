# 🥑 D-avocado

> **AI-powered avocado ripeness tracking platform**

Snap a photo of an avocado, find out its ripening stage, and know exactly how many days are left until it's ready to eat.

D-avocado classifies avocado ripeness (Stages 1–5) from a single photo using an image classification model (Google Vertex AI AutoML Vision in production, alongside an in-house ResNet-18 model), then predicts the remaining days until your preferred eating stage (D-day), adjusted for storage temperature.

---

# 📋 Executive Summary

**Subtitle:** Avocado Ripeness Detection & Prediction

D-Avocado is an AI service platform that helps consumers determine the ripeness of avocados and predict when they will reach their preferred stage of ripeness. One of the biggest challenges for avocado buyers is knowing whether an avocado is ready to eat or how many days remain until it reaches the desired condition. By simply taking and uploading a photo, users receive an estimate of the current ripeness level along with a prediction of when the avocado will be ready to enjoy. The platform can be used both before purchasing at the store and after bringing the avocado home. This allows consumers to make better purchasing decisions and enjoy avocados at their optimal freshness.

---

# 📱 Service Introduction

D-avocado is an AI-powered mobile application that helps users determine the optimal time to eat an avocado.

Instead of relying on subjective judgment, users simply take a photo of an avocado. The system automatically classifies its ripeness into one of five stages and predicts how many days remain until it reaches the user's preferred ripeness.

To improve the overall user experience, the application stores scan history, supports personalized ripeness preferences, and provides notifications before the avocado reaches its optimal eating stage.

<p align="center">
  <img src="https://raw.githubusercontent.com/QI-26SUMMER/.github/main/docs/images/service1.png" width="220"/>
  <img src="https://raw.githubusercontent.com/QI-26SUMMER/.github/main/docs/images/service2.png" width="220"/>
  <img src="https://raw.githubusercontent.com/QI-26SUMMER/.github/main/docs/images/service3.png" width="220"/>
</p>

---

# 🎥 Live Demo

<p align="center">
  <a href="https://youtu.be/Uzf3WhIbDaI">
    <img src="https://img.youtube.com/vi/Uzf3WhIbDaI/maxresdefault.jpg" width="800">
  </a>
</p>

<p align="center">
  <b>▶️ Watch the D-avocado Demo</b>
</p>

---

# ✨ Features

- 📷 **Ripeness Classification**
  - Upload a photo and receive a five-stage ripeness prediction.

- 📅 **D-day Prediction**
  - Estimate the remaining days until the avocado reaches your preferred ripeness stage.

- 🌡 **Temperature-aware Prediction**
  - D-day is adjusted using storage temperature.

- 👤 **Personalized Preferences**
  - Save your preferred eating stage once and apply it automatically to every scan.

- 🔔 **Notifications**
  - Receive reminders before the avocado reaches its target ripeness.

- 📚 **History**
  - Browse previous scans and prediction results.

---

# 🛠 Tech Stack

| Layer | Stack |
| --- | --- |
| Mobile (iOS) | Swift, SwiftUI (iOS 18+) |
| Backend API | Spring Boot 3.4.2, Java 21 |
| ML Inference | FastAPI on Cloud Run · Vertex AI AutoML Vision (production classifier) · PyTorch ResNet-18 (in-house model) · InSPyReNet (background removal) |
| Database | PostgreSQL (Cloud SQL) |
| Image Storage | Google Cloud Storage |
| Infrastructure | Cloud Run, Artifact Registry, Vertex AI (AutoML endpoint, Custom Job training) |

---

# 🏗 Architecture

<p align="center">
<img src="https://raw.githubusercontent.com/QI-26SUMMER/.github/main/docs/images/architecture.png" width="900">
</p>

The backend manages authentication, user information, scan history, user preferences, notifications, and cloud storage.

The AI inference service is independently deployed on Cloud Run and performs image preprocessing, ripeness prediction using Vertex AI AutoML, and D-day estimation.

---

# 🚀 End-to-End Workflow

```
Take Photo (iOS)
      │
      ▼
Upload Image → Spring Boot API
      │
      ▼
AI Inference Service (FastAPI, Cloud Run)
  ├─ Background removal & crop (InSPyReNet)
  ├─ Ripeness classification (Vertex AI AutoML)
  └─ Temperature-adjusted D-day calculation
      │
      ▼
Spring Boot: save scan · upload images to GCS · schedule notification
      │
      ▼
Return Result
```

---

# 📂 Repositories

| Repository | Description |
|------------|-------------|
| [d-avocado](https://github.com/QI-26SUMMER/d-avocado) | Project documentation (PRD, API, database, architecture, deployment, AI) |
| [davocado-frontend](https://github.com/QI-26SUMMER/davocado-frontend) | iOS app (Swift, SwiftUI) |
| [davocado-backend](https://github.com/QI-26SUMMER/davocado-backend) | Backend API (Spring Boot) |
| [d-avocado-ripeness-mlops](https://github.com/QI-26SUMMER/d-avocado-ripeness-mlops) | Model training, evaluation, and AI inference service (FastAPI) |

---

# 📚 Documentation

| Document | Description |
|----------|-------------|
| [PRD](https://github.com/QI-26SUMMER/d-avocado/blob/main/docs/PRD.md) | Product requirements |
| [API Specification](https://github.com/QI-26SUMMER/d-avocado/blob/main/docs/API.md) | Backend API documentation |
| [Database Specification](https://github.com/QI-26SUMMER/d-avocado/blob/main/docs/Database.md) | Database schema |
| [System Architecture](https://github.com/QI-26SUMMER/d-avocado/blob/main/docs/Architecture.md) | Overall system architecture |
| [Deployment](https://github.com/QI-26SUMMER/d-avocado/blob/main/docs/Deployment.md) | Deployment and infrastructure |
| [AI Documentation](https://github.com/QI-26SUMMER/d-avocado/blob/main/docs/AI.md) | AI model, training, and evaluation |

---

# 📖 Dataset, Models & References

### Dataset

- **Hass Avocado Ripening Photographic Dataset** (~14,700 images) — Mendeley Data. DOI: [10.17632/3xd9n945v8.1](https://doi.org/10.17632/3xd9n945v8.1) (License: CC BY 4.0)
  - Images used per model: ResNet-18 13,192 (curated subset of 392 samples with a complete stage 1→5 trajectory) · AutoML Raw 14,570 · AutoML Balanced 20,000 (balanced to 4,000 per class through data augmentation)

### AI Models & Tools

- **ResNet-18** (ImageNet-pretrained, torchvision) — He, K., Zhang, X., Ren, S., Sun, J. "Deep Residual Learning for Image Recognition." CVPR 2016. [arXiv:1512.03385](https://arxiv.org/abs/1512.03385)
- **AlexNet** (ImageNet-pretrained, torchvision; comparison model) — Krizhevsky, A., Sutskever, I., Hinton, G.E. "ImageNet Classification with Deep Convolutional Neural Networks." NeurIPS 2012.
- **InSPyReNet** (background removal) — Kim, T. et al. "Revisiting Image Pyramid Structure for High Resolution Salient Object Detection." ACCV 2022. [arXiv:2209.09475](https://arxiv.org/abs/2209.09475) · [transparent-background](https://github.com/plemeri/transparent-background)
- **U²-Net / rembg** (background removal, fallback) — Qin, X. et al. "U²-Net: Going Deeper with Nested U-Structure for Salient Object Detection." Pattern Recognition 2020. [arXiv:2005.09007](https://arxiv.org/abs/2005.09007) · [rembg](https://github.com/danielgatis/rembg)
- **Google Vertex AI AutoML Vision** (production classifier; AutoML Vision Balanced model) — [cloud.google.com/vertex-ai](https://cloud.google.com/vertex-ai)

### Papers

- Xavier, P., Rodrigues, P.M., Silva, C.L.M. (2024). "Shelf-Life Management and Ripening Assessment of 'Hass' Avocado (*Persea americana*) Using Deep Learning Approaches." *Foods*, 13(8), 1150. [doi.org/10.3390/foods13081150](https://doi.org/10.3390/foods13081150) — dataset, ripening coefficients (α); our temperature Q10 is derived from re-fitting this dataset
- Arpaia, M.L., Collin, S., Sievert, J., Obenland, D. (2018). "'Hass' avocado quality as influenced by temperature and ethylene prior to and during final ripening." *Postharvest Biology and Technology*, 140, 76–84. [doi.org/10.1016/j.postharvbio.2018.02.015](https://doi.org/10.1016/j.postharvbio.2018.02.015) — ripening-rate plateau above 20 °C

---

# 🙏 Acknowledgment


---

# 👥 Team

## Team Photo

<p align="center">
<img src="https://raw.githubusercontent.com/QI-26SUMMER/.github/main/docs/images/team_photo.png" width="800">
</p>

---

## Responsibilities

| Member | Major | Role & Responsibilities |
|---------|------------------|-------------------------|
| **Taeyeon Hwang** | Kyonggi University Computer Science | **Tech Lead** · Backend development · Ripeness classification model development · GCP infrastructure and server deployment |
| **Yujin Nam** | Keimyung University Computer Science | **Frontend Developer** · iOS app development · Data collection and dataset preparation |
| **HyeongJun Kim** | Chosun University Information and Communication Engineering | **ML Engineer** · Data analysis · Ripeness classification model development |
| **Seungchae Lee** | Kumoh National Institute Of Tech | **Data & ML Engineer** · Data analysis · Model evaluation and performance analysis |
| **Seon Ung** | Keimyung University Automotive Engineering | **Prediction Model Developer** · D-day prediction model · Temperature-dependent mathematical modeling |

---

# 📌 Future Work

- Push notification scheduling
- Continuous model retraining
- Explainable AI
- Model monitoring
- CI/CD automation
- Performance optimization

---

# 📄 License

This project was developed as a university capstone project for educational and research purposes.
