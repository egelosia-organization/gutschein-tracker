# Wartungshandbuch – Gutschein Tracker

Anleitung zur täglichen Verwaltung: Mitarbeiterverwaltung, Passwörter, Sicherungen, Fehlerbehebung.

---

## 👥 Mitarbeiterverwaltung

### Neuen Mitarbeiter hinzufügen

Mitarbeiternamen sind in der Datei **`src/lib/utils.ts`** gespeichert.

**Schritte:**

1. Öffne `src/lib/utils.ts`
2. Finde die Konstante `EMPLOYEE_NAMES = [...]`
3. Füge den Namen hinzu (alphabetisch bevorzugt)
   ```typescript
   export const EMPLOYEE_NAMES = [
     "Claudio",
     "Sabine",
     "Anja M",
     // ... andere Namen ...
     "Zied",  // ← neu
   ];
   ```
4. Datei speichern
5. Git Commit und Push
   ```bash
   git add src/lib/utils.ts
   git commit -m "Add new employee: Zied"
   git push
   ```
6. Vercel deployed automatisch

### Mitarbeiter umbenennen

**Wenn der Mitarbeiter bereits Gutscheine hat:**
- ⚠️ Nicht direkt umbenennen
- Existierende Gutscheine behalten den alten Namen im Verlauf
- Das ist wichtig für die Nachverfolgung

**Für neue Mitarbeiter ohne Gutscheine:**
- Kann problemlos in `EMPLOYEE_NAMES` umbenannt werden

### Mitarbeiter löschen

Das Löschen aus der Liste beeinträchtigt nicht die existierenden Gutscheine.
Sie behalten den Namen in ihrem Verlauf.

---

## 🔑 Passwortverwaltung

### Passwörter pro Standort

Die Passwörter sind in **`src/app/gutscheinList/data-table.tsx`** definiert:

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

### Passwort ändern

1. Öffne `src/app/gutscheinList/data-table.tsx`
2. Finde den Abschnitt `ladenPasswoerter`
3. Ändere den Wert:
   ```typescript
   braugasse: "neuesPasswort",  // ← geändert
   ```
4. Commit und Push
5. **Teile das neue Passwort** mit dem Standort-Team mit

⚠️ **Wichtig** : Passwörter sind derzeit **nicht gehasht** (aktuelle Limitation).

---

## 💾 Datensicherung

### Neon Datenbank

Die Datenbank ist auf **Neon** (serverless PostgreSQL) gehostet.

### Manuelle Sicherung erstellen

**Via Neon Console:**
1. Gehe auf https://console.neon.tech
2. Wähle dein Projekt
3. Gehe zu **Backups**
4. Klick **"Create Snapshot"**

**Via PostgreSQL CLI:**
```bash
pg_dump "postgresql://benutzer:passwort@host/datenbank" > sicherung.sql
```

### Sicherung wiederherstellen

**Via Neon:** Kontaktiere Neon Support
**Via CLI:**
```bash
psql "postgresql://benutzer:passwort@host/datenbank" < sicherung.sql
```

### Sicherungsplan

- **Täglich** : Automatische Snapshots (von Neon verwaltet)
- **Wöchentlich** : Manueller Snapshot vor Updates
- **Vor Änderungen** : Snapshot vor DB-Migrations

---

## 🔧 Fehlerbehebung

### Problem: "Connection refused"

**Symptome:** Fehler "Cannot connect to database"

**Mögliche Ursachen:**
1. DATABASE_URL falsch oder abgelaufen
2. Neon inaktiv (Cold Start)
3. Netzwerk blockiert Vercel → Neon

**Lösungen:**
```bash
# Verbindung lokal testen
npm run dev

# DATABASE_URL in .env prüfen
# Format: postgresql://benutzer:pass@host/db?params

# Vercel neu deployed
vercel redeploy
```

### Problem: "Coupon not found"

**Symptome:** Gutschein wird nicht erstellt

**Mögliche Ursachen:**
1. Netzwerkfehler bei Absendung
2. ID existiert bereits (nicht angezeigt)
3. Request Timeout

**Lösungen:**
1. Browser-Konsole prüfen (F12) auf Fehler
2. Seite neu laden, Gutschein suchen
3. Vercel Logs ansehen: `vercel logs`

### Problem: Langsam beim Einlösen

**Symptome:** Nach Erstellung dauert Einlösen 5+ Sekunden

**Ursache:** Neon Compute im Autosuspend

**Lösungen:**

1. **Option A: Autosuspend erhöhen**
   - Neon Console → Compute Settings
   - "Autosuspend delay" von 5 auf 30 min erhöhen
   - Kostspieliger, aber bessere UX

2. **Option B: Keepalive Cron**
   - Vercel Cron erstellen, der DB alle 4 min pingt
   - Kostenlos, aber komplexer

### Problem: Inkonsistente Daten

**Symptome:** Gutschein-Saldo stimmt nicht mit Verlauf überein

**Ursache:** Race Condition (2 Einlösungen gleichzeitig)

