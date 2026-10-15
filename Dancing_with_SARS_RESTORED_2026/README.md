# Dancing with SARS — ORIGINAL DESIGN RESTORED + KI / 3D Moon

**NASA Space Apps Challenge 2026 — Team `00000000A_LAGRANGE_NEXUS`**

Das ist bewusst **keine Neugestaltung als fremde App**: Das ursprüngliche dunkle Mission-Control-Design, **die Mondansicht**, das SAR/CPR-Konzept, alle **fünf Zielpunkte**, die Telemetrie, PSR- und Gefahrenzonen sind wieder im Mittelpunkt. Die Mission-Browser-Funktionen sind **zusätzlich** eingefügt.

## Sofort starten

1. ZIP entpacken; `index.html` doppelklicken. **Keine Installation, keine CDN-Verbindung nötig.**
2. Rechts oben im Kartenbereich zwischen **2D Südpol**, **2D ganzer Mond** und **3D Mond** wechseln.
3. Links ist dauerhaft ein **drehbarer 3D-Mond**. Mit Maus/Finger ziehen; bei schwacher/gesperrter Grafik nutzt das System ein Software-Rendering.
4. **SAR**-Buttons wechseln die PSR-/Hazard-Anzeigen; Zielpunkte anklicken, dann aktualisieren sich Telemetrie, KI-Demo und 3D-Mond.
5. Datum, Zeitfenster und Zeitregler steuern die Modell-Sonnen-/Erddaten. Die KI-Grafik reagiert auf die berechneten Daten.
6. **Empfohlen für importierte NASA/JPL-Daten:** `START_WINDOWS.bat` oder `start_mac_linux.sh` ausführen (Python 3 muss installiert sein), dann `http://localhost:8000` öffnen.

Die neue Version funktioniert lokal **auch ohne Three.js oder Leaflet**: Die 3D-Ansicht wird direkt mit WebGL erzeugt und fällt bei fehlendem WebGL auf ein interaktives Software-Rendering zurück.

## Wo ist die KI?

Im linken Panel **"KI MISSION ANALYST"** unter der 3D-Mondansicht. Der Analyzer ist **ein echtes lokal berechnetes Machine-Learning-Modell** (logistische Regression):

- Eingaben: **Hangneigung**, **Anteil geometrischer Stunden mit Sonne**, **Anteil geometrischer Stunden mit Erde**, **Demo-Mindesttemperatur**.
- Ausgabe: Modell-Score **0–100** für einen **synthetischen Vergleichsfall** sowie positive/negative Einflussfaktoren und die höchste Modell-Bewertung aller fünf Orte.
- Training: 12.000 **synthetische** Trainingsfälle. Code: `tools/train_demo_ai.py`. Gewichte: `assets/ml-model.json` und die offline-fähige `assets/ml-model.js`.
- Inferenz: lokal in `assets/app.js`, **ohne API-Schlüssel**, ohne Internet, ohne externen KI-Dienst.

> **Wissenschaftliche Ehrlichkeit:** Es gibt aktuell **keine KI, die mit echten NASA-Landungsbeobachtungen trainiert wurde**. Das Modell ist für die Demonstration einer ML-Architektur implementiert. **0–100 ist keine reale Landewahrscheinlichkeit, keine Eiswahrscheinlichkeit und keine Missionsfreigabe.** Bitte vor der Einreichung mit validierten Datensätzen trainieren, kalibrieren und testen.

## Wo ist der Mond?

