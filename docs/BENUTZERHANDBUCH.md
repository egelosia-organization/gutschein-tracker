# Benutzerhandbuch – Gutschein Tracker

Vollständiges Handbuch zur täglichen Verwendung der Anwendung im Laden.

## 🔐 Anmeldung

### Zugriff auf die Anwendung

1. Öffne den Browser und gehe zu: **[URL des Projekts]** (frag den Admin, wenn du sie nicht hast)
2. Du siehst einen Anmeldungsbildschirm mit dem Feld "Passwort"
3. Gib das Passwort **deines Standortes** ein

### Passwörter nach Standort

Jeder Standort hat sein eigenes Passwort:

| Standort | Passwort |
|----------|----------|
| Braugasse | `braugasse` |
| Transit | `transit` |
| Pit Stop | `pitstop` |
| Wirges | `wirges` |
| Büro | `büro` |
| Eiswagen | `eiswagen` |

⚠️ Die Passwörter **unterscheiden nicht zwischen Groß- und Kleinbuchstaben**.

---

## ➕ Gutschein erstellen (Erstellen)

### Schritt 1: Formulär öffnen

1. Klick auf den gelben Button **"Erstellen"** oben links
2. Ein Formular wird angezeigt

### Schritt 2: Informationen ausfüllen

**Erforderliche Felder:**

| Feld | Beschreibung | Beispiel |
|------|-------------|---------|
| **Nummer** | Eindeutige Gutscheinnummer | `A1234B` |
| **Mitarbeiter** | Dein Name (Auto-Vervollständigung) | `Claudio` |
| **Laden** | Dein Standort (Voreinstellung) | `Braugasse` |
| **Betrag** | Wert des Gutscheins in Euro | `10.00` |

### Schritt 3: Absenden

1. Klick auf **"Erstellen"**
2. Du siehst eine grüne Erfolgsmeldung
3. Der Gutschein erscheint sofort in der Liste

---

## 🎯 Gutschein einlösen (Einlösen)

### Schritt 1: Gutschein finden

**Option A: Suche**
- Nutze die Suchleiste oben (links neben der Tabelle)
- Tippe die Gutscheinnummer oder den Mitarbeiternamen ein
- Ergebnisse werden automatisch angezeigt

**Option B: Liste durchsuchen**
- Scroll nach unten durch die Tabelle
- Du kannst nach Spalten sortieren (Nummer, Mitarbeiter, Betrag, etc.)

### Schritt 2: "Einlösen" klicken

Wenn du den Gutschein in der Liste findest:
1. Klick auf den gelben Button **"Einlösen"** rechts neben dem Gutschein
2. Ein Formular wird angezeigt

### Schritt 3: Einlösungsformular ausfüllen

| Feld | Beschreibung | Beispiel |
|------|------------|---------|
| **Betrag verwendet** | Wie viel der Kunde ausgibt | `5.50` |
| **Mitarbeiter** | Dein Name | `Claudio` |
| **Laden** | Dein Standort | `Braugasse` |
| **Trinkgeld** | Trinkgeld (optional) | `0.50` |
| **Neue Nummer** | Neuer Gutschein (optional) | Für Tausch |

### Schritt 4: Bestätigung

1. Klick auf **"Speichern"** oder **"Einlösen"**
2. Wenn der Gutschein komplett aufgebraucht ist → Status wechselt zu "Benutzt" ✓
3. Wenn teilweise → verbleibender Betrag wird angezeigt

---

## 👁️ Verlauf anschauen

### Details eines Gutscheins sehen

1. Klick auf den Gutschein in der Liste
2. Expandiere die Zeile um alle Transaktionen zu sehen

### Dashboard (Übersicht)

Klick auf **"Dashboard"** oben, um zu sehen:
- Gesamt erstellte Gutscheine pro Standort
- Gesamt eingelöste Beträge pro Standort
- Verlauf aller Transaktionen

---

## 📊 Tabelle verstehen

### Hauptspalten

| Spalte | Bedeutung |
|--------|-----------|
| **Nummer (ID)** | Eindeutige Gutscheinnummer |
| **Mitarbeiter** | Wer hat den Gutschein erstellt |
| **Betrag initial** | Originalwert |
| **Verwendet** | Bereits ausgegebener Betrag |
| **Saldo** | Verbleibender Betrag |
| **Status** | ✓ Benutzt (fertig) oder ○ Aktiv |
| **Datum** | Erstellungsdatum |

### Filter und Suche

- **Suchleiste** : Suche nach Gutscheinnummer oder Mitarbeitername
- **Filter** : Sortiere nach Standort, Status, Datum
- **Pagination** : 4 Gutscheine pro Seite (siehe "Next" / "Prev" unten)

---

## ⚠️ Spezialfälle

### "Klein Becher" Gutschein

Der "Klein Becher" ist ein spezieller Gutschein-Typ:
- **Fester Wert** : 2.50€
- **Alles oder nichts** : Entweder verwendet oder nicht
- **Vereinfachtes Formular** : Kein "verwendeter Betrag"

### Gutschein-Tausch (WECHSEL)

Wenn ein Kunde einen alten Gutschein gegen einen neuen tauscht:
1. Füll im Einlösungsformular das Feld **"Neue Nummer"** aus
2. Tippe die Nummer des neuen Gutscheins ein
3. Das System verbindet die zwei Gutscheine mit dem Vermerk "WECHSEL"

### Gutschein mit Rest

Wenn der Kunde nicht alles ausgibt:
1. Der verbleibende Betrag wird nach der Transaktion angezeigt
2. Der Kunde kann ihn später verwenden
3. Der Gutschein-Verlauf zeigt jede Teilzahlung

---

## 🔍 Fehlerbehebung

### "Falsches Passwort"

- Überprüf, dass du das **exakte Passwort** deines Standorts eingegeben hast
- Beachte Umlaute (ü, ö)
- Nicht zwischen Groß- und Kleinbuchstaben

### Gutschein erscheint nach Erstellung nicht

- Warte einige Sekunden (Server aktualisiert)
- Seite neu laden (Strg+R oder F5)
- Suche den Gutschein in der Suchleiste

### "Coupon not found"

- Die Nummer existiert noch nicht
- Überprüf die Schreibweise der Nummer
- Erstelle den Gutschein zuerst, bevor du ihn einlöst

### Verbleibender Betrag ist seltsam

- Dezimalstellen werden gerundet (max. 0.01€)
- Trinkgeld reduziert den Saldo
- Überprüf den Verlauf des Gutscheins um alle Änderungen zu sehen

---

## 💡 Best Practices

✅ **Zu tun:**
- Suche den Gutschein **vor** dem Einlösen
- Gib den **exakten Betrag** aus, den der Kunde ausgibt
- Erfasse **Trinkgelder** zur Nachverfolgung
- Schau den **Verlauf** an bei Unsicherheiten

❌ **Zu vermeiden:**
- Ändere die **Nummer** eines Gutscheins nicht, außer bei Tausch
- Keine **negativen** oder unmöglichen Beträge
- Erstelle keine **Duplikate** (gleiche Nummern)

---

## 📞 Fragen?

Wenn etwas unklar ist oder nicht funktioniert:
1. Versuche die **Fehlerbehebung** oben
2. Lade die Seite komplett neu (Strg+Shift+R)
3. Kontaktiere den **System-Administrator**

---

**Version** : 1.0  
**Letzte Aktualisierung** : Oktober 2026
