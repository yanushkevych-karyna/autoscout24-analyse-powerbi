# AutoScout24 Fahrzeuginserate – Power-BI-Dashboard
 
🇬🇧 [English version](README.md)
 
## Überblick
 
Dieses Projekt ist mein erstes Power-BI-Dashboard. Es analysiert mehr als 116.000 Fahrzeuginserate von AutoScout24, um die Struktur des Marktes zu verstehen und herauszufinden, welche Faktoren den Angebotspreis beeinflussen (Kilometerstand, Alter, Marke, Kraftstoffart, Verkäufertyp u. a.).
 
Ziel war es, einen vollständigen Analyse-Workflow zu üben: Import der Rohdaten, Bereinigung in Power Query, Berechnung von Kennzahlen mit DAX und Darstellung der Ergebnisse in einem interaktiven Dashboard.
 
## Dashboard
 

## Datensatz
 
- **Quelle:** [AutoScout24 Car Listings Dataset auf Kaggle](https://www.kaggle.com/datasets/clkmuhammed/autoscout24-car-listings-dataset)
- **Umfang:** 118.382 Inserate, 75 Spalten (Rohdaten)
- **Inhalt:** Preis, Marke und Modell, Kilometerstand, Erstzulassung, Kraftstoffart, Leistung, Getriebe, Verkäufertyp, Standort, Unfall- und Servicehistorie, Ausstattung u. a.
## Fragestellungen
 

## Datenbereinigung (Power Query)
 
| Schritt | Verbleibende Zeilen |
|---|---|
| Rohdaten | 118.382 |
| Inserate ohne Erstzulassungsdatum entfernt | 118.161 |
| Nur Erstzulassungen ab 01.01.2000 | 118.047 |
| Nur Motorleistung zwischen 30 und 1.500 PS | 118.008 |
| Preise unter 500 EUR entfernt | 117.949 |
| Preise über 300.000 EUR und Kilometerstand über 500.000 km entfernt | 117.246 |
| Nur Pkw (Transporter entfernt) | 116.792 |
 
**Entscheidungen und Begründungen**
 
- **Entfernte Spalten:** 26 Spalten wurden gelöscht, weil sie fast oder komplett leer waren (z. B. Garantie, Verbrauch innerorts und außerorts, VIN) oder für die Analyse nicht relevant sind (z. B. Beschreibung, Straße, Ausstattungslisten).
- **Fehlende Werte:** Fehlende Werte wurden nur beim Erstzulassungsdatum entfernt, da es für die Berechnung des Fahrzeugalters benötigt wird. Fehlende Leistungs- und Kilometerangaben wurden behalten, damit die Inserate für andere Auswertungen nutzbar bleiben.
- **Ausreißer:** Oldtimer (Erstzulassung vor 2000) sowie extreme Preis-, Leistungs- und Kilometerwerte wurden ausgeschlossen, weil sie Durchschnittswerte verzerren. Ein BMW 316d war z. B. für 9,5 Mio. EUR inseriert, was offensichtlich ein Fehler ist. Sehr teure Supersportwagen wurden bewusst ausgeschlossen, da sich das Projekt auf den normalen Automarkt konzentriert.
- **Importproblem:** Dezimalwerte wie `22950.0` wurden zunächst zehnmal zu groß eingelesen (`229500`), weil in den deutschen Regionaleinstellungen der Punkt als Tausendertrennzeichen gilt. Gelöst wurde das durch die Umstellung der Importsprache auf Englisch (USA).
- **Kilometerstand:** Die Spalte enthielt Text wie `10,236 km`. Die Einheit wurde entfernt und die Spalte in eine Ganzzahl umgewandelt.
- **Währung:** Alle Preise sind in EUR.
**Neue Spalten**
 
- `car_age`: Alter des Fahrzeugs in Jahren, berechnet aus dem Erstzulassungsdatum (Bezugsjahr 2026)
- `price_group`: Preisklassen (unter 10k, 10k–20k, 20k–40k, 40k–80k, 80k+)
## Datenmodell und DAX-Kennzahlen
 

## Dashboard-Seiten
 
 
## Wichtigste Erkenntnisse
 

## Einschränkungen
 
- Die Daten zeigen **Angebotspreise** der Inserate, keine tatsächlichen Verkaufspreise.
- Die Daten sind eine Momentaufnahme zu einem Zeitpunkt.
- Die Filtergrenzen (Jahr, Preis, Kilometerstand, Leistung) sind meine eigenen analytischen Entscheidungen und beeinflussen die Ergebnisse.
## Verwendete Tools
 
Power BI Desktop, Power Query, DAX
