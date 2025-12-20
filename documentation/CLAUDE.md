# CLAUDE.md - BBL GIS Immobilienportfolio

## Projektübersicht

**BBL Immobilienportfolio - GIS POC** ist eine Single-Page-Web-Anwendung zur Verwaltung und Visualisierung des Schweizer Immobilienportfolios des Bundesamt für Bauten und Logistik (BBL).

**Live-Demo:** https://davras5.github.io/gis-immo/

### Hauptfunktionen
- Interaktive Kartenvisualisierung mit 3 Kartenstilen (Light, Standard, Satellite)
- Vier Anzeigemodi: Map, List, Gallery, Detail
- Detailansicht mit 7 Tabs: Übersicht, Bemessungen, Dokumente, Kosten, Verträge, Kontakte, Ausstattung
- Ortssuche via Swisstopo API
- URL-basierte Navigation mit Deep-Linking
- Export-Funktionen (CSV, JSON, PDF)
- Responsive Design mit Accessibility-Features

## Tech-Stack

| Technologie | Version | Verwendung |
|-------------|---------|------------|
| Vanilla JavaScript | ES6+ | Anwendungslogik |
| Mapbox GL JS | v3.4.0 | Kartenvisualisierung mit WebGL |
| CSS3 | - | Styling (Flexbox, Grid, CSS Variables) |
| GeoJSON | - | Datenformat für Geodaten |
| Swisstopo API | - | Ortssuche (Locations + Layers) |
| Geo Admin API | - | Schweizer Geodaten-Katalog |
| Material Symbols | Google Fonts | Icon-Bibliothek |

**Wichtig:** Keine Build-Tools, keine Frameworks - reine statische Dateien.

## Projektstruktur

```
gis-immo/
├── index.html                    # Komplette App (HTML + CSS + JavaScript, 268 KB)
├── data/
│   └── buildings.geojson         # Portfolio-Daten (10+ Gebäude, 85 KB)
├── assets/
│   └── images/
│       ├── preview1.jpg          # Screenshot Map-View
│       ├── preview2.jpg          # Screenshot List-View
│       └── preview3.jpg          # Screenshot Detail-View
├── documentation/
│   ├── DATAMODEL.md              # Detailliertes Datenmodell (82 KB)
│   └── CLAUDE.md                 # Diese Datei
├── README.md                     # Projekt-README
└── LICENSE                       # MIT-Lizenz
```

## Lokale Entwicklung

```bash
# Python
python -m http.server 8000

# Node.js
npx http-server

# PHP
php -S localhost:8000
```

Dann http://localhost:8000 öffnen.

### Browser-Anforderungen
- WebGL-Unterstützung (für Mapbox GL JS)
- ES6+ JavaScript
- LocalStorage
- Modernes CSS (Grid, Flexbox, Variables)

## Wichtige Konfiguration

### Mapbox Token (index.html, ~Zeile 4254)
```javascript
mapboxgl.accessToken = 'pk.eyJ1IjoiZGF2aWRyYXNuZXI1IiwiYSI6...'
```

### CSS Design-System (index.html, Zeilen 14-130)
```css
:root {
    /* Farben */
    --primary-red: #c00;
    --grey-900: #2D3236;
    --accent-panel: #6C757D;

    /* Status-Farben */
    --status-active: #2e7d32;      /* Grün - In Betrieb */
    --status-renovation: #ef6c00;   /* Orange - In Renovation */
    --status-planning: #1976d2;     /* Blau - In Planung */
    --status-inactive: #6C757D;     /* Grau - Ausser Betrieb */

    /* Typografie */
    --text-xs: 0.75rem;
    --text-sm: 0.875rem;
    --text-base: 1rem;
    --text-lg: 1.125rem;
    --text-xl: 1.25rem;
    --text-2xl: 1.5rem;

    /* Spacing */
    --space-1: 0.25rem;
    --space-2: 0.5rem;
    --space-4: 1rem;
    --space-8: 2rem;

    /* Radien & Touch Targets */
    --radius-sm: 4px;
    --radius-md: 8px;
    --radius-lg: 12px;
    --touch-target-min: 44px;
}
```

### Status-Farben
| Status | Farbe | CSS Variable |
|--------|-------|--------------|
| In Betrieb | `#2e7d32` (Grün) | `--status-active` |
| In Renovation | `#ef6c00` (Orange) | `--status-renovation` |
| In Planung | `#1976d2` (Blau) | `--status-planning` |
| Ausser Betrieb | `#6C757D` (Grau) | `--status-inactive` |

## Datenmodell (buildings.geojson)

Siehe [DATAMODEL.md](./DATAMODEL.md) für das vollständige Datenmodell.

