# Konzept: Kursanmeldung & Rechnungen – lisa-helbig.de

Stand: 09.11.2026 · Grundlage: Lisas Antworten zum Fragenkatalog, ihre Canva-Rechnungsvorlage (Nr. 0208), Backend-Sichtung

## Entscheidung: eigene Lösung statt Amelia

Amelia passt nicht zu Lisas Abrechnungsmodell:
- Krabbelkurse werden **rückwirkend für zwei Monate nach Anzahl der Einheiten** abgerechnet, Babymassage als **6er-Block**. Amelia rechnet pro Buchung zum Buchungszeitpunkt ab.
- Lisa braucht **ihre bestehende Rechnungsvorlage** und **einen durchgehenden Nummernkreis** (ab 0209ff.) für Kurse *und* Einzelberatungen. Das leistet Amelias Rechnungsfunktion nicht.
- Online-Zahlung ist nicht gewünscht, und damit fällt Amelias größter Vorteil weg.

Gebaut wird auf dem vorhandenen Stack: **ACF Pro** (Daten), **Bricks** (Frontend), **Fluent Forms Pro** (Anmeldeformular), **FluentSMTP/All-Inkl** (Mailversand), eigener Code als kleines Plugin. Amelia kann nach dem Go-live deaktiviert werden.

## Rahmenbedingungen (von Lisa)

| Thema | Vorgabe |
|---|---|
| Steuer | Kleinunternehmerin, Hinweis nach § 19 UStG, keine USt |
| Buchhaltung | kein Tool, Rechnungen bisher per Canva |
| Rechnungsnummern | 4-stellig, letzte 0208, im November folgen noch manuelle Rechnungen |
| Absender | Lisa Helbig · Das HebammenEck · Dasbecker Weg 82 · 59073 Hamm-Heessen |
| Zahlung | Überweisung, Zahlungsziel **7 Tage** (Vorlage nennt noch 14) |
| Teilnehmende | nur Privatpersonen, 4–12 pro Kurs |
| Mindestzahl | 4 (bei Online-Kursen flexibel), Entscheidung spätestens 2 Tage vor Start |
| Höchstzahl | keine, aber Warteliste gewünscht (siehe offene Fragen) |
| Storno | bis 7 Tage vorher kostenlos, danach 50 %, nach Kursstart + Rechnung 100 %; Ersatzperson erlaubt |
| Pflichtdaten | Name, Adresse, E-Mail, Telefon; optional Name + Alter des Kindes |
| Volumen | 3–4 feste Kurse/Woche + 2–6 flexible Kurse/Monat |
| Erinnerung | ja, Mail vor Kursbeginn |
| Externe Events | Honorarbasis, nur Verlinkung, keine Anmeldung |

## Kursarten

1. **Workshop**: ein Termin, Festpreis (z. B. Beikost & BLW, 30–35 €). Rechnung nach Zusage.
2. **Kursblock**: feste Anzahl Einheiten, Blockpreis (Babymassage = 6 Einheiten). Rechnung nach Zusage für den ganzen Block.
3. **Fortlaufender Kurs**: wöchentlich (Krabbelkurs / Eltern-Kind-Kurs nach QEKK). Abrechnung je **2-Monats-Zeitraum** = Anzahl angebotener Einheiten × Preis pro Einheit.

## Ablauf

```
Anmeldung (Website-Formular)
   → Bestätigungsmail an TN + Info-Mail an Lisa
   → Status "angemeldet" (oder "Warteliste")
Lisa entscheidet ≤ 2 Tage vorher: "Kurs findet statt" / "Kurs fällt aus"
   ├─ findet statt → Knopf "Rechnungen senden": PDF je TN, Mail mit PDF, Status "Rechnung versendet"
   └─ fällt aus  → Knopf "Absage senden": Absagemail an alle, keine Rechnung, kein Geld zurückzuzahlen
Erinnerungsmail X Tage vor Termin (automatisch)
Zahlungseingang: Lisa hakt "bezahlt" ab (optional: Zahlungserinnerung nach 7 Tagen)
```

**Storno durch TN:** Lisa setzt den TN in der Liste auf "storniert". Das System berechnet die Gebühr aus dem Datum (0 % / 50 % / 100 %). Ist schon eine Rechnung raus: Stornorechnung (eigene Nummer) + ggf. neue Rechnung über 50 %. Rechnungen werden nie gelöscht.

**Ersatzperson:** TN tauschen, Rechnung auf die neue Person (bzw. Storno + Neuausstellung, falls schon versendet).

**Warteliste:** Rückt jemand nach, bekommt die Person eine Mail.

## Bausteine

