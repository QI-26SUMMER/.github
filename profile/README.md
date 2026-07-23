# D-avocado

Snap a photo of an avocado, find out its ripening stage, and know exactly how many days
are left until it's ready to eat.

D-avocado classifies avocado ripeness (stages 1–5) from a single photo using a
ResNet-18 model, then predicts the number of days remaining until your target eating
stage (D-day) — adjusted for storage temperature.

---

## Features

- **Ripeness classification** — upload a photo, get a 5-class prediction with
per-class confidence
- **D-day prediction** — days remaining until your target stage, based on the
predicted stage and (optionally) storage temperature
- **Target stage setting** — set your preferred eating stage once; it applies to
every scan automatically
- **Notifications** — get notified before your avocado passes its target stage
- **History** — browse past scans and their outcomes

See [`docs/PRD.md`](./docs/PRD.md) for the full product spec, target user, and scope.

---

## Tech Stack

| Layer | Stack |
| --- | --- |
| Mobile (iOS) | Swift / Xcode |
| Backend API | Spring Boot 3.4.2, Java 21, Gradle 8.14.3 (Groovy DSL) |
| ML Inference | FastAPI, PyTorch (ResNet-18) |
| Database | PostgreSQL (Cloud SQL) |
| Image Storage | Google Cloud Storage |
| Infra | Cloud Run, Artifact Registry, Vertex AI Custom Job |

---

## Architecture

```
                 ┌───────────────┐
                 │   iOS App     │
                 └───────┬───────┘
                         │ HTTPS
                 ┌───────▼────────────┐
                 │ Spring Boot API    │
                 │ (Cloud Run)        │
                 │  - auth (JWT)      │
                 │  - scans CRUD      │
                 │  - notifications   │
                 └───┬───────────┬────┘
                     │           │
        ┌────────────▼──┐   ┌────▼─────────────┐
        │ Cloud SQL      │   │ GCS              │
        │ (PostgreSQL)   │   │ d-avocado-images │
        └────────────────┘   └───────────────────┘
                     │
                 ┌───▼─────────────────┐
                 │ FastAPI ML service  │
                 │ (Cloud Run)         │
                 │  - ResNet-18 infer  │
                 │  - D-day calc (β)   │
                 └──────────────────────┘
```

The Spring Boot API owns auth, scan records, and notification scheduling. The FastAPI
service owns all ripeness inference and D-day/β-coefficient calculation — Spring only
stores the values it returns and contains no ripening-model logic itself.

---

## Repo Structure

```
davocado/
├── server/     # Spring Boot API
├── ml/         # ResNet-18 training + FastAPI inference service
└── docs/       # PRD, DB spec, API spec
```

---

## Getting Started

### Backend (`/server`)

```bash
cd server
./gradlew bootRun
```

Requires a running PostgreSQL instance and the environment variables described in
`server/src/main/resources/application-*.yml` (profile-separated config).

### ML service (`/ml`)

```bash
cd ml
pip install -r requirements.txt
uvicorn app.main:app --reload
```

Model weights are loaded per `model_version`; see `ml/README.md` for training and
inference details.

---

## Documentation

- [`docs/PRD.md`](./docs/PRD.md) — product requirements, target user, scope
- [`docs/DB_spec.md`](./docs/DB_spec.md) — database schema
- [`docs/API_spec.md`](./docs/API_spec.md) — API endpoints

---

## Dataset & References

- Hass Avocado Ripening Photographic Dataset (~14,700 images, CC BY 4.0), Mendeley Data
- Xavier et al. (2024), *Foods* — dataset paper and α ripening coefficients
- Perez et al. (2004) — Q10 value
- Arpaia et al. (2018) — temperature–ripening-speed plateau finding

---

## Team

ML modeling & backend, and AutoML comparison model developed by separate team members
as part of a capstone project. Roles and full team list — *TBD*.
