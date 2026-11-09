# Anweisungen an den Coding-Agenten (TAE-Workshop, Vorlage)

Ziel: eine kleine Web-App fürs Ingenieurbüro, rund um Tragwerk und
Nachhaltigkeit. Welches der drei Themen ich baue, steht in meinem ersten
Prompt: Tragwerksmodell → Ökobilanz, Mengenliste → CO2 oder Re-Use-Matcher.
Die Daten liegen in daten/. Nutzer:innen sind Tragwerksplaner:innen. Sie
prüfen jeden Wert von Hand.

## So arbeiten wir
- Kleine Schritte. Nach jedem Schritt das Ergebnis zeigen.
- Bei Unklarheit nachfragen statt raten. Vor dem Lesen einer Datei erst
  ihre Struktur ansehen und mir beschreiben.
- Einheiten immer mitführen und in der Oberfläche anzeigen.
- Emissionsfaktoren NIE schätzen oder aus dem Gedächtnis einsetzen. Jeder
  Faktor wird mit Name, UUID, Modul und deklarierter Einheit angezeigt.
- Überall, wo die App etwas entscheidet, das nicht in den Daten steht:
  Algorithmus, KI, Mensch. Jede Stufe liefert Ergebnis und Konfidenz
  (0 bis 1). Algorithmus und KI entscheiden ab 0,8, darunter ich.
- Ziel der App: Ich kann jedes Ergebnis prüfen. Jedes Element ist
  anklickbar und zeigt, wie es entschieden wurde: welche Regel mit welcher
  Konfidenz, was die KI bekommen und geantwortet hat, oder dass ich es war.
- Die App ist EINE Datei index.html, ohne Server und ohne Installation.
  Bibliotheken nur per CDN (z. B. SheetJS für Excel, three.js für 3D).
  Die Nutzerin lädt ihre Dateien in der App per Dateiauswahl.
- Emissionsfaktoren nur aus faktoren_oekobaudat.csv (aus der ÖKOBAUDAT,
  mit UUID). Keine anderen Werte einsetzen.
- KI-Stufe ohne API-Schlüssel: Du selbst bist die KI. Beim Bauen ordnest du
  die Fälle zu, die der Algorithmus nicht sicher entscheidet, und schreibst
  Ergebnis, Konfidenz und Begründung in eine Datei, die die App liest. Die
  App zeigt sie mit der Quelle „Agent beim Bauen“.
- Arbeite auf dem Repo. Am Ende jedes Schritts committen, pushen und mir
  einen Link geben, unter dem ich index.html im Handy-Browser ansehen kann:
  https://htmlpreview.github.io/?https://github.com/BENUTZER/REPO/blob/BRANCH/index.html
- Die Daten liegen im Repo. Die App darf sie zusätzlich per Dateiauswahl laden.

## Unsere Entscheidungen
(Hier tragt ihr selbst ein, was ihr beim Bauen festlegt: Regeln, Schwellen,
Annahmen. Der Agent liest es bei jedem Start mit.)

## Fakten zu den Daten

### Emissionsfaktoren: daten/gemeinsam/faktoren_oekobaudat.csv
Spalten: kategorie; beschreibung; oekobaudat_uuid; oekobaudat_name; version;
deklarierte_einheit; kg_je_deklarierte_einheit; gwp_bezug; A1-A3; C3; C4; D.
gwp_bezug sagt, ob die Werte je kg (Stahl) oder je m3 (Beton, Holz) gelten.
Dezimalkomma. Kategorien: STAHL_PROFIL, BETONSTAHL, BETON_C25_30,
BETON_C30_37, BETON_C35_45, BSH, VOLLHOLZ, BSP.

## Thema Tragwerksmodell → Ökobilanz (daten/tragwerksmodell)
### Beispiel für eine Regel mit Konfidenz
Regel: neigung >= 75° ± 15° → STUETZE. Konfidenz = 0,5 + (Neigung − 75°) / (2 × 15°),
begrenzt auf 0 bis 1. Eine Schrägstütze mit 72,5° bekommt 0,42, also fragt die
App die KI.