### Gebäude-Entity (Hauptfelder)
```javascript
{
  // Identifikation
  id: "BBL-001",
  name: "Bundeshaus West",
  egid: "301001234",              // Eidgenössischer Gebäudeidentifikator
  egrid: "CH123456789012",        // Eidgenössischer Grundstücksidentifikator

  // Adresse
  adresse: "Bundesplatz 3, 3003 Bern",
  hausnummer: "3",
  plz: "3003",
  ort: "Bern",
  region: "Kanton Bern",
  land: "CH",

  // Organisation
  teilportfolio: "Verwaltungsgebäude",
  teilportfolio_gruppe: "Bundesverwaltung",
  verantwortlich: "Anna Müller",

  // Gebäudedetails
  baujahr: 1902,
  geschosse: 5,
  flaeche_ngf: 12500,             // Nutzfläche m²
  denkmalschutz: "Ja",
  parkplaetze: 45,
  ladestationen: 8,

  // Status & Energie
  status: "In Betrieb",
  sanierung: "2019",
  energieklasse: "C",
  waermeerzeuger: "Fernwärme",

  // Grundstück
  grundstueck_name: "Bundesplatz Parzelle A",
  grundstueck_id: "BE-3003-1001",
  eigentum: "Eigentum Bund",

  // Gültigkeit
  gueltig_von: "1902-06-01",
  gueltig_bis: null
}
```

### Verschachtelte Entitäten

| Entity | Beschreibung | Felder |
|--------|--------------|--------|
| **bemessungen** | Flächenmessungen nach SIA | areaType, value, unit, accuracy, standard, source, validFrom, validUntil |
| **dokumente** | Dokumente & Pläne | name, type, url, date, size |
| **kontakte** | Ansprechpersonen | name, rolle, organisation, telefon, email |
| **vertraege** | Verträge | name, partner, beginn, ende, betrag, status |
| **ausstattung** | Gebäudeausstattung | kategorie, name, hersteller, baujahr, standort |

### Bemessungen-Array
```javascript
{
  id: "BBL-001-M1",
  areaType: "Bruttogeschossfläche",  // BGF, NGF, EBF, Volumen, etc.
  value: 15000,
  unit: "m²",                         // oder m³, Stk
  accuracy: "Gemessen",               // Berechnet, Geschätzt, Aggregiert
  standard: "SIA 416",                // SIA 380/1, DIN 277
  source: "CAD/BIM",                  // Vermessung, Schätzmodell, Manuell
  validFrom: "15.03.2019",
  validUntil: null
}
```

## View-System

### Vier Hauptansichten

| View | URL-Parameter | Beschreibung |
|------|---------------|--------------|
| **Map** | `?view=map` | Interaktive Karte mit Gebäude-Markern |
| **List** | `?view=list` | Sortierbare Tabelle mit Export |
| **Gallery** | `?view=gallery` | Karten-Grid mit Vorschaubildern |
| **Detail** | `?view=detail&buildingId=BBL-001` | Detailansicht mit Tabs |

### Detail-View Tabs
1. **Übersicht** - Stammdaten, Bild-Carousel, Mini-Karte
2. **Bemessungen** - Flächentabelle nach SIA-Standards
3. **Dokumente** - Grundrisse, Pläne, GEAK-Zertifikate
4. **Kosten** - Kostentabelle (geplant)
5. **Verträge** - Wartungs-, Reinigungs-, Sicherheitsverträge
6. **Kontakte** - Objektverantwortliche, Hauswart, etc.
7. **Ausstattung** - Gebäudetechnik und Inventar

## Externe APIs

### Swisstopo Location Search
```
GET https://api3.geo.admin.ch/rest/services/ech/SearchServer
?type=locations&limit=5&sr=4326&searchText=<query>&lang=de
```

### Swisstopo Layer Search
```
GET https://api3.geo.admin.ch/rest/services/ech/SearchServer
?type=layers&limit=5&lang=de&searchText=<query>
```

### Mapbox Map Tiles
```
https://api.mapbox.com/mapbox-gl-js/v3.4.0/
Styles: light-v11, standard-v12, satellite-v9
```

## Code-Konventionen

### JavaScript
- **Variablen:** `var` (Legacy-Kompatibilität)
- **Funktionen:** camelCase (`switchView`, `renderListView`)
- **DOM-Zugriff:** `document.getElementById()`
- **Event-Handler:** `addEventListener` Pattern
- **Globale State:** `portfolioData`, `filteredData`, `selectedBuildingId`

### CSS
- **Classes:** kebab-case (`.gallery-card`, `.status-badge`)
- **IDs:** kebab-case (`#map-view`, `#info-panel`)
- **Layout:** Flexbox für 1D, CSS Grid für 2D
- **Variables:** Alle Design-Tokens in `:root`

### HTML
- **Data-Attributes:** `data-view`, `data-id`, `data-sort`, `data-filter`
- **Semantische Tags:** `<header>`, `<main>`, `<nav>`, `<aside>`, `<section>`
- **Accessibility:** `role`, `aria-label`, `tabindex`

