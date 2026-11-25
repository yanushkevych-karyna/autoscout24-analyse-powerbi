# AutoScout24 Car Market Explorer – Power-BI-Dashboard

🇬🇧 [English version](README.md)

## Überblick

Ein interaktives Power-BI-Dashboard auf Basis von mehr als 116.000 Fahrzeuginseraten von AutoScout24. Wählt man eine Marke aus, aktualisiert sich die ganze Seite: Kennzahlen, Preis im Vergleich zu allen Marken, Karosserieformen, Kraftstoff- und Getriebearten, Händler im Vergleich zu Privatverkäufern, Preis nach Fahrzeugalter, eine Karte und eine Textzusammenfassung, die sich bei jeder Auswahl neu schreibt.

Dies ist mein erstes Power-BI-Projekt. Ziel war es, einen vollständigen Analyse-Workflow zu üben: Import der Rohdaten, Bereinigung in Power Query, Kennzahlen mit DAX und die Gestaltung eines interaktiven Dashboards.

## Dashboard



## Datensatz

- **Quelle:** [AutoScout24 Car Listings Dataset auf Kaggle](https://www.kaggle.com/datasets/clkmuhammed/autoscout24-car-listings-dataset) (Lizenz: MIT)
- **Rohdaten:** 118.382 Inserate, 75 Spalten
- **Abdeckung:** Inserate aus 8 europäischen Ländern (AT, BE, DE, ES, FR, IT, LU, NL), nach der Bereinigung 24 Marken
- **Inhalt:** Preis, Marke und Modell, Kilometerstand, Erstzulassung, Kraftstoffart, Leistung, Getriebe, Verkäufertyp, Standort, Unfall- und Servicehistorie, Ausstattung u. a.

## Fragestellungen

1. Wie sieht das Angebot aus (Inserate, typischer Preis, Kilometerstand und Alter)?
2. Wie schneidet eine einzelne Marke im Vergleich zu allen Marken ab?
3. Wie unterscheiden sich Händler und Privatverkäufer?
4. Wie sind Karosserieform, Kraftstoffart und Getriebe verteilt?
5. Wie entwickelt sich der Preis mit dem Alter des Fahrzeugs?
6. Wo stehen die Inserate?

## Funktionen des Dashboards

- **Marken-Kacheln:** die 10 Marken mit den meisten Inseraten. Ein Klick aktualisiert alle Visuals der Seite.
- **Länderfilter:** Dropdown, um ein einzelnes Land zu betrachten.
- **Kennzahlen-Karten:** Inserate, Durchschnittspreis, Medianpreis, durchschnittlicher Kilometerstand, durchschnittliches Alter.
- **Medianpreis im Vergleich zu allen Marken:** zeigt, wie weit die gewählte Marke über oder unter dem Median aller Marken liegt.
- **Dynamisches Bild und dynamische Überschrift** der gewählten Marke.
- **„Make Story“:** eine mit DAX erzeugte Textzusammenfassung, die sich für jede gewählte Marke und jedes Land ändert.
- **Diagramme:** Karte der Inserate, Inserate nach Land, Top-5-Modelle, Karosserieform nach Verkäufertyp, Anteil der Kraftstoffarten, Händler im Vergleich zu Privatverkäufern, Getriebeart, Medianpreis nach Fahrzeugalter.

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
- **Fehlende Werte:** Nur das Erstzulassungsdatum war Pflicht, da es für die Berechnung des Fahrzeugalters benötigt wird. Fehlende Leistungs- und Kilometerangaben wurden behalten, damit die Inserate für andere Auswertungen nutzbar bleiben.
- **Ausreißer:** Oldtimer (Erstzulassung vor 2000) sowie extreme Preis-, Leistungs- und Kilometerwerte wurden ausgeschlossen, weil sie Durchschnittswerte verzerren. Ein BMW 316d war z. B. für 9,5 Mio. EUR inseriert, was offensichtlich ein Fehler ist. Sehr teure Supersportwagen wurden bewusst ausgeschlossen, da sich das Projekt auf den normalen Automarkt konzentriert.
- **Importproblem:** Dezimalwerte wie `22950.0` wurden zunächst zehnmal zu groß eingelesen (`229500`), weil die deutschen Regionaleinstellungen den Punkt als Tausendertrennzeichen behandeln. Gelöst wurde das durch die Umstellung der Importsprache auf Englisch (USA).
- **Kilometerstand:** Die Spalte enthielt Text wie `10,236 km`. Die Einheit wurde entfernt und die Spalte in eine Ganzzahl umgewandelt.
- **Währung:** Alle Preise sind in EUR.
- **Kartenkoordinaten:** Einige Koordinaten waren eindeutig falsch (Punkte im Atlantik). Für die Karte wurden Koordinaten außerhalb Westeuropas und der Kanarischen Inseln in zwei neuen Spalten (`lat_map`, `lon_map`) auf leer gesetzt. Die Inserate selbst blieben für alle anderen Auswertungen erhalten.

**Neue Spalten**

- `car_age`: Alter des Fahrzeugs in Jahren, berechnet aus dem Erstzulassungsdatum (Bezugsjahr 2026)
- `price_group`: Preisklassen (unter 10k, 10k–20k, 20k–40k, 40k–80k, 80k+)
- `lat_map`, `lon_map`: bereinigte Koordinaten für die Karte

## DAX-Kennzahlen (Auswahl)

```
Listings     = COUNTROWS(autoscout24_clean)
Median Price = MEDIAN(autoscout24_clean[price])
Avg Mileage  = AVERAGE(autoscout24_clean[mileage_km])
Avg Age      = AVERAGE(autoscout24_clean[car_age])

Market Median Price = CALCULATE([Median Price], ALL(autoscout24_clean[make]))

Price vs Market % =
VAR _one = SELECTEDVALUE(autoscout24_clean[make])
RETURN
    IF(ISBLANK(_one), BLANK(), DIVIDE([Median Price], [Market Median Price]) - 1)

Share of Listings % =
DIVIDE([Listings], CALCULATE([Listings], ALLSELECTED(autoscout24_clean)))
```

Neben dem Durchschnittspreis wird der Medianpreis gezeigt, weil einzelne sehr teure Fahrzeuge den Durchschnitt nach oben ziehen. Weitere Kennzahlen steuern die dynamische Überschrift, das Bild und die Textzusammenfassung (`Selected Make`, `Make Image`, `Make Story`, `Dealer Share %`, `Top Model`, `Top Fuel`).

## Wichtigste Erkenntnisse

- Der Datensatz enthält 116.792 Inserate von 24 Marken. Der Medianpreis liegt bei etwa 39.900 €, der Durchschnittspreis bei etwa 54.000 €. Der Durchschnitt wird durch teure Fahrzeuge nach oben gezogen, deshalb ist der Median das bessere Maß für einen typischen Preis.
- BMW, Porsche, Mercedes-Benz und Audi haben die meisten Inserate. Der Datensatz ist klar auf Premiummarken ausgerichtet.
- Rund 83 % aller Inserate stammen von Händlern, 17 % von Privatverkäufern. Je nach Marke unterscheidet sich das: Bei BMW kommen z. B. 77 %, bei Volvo 74 % der Inserate von Händlern.
- Benzin (44 %) und Diesel (32 %) dominieren. Reine Elektrofahrzeuge machen nur etwa 6 % der Inserate aus.
- Beispiel Volvo: Medianpreis von 16.885 €, das sind 58 % unter dem Median aller Marken. Der durchschnittliche Kilometerstand beträgt 163.000 km und das durchschnittliche Alter 10,9 Jahre, im Vergleich zu 72.000 km und 6,2 Jahren über alle Inserate.
- Beispiel BMW: Medianpreis von etwa 28.000 €, rund 30 % unter dem Median aller Marken, bei einem durchschnittlichen Kilometerstand von 102.000 km.
- In dieser Stichprobe fällt der Medianpreis in den ersten Jahren stark und flacht nach etwa zehn Jahren ab.

## Einschränkungen

- Die Daten zeigen **Angebotspreise** der Inserate, keine tatsächlichen Verkaufspreise, und sind eine Momentaufnahme zu einem Zeitpunkt.
- Der Datensatz enthält nur 24 Marken und ist auf Premiummarken ausgerichtet. Die Ergebnisse sind nicht repräsentativ für den gesamten Automarkt.
- „Alle Marken“ im Dashboard bedeutet alle Marken dieses Datensatzes. Der „Medianpreis im Vergleich zu allen Marken“ hängt daher vom Markenmix der Stichprobe ab.
- Preise aus acht Ländern werden direkt verglichen, obwohl Preisniveau und Steuern abweichen.
- Die Koordinaten scheinen auf ganze Grad gerundet zu sein und beziehen sich auf den Standort des Verkäufers. Die Karte zeigt daher eine grobe Verteilung und keine exakten Orte.
- Modellnamen stammen so, wie die Verkäufer sie eingegeben haben (z. B. `992` für eine Generation des Porsche 911).
- Marken mit wenigen Inseraten (z. B. Aston Martin, Honda, Hyundai) liefern weniger verlässliche Durchschnittswerte.
- Die Filtergrenzen (Jahr, Preis, Kilometerstand, Leistung) sind meine eigenen analytischen Entscheidungen und beeinflussen die Ergebnisse.

## Quellen und Hinweis

Dies ist ein unabhängiges Lernprojekt. Es steht in keiner Verbindung zu AutoScout24 oder einem Automobilhersteller und wird von diesen weder unterstützt noch gesponsert. Alle Markennamen sind Eigentum der jeweiligen Inhaber. Der Datensatz wurde auf Kaggle unter der MIT-Lizenz veröffentlicht; die enthaltenen Inserate stammen von AutoScout24.

- Daten: AutoScout24 Car Listings Dataset auf Kaggle
- Fahrzeugbilder und Illustrationen in der Kopfzeile: mit einem KI-Bildgenerator erstellt. Sie dienen nur der Illustration und zeigen nicht die Fahrzeuge aus den Inseraten.

## Verwendete Tools

Power BI Desktop, Power Query, DAX

## Autorin

**Karyna Yanushkevych**
[LinkedIn](https://www.linkedin.com/in/karyna-yanushkevych-91785a39a/) · [GitHub](https://github.com/yanushkevych-karyna)
