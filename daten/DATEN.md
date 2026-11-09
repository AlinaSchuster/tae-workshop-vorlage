# Fakten zu den Daten (zum Nachschlagen)

Alle Daten sind erfundene Beispiele für den TAE-Workshop. Emissionsfaktoren aus der ÖKOBAUDAT (BMWSB), mit UUID.

## Emissionsfaktoren: gemeinsam/faktoren_oekobaudat.csv
Spalten: kategorie; beschreibung; oekobaudat_uuid; oekobaudat_name; version;
deklarierte_einheit; kg_je_deklarierte_einheit; gwp_bezug; A1-A3; C3; C4; D.
gwp_bezug sagt, ob die Werte je kg (Stahl) oder je m3 (Beton, Holz) gelten.
Dezimalkomma. Kategorien: STAHL_PROFIL, BETONSTAHL, BETON_C25_30,
BETON_C30_37, BETON_C35_45, BSH, VOLLHOLZ, BSP.

## Thema Tragwerksmodell → Ökobilanz (tragwerksmodell/)

### Datenquelle B: Excel-Export (TAE_*_Export.xlsx)
Blätter: 1.1 Knoten, 1.2 Linien, 1.3 Materialien, 1.13 Querschnitte,
1.17 Stäbe (mit Stabtyp und Gelenken), 1.7 Knotenlager, 2.1 Lastfälle,
2.5 Lastkombinationen, 4.1 Stäbe - Schnittgrößen. Zeile 1 ist der Titel,
Zeilen 2 und 3 sind Kopfzeilen mit zusammengefassten Zellen, Daten ab Zeile 4.
Einheiten stehen im Kopf. Knotenlisten der Linien können Bereiche enthalten.
Zuerst die Struktur jedes Blatts ansehen und mir zeigen, dann erst parsen.

### Datenquelle C: Rhino-Modell (.3dm)
Im Browser mit rhino3dm.js. Jeder Stab ist eine Linie, ein Layer je Querschnitt.
User Text am Objekt: Stab, Querschnitt, Material, A_m2, Laenge_m,
Wichte_kN_m3, Stabtyp, Gelenk_Anfang, Gelenk_Ende, Lager_Anfang, Lager_Ende.
Zuerst alle vorhandenen Schlüssel auflisten und prüfen.

### testfaelle.csv (Bonus)
20 Materialnamen mit Soll-Kategorie: nr; materialname; soll_kategorie; anmerkung.

## Thema Mengenliste → CO2 (mengenliste/)

### Mengenliste_Beispielprojekt.xlsx
Blatt „Daten“. Zeile 1 Titel, Zeile 2 Hinweis, Kopfzeile in Zeile 4, Daten ab
Zeile 5. Spalten: Pos., Kurztext, Menge, Einheit (m², m³, t, kg, Stk, lfm, m,
psch). Der Kurztext ist Freitext, wie in echten Mengenermittlungen.
Abmessungen stehen oft nur im Text (d=25 cm, 30x60, L=6,50 m).
Positionen ohne Tragwerksmaterial (Schalung, Dämmung, Beschichtung)
bekommen die Kategorie NICHT_BILANZIERT.

### profile.csv
Stahlprofile: profil; g_kg_je_m; h_mm; b_mm; Wel_y_cm3. Für Positionen in Stück.

## Thema Re-Use-Matcher (reuse/)

### Bauteilboerse_Rueckbau.xlsx (Angebot)
Blatt „Daten“, Kopfzeile in Zeile 4, Daten ab Zeile 5. Spalten: Nr.,
Beschreibung (Freitext, unsauber), Länge [m], Stück, Zustand, Herkunft.

### Bedarf_Neubau.xlsx (Bedarf)
Blatt „Daten“, Kopfzeile in Zeile 4. Spalten: Pos., Bauteil,
erf. Wel,y [cm³], Länge [m], Stück.

### profile.csv
Stahlprofile: profil; g_kg_je_m; h_mm; b_mm; Wel_y_cm3. Ein Altteil passt,
wenn Wel,y >= erforderlich und Länge >= benötigt (kürzen geht, verlängern
nicht). Jedes Stück nur einmal verwenden.

### CO2-Einsparung (Annahme, in der App sichtbar machen)
Eingespart = Masse der verwendeten Länge x A1-A3 von STAHL_PROFIL aus
faktoren_oekobaudat.csv. Aufbereitung und Transport werden vernachlässigt.