1. **Beitragstyp "Termin"** (ACF) ersetzt die heutige Repeater-Liste: Titel, Kursart, Termin(e), Dauer, Ort/Zoom-Link, Preis (als Zahl, pro Kurs/Block/Einheit), Mindest-/Höchstzahl, Status, intern/extern + externe URL. Die Termine-Seite in Bricks liest künftig daraus, die Optik bleibt.
2. **Anmeldeformular** (Fluent Forms): am Termin eingebunden, der Termin wird automatisch zugeordnet, Datenschutz-Checkbox, Bestätigungsmails.
3. **Teilnehmer-Verwaltung im Backend** pro Termin: Liste mit Status, Knöpfe "Findet statt → Rechnungen senden", "Fällt aus → Absage senden", Storno, Ersatzperson, bezahlt, CSV-Export.
4. **Rechnungsmodul**:
   - Lisas Canva-Layout als PDF-Vorlage nachgebaut (Logo, Farben, Unterschrift)
   - fortlaufende Nummer, Startnummer beim Go-live einstellbar
   - Pflichtangaben einer Kleinbetragsrechnung (≤ 250 € brutto): Name + Anschrift Lisa, Datum, Leistung + Leistungsdatum/-zeitraum, Betrag, § 19-Hinweis
   - **über 250 €**: zusätzlich Steuernummer, Rechnungsnummer (ist ohnehin drauf), Anschrift Empfänger. Steuernummer am besten immer aufnehmen.
   - **Manuelle Rechnung** für Einzelberatungen (Still-/Schlafberatung), damit Canva wegfällt und es nur einen Nummernkreis gibt
   - PDFs werden dauerhaft gespeichert (10 Jahre Aufbewahrung), Jahresexport als ZIP/CSV für die EÜR
5. **Mails**: Anmeldebestätigung, Wartelistenbestätigung/Nachrücker, Rechnung, Absage, Erinnerung, ggf. Zahlungserinnerung. Texte kann Lisa im Backend bearbeiten.

## Umsetzung

1. Staging-Kopie mit WPvivid Pro anlegen, Entwicklung nur dort
2. Beitragstyp "Termin" + Übernahme der bestehenden Termine, Termine-Seite umstellen
3. Formular + Teilnehmerverwaltung
4. Rechnungsmodul + Mails + Erinnerungen
5. Test mit Lisa auf Staging (Testanmeldungen, Testrechnungen mit Testnummernkreis)
6. Go-live, Startnummer setzen, Amelia deaktivieren

## MVP-Zuschnitt (Ziel: wenige Stunden)

**Drin:**
- Beitragstyp "Termin" + Skript, das die 29 bestehenden Repeater-Einträge übernimmt
- Fluent-Forms-Anmeldeformular, Termin wird automatisch zugeordnet, Standard-Bestätigungsmail
- Pro Termin eine Teilnehmerliste mit Status und zwei Knöpfen: "Rechnungen senden" / "Absage senden"
- Fortlaufender Kurs: Beim Klick auf "Rechnungen senden" gibt Lisa Zeitraum + Anzahl Einheiten ein, keine automatische Berechnung
- Rechnungs-PDF im Canva-Layout, fortlaufende Nummer, Versand per Mail, PDF wird gespeichert
- Manuelle Rechnung (für Beratungen und Stornogebühren) über ein schlichtes Formular
- "Bezahlt"-Haken
- Erinnerungsmail per WP-Cron, fester Vorlauf

**Bewusst später / manuell:**
- Storno: Lisa setzt den Status und stellt eine 50-%-Rechnung bei Bedarf manuell aus (keine automatische Gebührenlogik)
- Ersatzperson: Eintrag bearbeiten
- Warteliste: nur Status "Warteliste" bei optionaler Höchstzahl, Nachrücken per Hand
- Zahlungserinnerungen, Jahresexport, editierbare Mailtexte im Backend

## Offene Fragen an Lisa

1. **Warteliste ohne Höchstzahl**: Sollen wir pro Kurs eine *optionale* Höchstzahl einstellbar machen? Die Warteliste greift dann nur dort.
2. **Krabbelkurs**: Wer mitten im 2-Monats-Zeitraum einsteigt, zahlt der nur die restlichen Einheiten? Werden verpasste Einheiten trotzdem berechnet?
3. **Preise**: Preis pro Einheit beim Krabbelkurs, Blockpreis Babymassage, Preise der übrigen Kurse.
4. **Erinnerung**: Wie viele Tage vor Kursbeginn? Bei laufenden Kursen nur vor dem ersten Termin?
5. **Einzelberatungen**: Sollen auch die Rechnungen für Beratungen über das neue System laufen? (Empfehlung: ja, ein Nummernkreis)
6. **Steuernummer**: Bitte mitteilen, damit sie auf jede Rechnung kommt (Pflicht ab 250 €, z. B. bei längeren Blöcken).
7. **Zahlungserinnerung**: Automatisch nach Ablauf der 7 Tage, oder lieber nur eine Liste der offenen Posten für dich?
8. **Rechnungsvorlage**: Bitte Logo und Unterschrift als Datei (PNG/SVG) schicken, am besten auch den Canva-Export als PDF.
