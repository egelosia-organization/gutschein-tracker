# Technisches Handbuch – Gutschein Tracker

Vollständige technische Dokumentation für Entwickler und Systemadministratoren.

---

## 🏗️ Architektur-Übersicht

### Tech-Stack

- **Framework** : Next.js 15 (App Router)
- **Sprache** : TypeScript
- **UI** : React 18 + Shadcn/UI + Tailwind CSS
- **Datenbank** : PostgreSQL (Neon serverless)
- **ORM** : Prisma 6.4+
- **Validierung** : Zod + React Hook Form
- **Deployment** : Vercel

### Schichtenarchitektur

```
┌─────────────────────────────────┐
│   React-Komponenten (UI)        │  src/app/gutscheinList/
│   - DataTable, Formulare        │
└──────────────┬──────────────────┘
               │
┌──────────────▼──────────────────┐
│   Next.js API-Routen            │  src/app/api/coupons/
│   - POST /api/coupons           │
│   - PUT /api/coupons/[id]/redeem│
└──────────────┬──────────────────┘
               │
┌──────────────▼──────────────────┐
│   Prisma ORM                    │  lib/db.ts
│   - Gutschein, GutscheinVerlauf │
└──────────────┬──────────────────┘
               │
┌──────────────▼──────────────────┐
│   PostgreSQL (Neon)             │
└─────────────────────────────────┘
```

---

## 📁 Projektstruktur

```
gutschein-tracker/
├── src/
│   ├── app/
│   │   ├── page.tsx              # Root → /gutscheinList
│   │   ├── layout.tsx            # Globales Layout
│   │   ├── api/
│   │   │   ├── coupons/
│   │   │   │   ├── route.ts      # GET/POST Gutscheine
│   │   │   │   ├── [id]/
│   │   │   │   │   └── redeem/route.ts  # PUT einlösen
│   │   │   │   ├── check-id/route.ts    # GET existiert
│   │   │   │   └── old-coupon/route.ts  # POST alt
│   │   │   └── dashboard/route.ts       # Statistiken
│   │   └── gutscheinList/
│   │       ├── page.tsx          # Hauptseite (Server)
│   │       ├── data-table.tsx    # Tabelle + Auth
│   │       ├── columns.tsx       # Spalten-Definitionen
│   │       ├── add-coupon.tsx    # Formular erstellen
│   │       ├── redeem-coupon-form.tsx   # Formular einlösen
│   │       └── utils.ts          # MITARBEITER_NAMEN, STANDORTE
│   ├── components/
│   │   ├── ui/                   # Shadcn-Komponenten
│   │   └── icons.tsx             # Lucide-Icons
│   ├── hooks/
│   │   └── use-toast.ts          # Toast-Benachrichtigungen
│   └── lib/
│       └── utils.ts              # Hilfsfunktionen
├── lib/
│   └── db.ts                      # Prisma Singleton
├── prisma/
│   ├── schema.prisma             # Datenmodelle
│   └── migrations/               # Migrations
├── docs/
│   ├── README.md
│   ├── BENUTZERHANDBUCH.md
│   ├── TECHNISCHES_HANDBUCH.md
│   └── WARTUNGSHANDBUCH.md
├── .env
├── package.json
├── tsconfig.json
└── next.config.ts
```

---

## 🗄️ Datenmodelle

### Gutschein

```prisma
model Gutschein {
  id            String          @id @default(cuid())
  erstelltAm    DateTime        @default(now())
  aktualisiertAm DateTime       @updatedAt
  
  mitarbeiter   String          # Wer hat erstellt
  beschreibung  String          # "NEU!", "ALT!", etc.
  
  gutscheinTyp  String          # "value" oder "klein"
  ursprungswert Decimal?        # Originalwert
  verwendetWert Decimal?        # Bereits ausgegeben
  restWert      Decimal?        # Verbleibend
  
  verwendet     Boolean         # Komplett aufgebraucht?
  laden         String?         # Standort
  altesSystem   Boolean         # Migration?
  
  zusatzzahlung Decimal?        # Surcoût
  trinkgeld     Decimal?        # Trinkgeld
  alteId        String?         # Getauschte Nummer
  
  gutscheinVerlauf GutscheinVerlauf[] @relation("GutscheinVerlauf")
}
```

### GutscheinVerlauf (Audit-Trail)

```prisma
model GutscheinVerlauf {
  id           String   @id @default(cuid())
  geaendertAm  DateTime @default(now())
  
  gutscheinId  String
  gutschein    Gutschein @relation("GutscheinVerlauf", fields: [gutscheinId], references: [id], onDelete: Cascade)
  
  # Snapshot des Zustands zum Änderungszeitpunkt
  mitarbeiter  String
  beschreibung String
  altesSystem  Boolean
  gutscheinTyp String
  ursprungswert Decimal?
  verwendetWert Decimal?
  restWert     Decimal?
  verwendet    Boolean
  laden        String?
  zusatzzahlung Decimal?
  trinkgeld    Decimal?
  alteId       String?
  
  @@index([gutscheinId, geaendertAm])
}
```

### Wichtige Punkte