### Datenquelle B: Excel-Export (TAE_*_Export.xlsx)
Blätter: 1.1 Knoten, 1.2 Linien, 1.3 Materialien, 1.13 Querschnitte,
1.17 Stäbe (mit Stabtyp und Gelenken), 1.7 Knotenlager, 2.1 Lastfälle,
2.5 Lastkombinationen, 4.1 Stäbe - Schnittgrößen. Zeile 1 ist der Titel,
Zeilen 2 und 3 sind Kopfzeilen mit zusammengefassten Zellen, Daten ab Zeile 4.
Einheiten stehen im Kopf. Knotenlisten der Linien können Bereiche enthalten.
Zuerst die Struktur jedes Blatts ansehen und mir zeigen, dann erst parsen.

### Datenquelle C: Rhino-Modell (.3dm)
Im Browser mit rhino3dm.js (per CDN). Jeder Stab ist eine Linie, ein Layer je Querschnitt.
User Text am Objekt: Stab, Querschnitt, Material, A_m2, Laenge_m,
Wichte_kN_m3, Stabtyp, Gelenk_Anfang, Gelenk_Ende, Lager_Anfang, Lager_Ende.
Zuerst alle vorhandenen Schlüssel auflisten und prüfen.

## Thema Mengenliste → CO2 (daten/mengenliste)
### Beispiel für eine Regel mit Konfidenz
Stichwörter je Kategorie, z. B. „C30/37“ → BETON_C30_37, „Holz“ oder „KVH“ → VOLLHOLZ.
Konfidenz = Anteil der gefundenen Stichwörter, die auf dieselbe Kategorie zeigen.
„Holz-Beton-Verbunddecke: Aufbeton C25/30“ trifft „Holz“ und „C25/30“, also 0,5:
die KI entscheidet.

### Mengenliste_Beispielprojekt.xlsx
Blatt „Daten“. Zeile 1 Titel, Zeile 2 Hinweis, Kopfzeile in Zeile 4, Daten ab
Zeile 5. Spalten: Pos., Kurztext, Menge, Einheit (m², m³, t, kg, Stk, lfm, m,
psch). Der Kurztext ist Freitext, wie in echten Mengenermittlungen.
Abmessungen stehen oft nur im Text (d=25 cm, 30x60, L=6,50 m).
Positionen ohne Tragwerksmaterial (Schalung, Dämmung, Beschichtung)
bekommen die Kategorie NICHT_BILANZIERT.

### daten/gemeinsam/profile.csv
Stahlprofile: profil; g_kg_je_m; h_mm; b_mm; Wel_y_cm3. Für Positionen in Stück.

## Thema Re-Use-Matcher (daten/reuse)
### Beispiel für eine Regel mit Konfidenz
Enthält die Beschreibung genau einen Profilnamen aus profile.csv (IPE 300,
HEB 200 …), ist die Konfidenz 1,0. Jedes Unsicherheitszeichen („?“, „ca.“,
„unleserlich“) halbiert sie. „IPE 330 (?)“ bekommt 0,5, also fragt die App die KI.

### Bauteilboerse_Rueckbau.xlsx (Angebot)
Blatt „Daten“, Kopfzeile in Zeile 4, Daten ab Zeile 5. Spalten: Nr.,
Beschreibung (Freitext, unsauber), Länge [m], Stück, Zustand, Herkunft.

### Bedarf_Neubau.xlsx (Bedarf)
Blatt „Daten“, Kopfzeile in Zeile 4. Spalten: Pos., Bauteil,
erf. Wel,y [cm³], Länge [m], Stück.

### daten/gemeinsam/profile.csv
Stahlprofile: profil; g_kg_je_m; h_mm; b_mm; Wel_y_cm3. Ein Altteil passt,
wenn Wel,y >= erforderlich und Länge >= benötigt (kürzen geht, verlängern
nicht). Jedes Stück nur einmal verwenden.

### CO2-Einsparung (Annahme, in der App sichtbar machen)
Eingespart = Masse der verwendeten Länge x A1-A3 von STAHL_PROFIL aus
faktoren_oekobaudat.csv. Aufbereitung und Transport werden vernachlässigt.
