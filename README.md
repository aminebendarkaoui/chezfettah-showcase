# Chez Fettah — Full-Stack Booking Platform (FastAPI + React/TypeScript)

**Live:** https://chezfettah.com

> **Showcase Repo (ohne Quellcode):**  
> Dieses Repository ist eine **professionelle Projektdokumentation** (Architektur, Tech-Stack, Design-Entscheidungen).  
> Der produktive Code bleibt bewusst privat (Security/Secrets/Business-Logik).

---

## Überblick

**Chez Fettah** ist die offizielle Website & Buchungsplattform eines familiengeführten Gästehauses in Marokko mit:
- **Chalets**
- **Caravan-/Wohnmobil-Stellplätzen**
- **Restaurant**

Die Plattform ist als **Headless Full-Stack System** umgesetzt (Frontend/Backend getrennt deployt):
- **Frontend:** React + TypeScript (Vite) auf **Firebase Hosting**
- **Backend:** FastAPI (JSON API) als Container-Service auf **Google Cloud Run**
- **Daten:** **Firebase / Cloud Firestore**

---

## Architektur (Headless, getrennte Deployments)

### Frontend — React + TypeScript (Vite) auf Firebase Hosting
- Single Page App (SPA) mit React + TypeScript (Vite Build)
- Deployment auf Firebase Hosting (schnelle Auslieferung über globales CDN, HTTPS)
- Klare Trennung: UI/UX im Frontend, Business-Logik & Validierung im Backend

### Backend — FastAPI JSON API auf Google Cloud Run
- Stateless FastAPI API (REST/JSON)
- Containerized Deployment auf Cloud Run mit automatischem Skalieren (inkl. Scale-to-zero bei Inaktivität)
- Saubere Schichten: Routing → Use-Cases/Services → Data Access Layer

### Daten — Cloud Firestore (Firebase)
- Cloud Firestore als zentrale Datenbasis (Buchungen, Verfügbarkeiten, Angebote, Admin-Daten)
- Transaktionen für konsistente, race-condition-sichere Buchungen

---

## Kernfeature: Atomare Buchungen (Anti-Double-Booking)

Ziel: **Keine Doppelbuchungen**, auch nicht bei parallelen Requests.

**Ansatz (Firestore Transactions):**
1. Der angefragte Zeitraum wird in eindeutige **Slots** normalisiert (z. B. pro Tag: `YYYY-MM-DD`).
2. In einer **read-write Transaction** werden die betroffenen Slot-Dokumente gelesen.
   - Wenn **mindestens ein Slot bereits belegt** ist → Transaction bricht ab (keine Buchung).
3. Wenn alle Slots frei sind, werden **in derselben Transaction**:
   - ein **Booking-Dokument** erstellt
   - alle betroffenen **Availability-Slots** atomar als belegt/gesperrt geschrieben

**Ergebnis:**  
Firestore Transaktionen sind **atomar** (alles oder nichts). Read-write Transactions sperren gelesene Dokumente und werden bei Contention automatisch erneut versucht (Retry) — dadurch werden Race-Conditions zuverlässig vermieden.

---

## Security & Robustness

- **JWT-basierter Auth-Flow** (Login-Token, geschützte Endpunkte)
- Strikte **Server-Side Validierung** (kein Vertrauen in Client-Daten)
- Saubere **CORS-Konfiguration** für getrennte Origins (Browser-Frontend ↔ API)
- Konfiguration über **Environment Variables** (keine Secrets im Repository)
- Konsistente Fehlerbehandlung & Logging für nachvollziehbare Produktion

---

## Performance-Entscheidungen

- **Firebase Hosting**: schnelle Auslieferung über globales CDN
- **Cloud Run**: automatisches Skalieren je nach Traffic (effizient bei wenig Traffic, stabil bei Last)
- **Firestore**: transaktionsbasierte Buchungen + indexbewusstes Datenmodell für geringe Latenz und hohe Konsistenz

---

## Tech Stack

**Frontend**
- React
- TypeScript
- Vite
- Firebase Hosting

**Backend**
- Python
- FastAPI
- JWT Authentication
- Google Cloud Run

**Database / Cloud**
- Firebase / Cloud Firestore (Transactions für atomare Buchungen)

---

## Repository-Inhalt (Showcase)

- `/docs/` – Architektur, Flows, Screenshots
- `/diagrams/` – Systemdiagramme
- `README.md` – High-Level Overview

---

## Referenzen (Docs)

- Cloud Run Autoscaling / Scale-to-zero: https://docs.cloud.google.com/run/docs/about-instance-autoscaling
- Firestore Transactions (Atomic Operations): https://firebase.google.com/docs/firestore/manage-data/transactions
- Firebase Hosting (Global CDN): https://firebase.google.com/docs/hosting
- FastAPI (Framework Overview): https://fastapi.tiangolo.com/

---

## License

All rights reserved. Documentation-only repository (no production source code included).