- **Decimal** : Für genaue Geldwerte (kein Float)
- **Verlauf unveränderbar** : Jede Änderung erstellt einen Eintrag
- **Cascade Delete** : Löschen eines Gutscheins löscht seinen Verlauf
- **Timestamps** : Vollständige Nachverfolgung via `erstelltAm`, `aktualisiertAm`, `geaendertAm`

---

## 🔌 API-Endpunkte

### GET `/api/coupons`

Ruft alle Gutscheine ab.

**Response:**
```json
{
  "data": [
    {
      "id": "ABC123",
      "mitarbeiter": "Claudio",
      "gutscheinTyp": "value",
      "restWert": "10.00",
      "erstelltAm": "2026-10-13T14:30:00Z"
    }
  ]
}
```

### POST `/api/coupons`

Erstellt einen neuen Gutschein.

**Request:**
```json
{
  "id": "ABC123",
  "mitarbeiter": "Claudio",
  "laden": "Braugasse",
  "gutscheinTyp": "value",
  "ursprungswert": 10.00
}
```

### PUT `/api/coupons/[id]/redeem`

Löst einen Gutschein ein.

**Request:**
```json
{
  "verwendetWert": 5.50,
  "mitarbeiter": "Claudio",
  "laden": "Braugasse",
  "trinkgeld": 0.50,
  "neueId": "DEF456"
}
```

### GET `/api/coupons/check-id?id=ABC123`

Prüft, ob ein Gutschein existiert.

**Response:**
```json
{
  "exists": true
}
```

### GET `/api/dashboard`

Ruft Statistiken pro Standort ab.

---

## 🔐 Authentifizierung

### Passwörter pro Standort

**Datei** : `src/app/gutscheinList/data-table.tsx`

```typescript
const ladenPasswoerter: Record<string, Standort> = {
  braugasse: "Braugasse",
  transit: "Transit",
  pitstop: "Pit Stop",
  wirges: "Wirges",
  büro: "Büro",
  eiswagen: "Eiswagen",
};
```

⚠️ **Achtung** : Authentifizierung ist derzeit **nur auf Client-Seite** (localStorage). Für Production sollte es sein:
- Authentifizierung auf Server-Seite (Sessions)
- Gehashte Passwörter
- Middleware auf API-Routen

---

## 🚀 Installation und Entwicklung

### Voraussetzungen

- Node.js 18+
- npm oder yarn
- Git

### Setup

```bash
# Repository klonen
git clone <repo-url>
cd gutschein-tracker

# Abhängigkeiten installieren
npm install

# .env.local mit DATABASE_URL erstellen

# Prisma Client generieren
npx prisma generate

# Entwicklungsserver starten
npm run dev
```

Server läuft unter http://localhost:3000

### Befehle

```bash
npm run dev          # Entwicklungsserver
npm run build        # Production Build
npm start            # Production Server
npm run lint         # ESLint
npm run prisma:seed  # Datenbank initialisieren
```

---

## 🗄️ Datenbank

### Connection String

**Format:**
```
postgresql://benutzer:passwort@host:port/datenbank?param1=wert1&param2=wert2
```

**Neon Setup:**
- Pool Connection (mit pgbouncer)
- SSL erforderlich
- Channel Binding Support

### Migrationen

```bash
# Nach Schema-Änderung
npx prisma migrate dev --name <beschreibung>

# In Production anwenden
npx prisma migrate deploy

# Status prüfen
npx prisma migrate status
```

### Sicherung

```bash
# Manueller Dump
pg_dump "postgresql://..." > backup.sql

# Wiederherstellen
psql "postgresql://..." < backup.sql
```

---

## 📦 Vercel Deployment

### Schritte

1. **GitHub Push**
   ```bash
   git push origin main
   ```

2. **Vercel Link**
   ```bash
   vercel link
   ```

3. **Umgebungsvariablen in Vercel setzen**
   ```
   DATABASE_URL=postgresql://...?pgbouncer=true
   NEXT_PUBLIC_BASE_URL=https://deine-app.vercel.app
   ```

4. **Deployment**
   ```bash
   vercel deploy
   ```

---

## ⚡ Performance

### Aktuelle Optimierungen

- ✅ Connection Pooling (Neon mit pgbouncer)
- ✅ Transaktionen für Datenkonsistenz
- ✅ Cache Invalidation via `revalidatePath`
- ❌ Keine Pagination (GET /api/coupons holt alles)
- ❌ Keine Tests

### Zukünftige Verbesserungen

1. API-Pagination hinzufügen
2. Datenbankindizes optimieren
3. Caching-Strategie
4. End-to-End Tests (Playwright)

---

## 🐛 Debugging

### Logs

Browser-Konsole (F12) und Server-Konsole zeigen Debug-Ausgaben.

### Häufige Probleme

| Problem | Ursache | Lösung |
|---------|--------|--------|
| 500 Internal Error | DB-Fehler | DATABASE_URL prüfen |
| "Coupon not found" | ID existiert nicht | Nummer in DB prüfen |
| Langsam beim Einlösen | Neon Cold Start | Autosuspend erhöhen |
| Double-spending | Race Condition | Transaktionen nutzen |

---

**Version** : 1.0  
**Letzte Aktualisierung** : Oktober 2026