**Lösungen:**
1. Verlauf des Gutscheins prüfen
2. Vercel Logs auf Transaction-Fehler prüfen
3. Dev kontaktieren für DB-Patch

### Problem: "The new coupon ID already exists"

**Symptome:** Kann nicht tauschen (WECHSEL)

**Ursache:** Neue ID existiert bereits

**Lösungen:**
1. Neue Gutscheinnummer prüfen
2. Wenn Duplikat, per DB löschen
3. Tausch erneut versuchen

---

## 🗄️ Direkter Datenbankzugriff

### Daten via Neon ansehen

**Via Neon Dashboard (Web):**
1. console.neon.tech
2. Projekt wählen
3. **SQL Editor** klicken
4. SQL-Queries schreiben

**Via psql (Command Line):**
```bash
psql "postgresql://benutzer:passwort@host/datenbank"

# Beispiele:
SELECT * FROM "Coupon" WHERE employee = 'Claudio';
SELECT COUNT(*) FROM "Coupon";
SELECT * FROM "CouponHistory" WHERE "couponId" = 'ABC123' ORDER BY "modifiedAt" DESC;
```

### Gutschein löschen

⚠️ Achtung: Nicht rückgängig zu machen

```sql
DELETE FROM "Coupon" WHERE id = 'ABC123';
-- Verlauf wird automatisch gelöscht (CASCADE)
```

### Salden neu berechnen

Wenn Gutschein falschen Saldo anzeigt:

```sql
-- Verlauf prüfen
SELECT * FROM "CouponHistory" WHERE "couponId" = 'ABC123' ORDER BY "modifiedAt" DESC LIMIT 5;

-- Manuell aktualisieren
UPDATE "Coupon" SET "restValue" = 5.00, "usedValue" = 10.00 WHERE id = 'ABC123';
```

### Daten exportieren

Für Analysen oder Berichte:

```bash
# Export als CSV
psql "postgresql://..." -c "COPY (SELECT * FROM \"Coupon\") TO STDOUT WITH CSV HEADER" > gutscheine.csv

# Export als JSON
psql "postgresql://..." --json -c "SELECT * FROM \"Coupon\"" > gutscheine.json
```

---

## 📊 Monitoring

### Performance prüfen

**Vercel Analytics:**
1. vercel.com
2. Projekt wählen
3. **Analytics** Tab
4. Response Times und Fehler sehen

**Vercel Logs:**
```bash
vercel logs --tail
```

### Wichtige Metriken

- **Response Time** : Sollte < 500ms sein
- **Error Rate** : Sollte nahe 0% sein
- **DB Connections** : Neon Console überprüfen

---

## 🚀 Updates durchführen

### Abhängigkeiten aktualisieren

```bash
# Verfügbare Updates prüfen
npm outdated

# Patches aktualisieren (sicher)
npm update

# Spezifische Abhängigkeit
npm install next@latest

# Nach Update lokal testen
npm run dev
npm run build
```

### Update deployen

```bash
git add .
git commit -m "Update dependencies"
git push

# Vercel deployed automatisch
```

### Prisma Migrations

```bash
# Nach schema.prisma Änderung
npx prisma migrate dev --name <beschreibung>

# Migrationen überprüfen
npx prisma migrate status

# In Production
npx prisma migrate deploy
```

---

## 🔄 Deployment Workflow

### Kompletter Prozess

1. **Lokal entwickeln**
   ```bash
   git checkout -b feature/something
   # ... Code-Änderungen ...
   npm run dev
   npm run lint
   ```

2. **Commit und Push**
   ```bash
   git add .
   git commit -m "Beschreibung der Änderungen"
   git push origin feature/something
   ```

3. **Pull Request erstellen** (optional, aber empfohlen)
   - Über GitHub
   - Änderungen reviewen
   - Zu `main` mergen

4. **Vercel überprüfen**
   - Vercel deployed automatisch
   - Build Logs anschauen
   - In Production testen

---

## 📋 Wartungs-Checkliste

**Wöchentlich:**
- [ ] Vercel Fehler prüfen (Analytics)
- [ ] Anomale Gutscheine prüfen
- [ ] Zugriffs-Logs überprüfen

**Monatlich:**
- [ ] Neon Snapshot erstellen
- [ ] Performance überprüfen
- [ ] Passwörter ggf. ändern
- [ ] Daten für Finanzbericht exportieren

**Quartal:**
- [ ] Abhängigkeiten aktualisieren
- [ ] Security Audit
- [ ] Queries optimieren wenn nötig

**Jährlich:**
- [ ] Alte Gutscheine archivieren
- [ ] Datenbank-Kapazität prüfen
- [ ] Vercel/Neon Kosten evaluieren

---

## 📞 Eskalation

Bei Problemen:

1. **Logs prüfen** (Vercel, Browser-Konsole)
2. **Dieses Handbuch** konsultieren
3. **Ursprünglichen Entwickler** kontaktieren bei Bedarf

---

**Version** : 1.0  
**Letzte Aktualisierung** : Oktober 2026  
**Verantwortlich** : [Dein IT-Team]