## Wichtige Funktionen

### View-Management
```javascript
switchView(view)              // Wechselt zwischen map/list/gallery/detail
showDetailView(id)            // Öffnet Detail-Ansicht für Gebäude
setViewInURL(view)            // Speichert View in URL
getViewFromURL()              // Liest View aus URL
getBuildingIdFromURL()        // Liest Building-ID aus URL
```

### Daten-Rendering
```javascript
renderListView()              // Rendert Tabellen-Ansicht
renderGalleryView()           // Rendert Galerie-Karten
renderMeasurementsTable()     // Rendert Bemessungen-Tab
renderDocumentsTable()        // Rendert Dokumente-Tab
renderContactsTable()         // Rendert Kontakte-Tab
renderContractsTable()        // Rendert Verträge-Tab
renderFacilitiesTable()       // Rendert Ausstattung-Tab
```

### Karte
```javascript
addMapLayers()                // Fügt GeoJSON-Layer zur Karte hinzu
selectBuilding(id)            // Wählt Gebäude auf Karte
flyToBuilding(id)             // Fliegt zu Gebäude
initMiniMap()                 // Initialisiert Mini-Karte in Detail-View
setMapStyle(style)            // Wechselt Kartenstil
```

### Filter & Suche
```javascript
applyFilters()                // Wendet alle aktiven Filter an
initFilterOptions()           // Initialisiert Filter-Dropdowns
performSearch(query)          // Führt lokale + API-Suche durch
renderSearchResults()         // Zeigt Suchergebnisse an
handleSearchClick(type, id)   // Globale Handler für Suchergebnisse
```

### Export
```javascript
exportToCSV()                 // Exportiert gefilterte Daten als CSV
exportToJSON()                // Exportiert gefilterte Daten als JSON
exportToPDF()                 // Exportiert gefilterte Daten als PDF
```

## Accessibility-Features

- **Skip-Links** für Tastaturnavigation
- **Focus-Visible Styles** für Tastaturbenutzer
- **WCAG Farbkontrast** für Lesbarkeit
- **Semantisches HTML** für Screen Reader
- **Touch-Target Minimum** 44px für Mobile
- **ARIA-Labels** für interaktive Elemente

## Hinweise für Entwicklung

1. **Single-File-Architektur:** Alle Änderungen erfolgen in `index.html` (~7000 Zeilen)
2. **Kein Build:** Änderungen sind sofort sichtbar nach Browser-Reload
3. **Mapbox-Abhängigkeit:** WebGL erforderlich, Token muss gültig sein
4. **Daten:** GeoJSON wird bei Seitenladung von `/data/buildings.geojson` geladen
5. **URL-State:** View und Building-ID werden in URL gespeichert
6. **LocalStorage:** Map-Style wird persistent gespeichert

### Code-Struktur in index.html
| Zeilen | Inhalt |
|--------|--------|
| 1-10 | HTML Head, CDN-Links |
| 11-2600 | CSS Styles (Design-System) |
| 2600-4200 | HTML Struktur |
| 4200-7000 | JavaScript (Logik) |

## Schweizer Standards

| Standard | Beschreibung |
|----------|--------------|
| **SIA 416** | Schweizer Norm für Flächen und Volumen im Hochbau |
| **SIA 380/1** | Energiebedarf von Gebäuden |
| **EGID** | Eidgenössischer Gebäudeidentifikator |
| **EGRID** | Eidgenössischer Grundstücksidentifikator |
| **LV95** | Schweizer Koordinatenreferenzsystem |
| **Swisstopo** | Bundesamt für Landestopografie |
| **SN 506 511** | Baukostenplan Schweiz |

## Deployment

**GitHub Pages:** Push zu `main` deployt automatisch nach https://davras5.github.io/gis-immo/

**Alternativ:** Beliebiger Static Host (Netlify, Vercel, Apache, Nginx, CloudFlare).

**Performance:**
- index.html: 268 KB (komprimiert ~60 KB)
- buildings.geojson: 85 KB (komprimiert ~20 KB)
- Schnelle Ladezeiten mit GZIP-Komprimierung
- Client-seitiges Filtern ohne Netzwerk-Calls

## Statistiken

| Metrik | Wert |
|--------|------|
| Dateien | 8 (1 HTML, 1 JSON, 3 Images, 3 Docs) |
| HTML-Grösse | 268 KB (~7000 Zeilen) |
| Daten-Grösse | 85 KB (~2800 Zeilen) |
| Funktionen | 50+ |
| Views | 4 (Map, List, Gallery, Detail) |
| Gebäude | 10+ mit vollständigen Daten |
| Externe APIs | 3 (Mapbox, Swisstopo x2) |
| CSS Variables | 30+ |
| Event Listeners | 54+ |
