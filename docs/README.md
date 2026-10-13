# Gutschein Tracker – Dokumentation

Vollständige Dokumentation für die Verwaltung von Gutscheinen (Geschenkgutscheinen) des Eisladens.

## 📋 Inhalt

- **[BENUTZERHANDBUCH.md](./BENUTZERHANDBUCH.md)** — So verwendest du die Anwendung täglich
- **[TECHNISCHES_HANDBUCH.md](./TECHNISCHES_HANDBUCH.md)** — Architektur, API-Endpunkte, Deployment
- **[WARTUNGSHANDBUCH.md](./WARTUNGSHANDBUCH.md)** — Verwaltung von Mitarbeitern, Passwörtern, Sicherungen

## 🎯 Überblick

**Gutschein Tracker** ist eine Webanwendung zur Verwaltung von Gutscheinen über mehrere Standorte hinweg:

- **Braugasse**
- **Transit**
- **Pit Stop**
- **Wirges**
- **Büro**
- **Eiswagen**

### Hauptfunktionen

✅ **Gutscheine erstellen** — Neuen Gutschein mit Betrag und Mitarbeiter erfassen  
✅ **Gutscheine ansehen** — Übersicht aller laufenden Gutscheine  
✅ **Gutscheine einlösen** — Teilzahlung oder vollständigen Bezahlung registrieren  
✅ **Alte Gutscheine verwalten** — Import aus dem früheren System  
✅ **Vollständiger Verlauf** — Nachverfolgung jeder Transaktion mit Audit-Trail  
✅ **Dashboard** — Übersicht der Kennzahlen pro Standort  

## 🚀 Schnelleinstieg

### Für Benutzer
Siehe [BENUTZERHANDBUCH.md](./BENUTZERHANDBUCH.md) um zu erfahren, wie du:
- Dich anmeldest
- Einen Gutschein erstellst
- Einen Gutschein einlöst
- Den Verlauf anschaust

### Für Entwickler / Administratoren
Siehe [TECHNISCHES_HANDBUCH.md](./TECHNISCHES_HANDBUCH.md) für:
- Installation und Start der App
- Deployment auf Vercel
- Datenbankstruktur verstehen
- API-Endpunkte aufrufen

### Für Wartung und Verwaltung
Siehe [WARTUNGSHANDBUCH.md](./WARTUNGSHANDBUCH.md) für:
- Mitarbeiter hinzufügen/ändern
- Passwörter der Standorte ändern
- Fehlersuche und Behebung
- Datensicherung durchführen

## 💻 Technisches Setup

- **Frontend** : Next.js 15 + React + TypeScript
- **Backend** : Next.js API Routes + Prisma ORM
- **Datenbank** : PostgreSQL (Neon)
- **UI** : Shadcn/UI + Tailwind CSS
- **Deployment** : Vercel

## 📞 Support

Kontaktiere den Entwickler bei:
- Fehlern oder unerwartetem Verhalten
- Anfragen für neue Funktionen
- Technischen Fragen
- Deployment-Problemen

---

**Letzte Aktualisierung** : Oktober 2026  
**Anwendung** : Gutschein Tracker v1.0