- Im linken Panel: **3D Orbitale Mondansicht**, laufend animiert, per Maus/Finger drehbar.
- Rechts: **2D Südpol**, **2D gesamter Mond**, **großer 3D-Mond**.
- Die Hintergrundtexturen liegen offline in `assets/moon_illustrative.jpg` und `assets/south_pole_illustrative.jpg`.
- **Diese Texturen sind illustrativ und prozedural erzeugt; sie sind KEIN echtes NASA-Foto / LROC-Datensatz.** Code zur Regeneration: `tools/generate_demo_texture.py` (benötigt numpy, scipy, Pillow); die gelieferten JPGs brauchen das nicht.
- NASA stellt eine echte Mondtextur bereit: [NASA Scientific Visualization Studio CGI Moon Kit](https://svs.gsfc.nasa.gov/4720) (`lroc_color_2k.jpg`). Das ist eine gute Quelle für den nächsten visuellen wissenschaftlichen Schritt; die Textur wurde **nicht** ungeprüft als NASA-Aufnahme beigepackt.

## Echtes JPL-Horizons-Format (optional)

Ein vorhandenes Python-Skript holt topozentrische Azimut/Elevations-Zeitreihen für Sonne und Erde über die offizielle NASA/JPL-API. Der Abruf benötigt Internet, die hier bereitgestellte ZIP enthält **keine bereits live verifizierten JPL-Ephemeriden**.

```bash
python tools/fetch_horizons.py --start 2026-11-14 --days 7 --step-hours 1
```

Es wird bei Erfolg `data/horizons.json` erzeugt. Die Webseite lädt die Datei automatisch, wenn sie über einen lokalen Webserver läuft, oder du verwendest **JPL JSON IMPORT**. Andere Intervalle als vollständige Stunden werden absichtlich nicht interpoliert. Fehlen Werte, steht **N/A**, ohne erfundene Fallback-Zahlen. Die Nutzerdatei ist nur auf Struktur geprüft; eine unabhängig verifizierte NASA-Herkunft kann der Browser nicht garantieren.

**Ohne Import** zeigt die Webseite `MODELL / DEMO`: ein didaktisches periodisches Sonne/Erde-Modell ohne korrekte Ephemeridenphase. Ausgaben sind keine realen Beobachtungen für den ausgewählten Tag.

Es gilt stets der ideale lokale Horizont 0° und nicht eine aus LRO/LOLA-Höhendaten berechnete Gelände-Horizontmaske. Deshalb bedeuten "Sonne über Horizont" und "Erde über Horizont" keine gesicherte Stromversorgung oder Funkverbindung.

## Ursprungsdaten und Einschränkungen

- `data/data.json` ist die **unveränderte Original-JSON**; Markierungen und Werte stammen aus dem bereitgestellten Prototyp und sind **nicht verifizierte NASA-Messwerte**.
- `original/` enthält die **vier unveränderten Ausgangsdateien**.
- Die Radar-Polygone sind Original-Demozonen. Die Buttons sind echte Anzeigeumschalter, **keine** Pixel-basierten SAR-Rohdaten-Layer.
- Erhöhtes CPR kann an Wassereis liegen, kann aber ebenso durch raues Gelände entstehen: Ein hoher CPR-Wert ist **kein Wassereis-Beweis**.
- Die Landepunkte und deren Hang-/Thermalwerte müssen vor einer wissenschaftlichen Submission anhand offizieller NASA-Daten geprüft werden.
- Bitte vor dem Hackathon mit den Organisatoren klären, welche schon vorher entstandenen Projektteile zulässig sind.

## Dateien

```text
index.html                      Ursprünglicher Control-Room-Stil mit erweiterten Panels
assets/app.js                   Mondkarte, Missionsvergleich, KI-Auswertung, CSV, JPL-Import
assets/moon-webgl.js            Interaktive 3D-Mond-Engine, CPU-Fallback
assets/astro.js                 Zeitgeometrie + gekennzeichnetes Demo-Modell
assets/ml-model.js              Offline-Gewichte des trainierten Demo-ML-Modells
assets/ml-model.json            Lesbare Gewichte / Herkunft / Merkmale
assets/default-data.js         Originaldaten als lokale file://-Fallback-Datei
assets/*.jpg                    Offline-Texturillustrationen (keine NASA-Bilder)
data/data.json                 Ursprüngliche Zielpunkte und Polygone
data/sites.json                Koordinaten für optionalen JPL-Abruf
tools/fetch_horizons.py         NASA/JPL Horizons API-Export
tools/train_demo_ai.py          Nachvollziehbare Erzeugung des KI-Demo-Modells
tools/generate_demo_texture.py  Illustrationstexturen generieren
original/                       Originaldateien zur Rückverfolgung
START_WINDOWS.bat               Lokaler Webserver Windows
start_mac_linux.sh              Lokaler Webserver macOS/Linux
```

## Qualitätssicherung

JavaScript prüfen: `node --check assets/app.js && node --check assets/moon-webgl.js`. Test der Rechenfunktionen: `node tests/astro.test.js` (siehe Datei). Die UI wurde in Chromium mittels gerenderter Seitenelemente, Canvas-Fallback und Klicktests geprüft. **Ein echter Internetabruf von NASA-Daten konnte in der Bearbeitungsumgebung nicht ausgeführt werden.** WebGL-Unterstützung ist geräteabhängig; wenn sie fehlt, greift das 2D-Software-Rendering, das per Maus ebenfalls drehbar ist.

Weitere Quellen: [NASA/JPL Horizons API](https://ssd-api.jpl.nasa.gov/doc/horizons.html), [NASA LRO](https://science.nasa.gov/mission/lro/).
