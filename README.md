# JSON Archiv

Sammlung von Overpass Abfragen und deren JSON Files.
Abgelegt in der Ordnerstruktur und nach Datum der Abfrage sortiert.

**Wichtig**
Die Daten sind direkt aus OSM exportiert.

## Sinn und Zweck

Sinn dieses Archivs ist es, Datenveränderungen später nachzuvollziehen. Grundsätzlich werden nicht jeden Monat immer alle GeoJSON gezogen, es besteht noch kein Automatismus dazu. Issue dazu wird noch erstellt.
Die JSON Datensammlung soll stetig wachsen und und so ein sauberes Archiv generieren.

## Overpass Abfragen

Nicht abschliessend und laufend erweitert.

<details><summary>Abfragen ausklappen</summary>
<p>

## Spitäler und Altersheime

### Dispogebiet SRZ

```
[out:json];
// [out:csv( ::type, ::id, ::lat, ::lon, name)];
// fetch area “Dispogebiet SRZ” to search in
(
{{geocodeArea:CH-ZH}};
{{geocodeArea:CH-SZ}};
{{geocodeArea:CH-SH}};
{{geocodeArea:CH-ZG}};
)->.searchArea;
// gather results
(
nwr["social_facility"="nursing_home"]["name"!~"^$"](area.searchArea);
nwr["social_facility"="group_home"]["name"!~"^$"](area.searchArea);
nwr["amenity"="hospital"]["name"!~"^$"](area.searchArea);
nwr["amenity"="nursing_home"]["name"!~"^$"](area.searchArea);
nwr["healthcare"="hospital"]["name"!~"^$"](area.searchArea);
);
// print results
out body;
>;
out skel qt;
```

### Stadt Zürich

```
[out:json];
// [out:csv( ::type, ::id, ::lat, ::lon, name)];
// fetch area “Dispogebiet SRZ” to search in
(
{{geocodeArea:Zurich}};
)->.searchArea;
// gather results
(
nwr["social_facility"="nursing_home"]["name"!~"^$"](area.searchArea);
nwr["social_facility"="group_home"]["name"!~"^$"](area.searchArea);
nwr["amenity"="hospital"]["name"!~"^$"](area.searchArea);
nwr["amenity"="nursing_home"]["name"!~"^$"](area.searchArea);
nwr["healthcare"="hospital"]["name"!~"^$"](area.searchArea);
);
// print results
out body;
>;
out skel qt;
```

## Defibrillatoren

### Dispogebiet SRZ

```
[out:json];
// [out:csv( ::type, ::id, ::lat, ::lon, name)];
// fetch area “Dispogebiet SRZ” to search in
(
{{geocodeArea:CH-ZH}};
{{geocodeArea:CH-SZ}};
{{geocodeArea:CH-SH}};
{{geocodeArea:CH-ZG}};
)->.searchArea;

// gather results
(
nwr["emergency"="defibrillator"](area.searchArea);
);
// print results
out body;
>;
out skel qt;
```

### Kanton ZH

```
[out:json][timeout:25];
// fetch area “CH-ZH” to search in
{{geocodeArea:CH-ZH}}->.searchArea;
// gather results
(
  // query part for: “emergency=defibrillator”
  node["emergency"="defibrillator"](area.searchArea);
  way["emergency"="defibrillator"](area.searchArea);
  relation["emergency"="defibrillator"](area.searchArea);
);
// print results
out body;
>;
out skel qt;
```

### Stadt ZH

```

[out:json][timeout:25];
// fetch area “Zurich” to search in
{{geocodeArea:Zurich}}->.searchArea;
// gather results
(
  // query part for: “emergency=defibrillator”
  node["emergency"="defibrillator"](area.searchArea);
  way["emergency"="defibrillator"](area.searchArea);
  relation["emergency"="defibrillator"](area.searchArea);
);
// print results
out body;
>;
out skel qt;
```

## Rettungswachen

### Dispogebiet SRZ
```
[out:json];
// [out:csv( ::type, ::id, ::lat, ::lon, name)];
// fetch area “Dispogebiet SRZ” to search in
(
{{geocodeArea:CH-ZH}};
{{geocodeArea:CH-SZ}};
{{geocodeArea:CH-SH}};
{{geocodeArea:CH-ZG}};
)->.searchArea;

// gather results
(
nwr["emergency"="ambulance_station"](area.searchArea);
);
// print results
out body;
>;
out skel qt;
```

## Feuerwehrwachen

### Kanton ZH

```
[out:json][timeout:25];
// fetch area “CH-ZH” to search in
{{geocodeArea:CH-ZH}}->.searchArea;
// gather results
(
  // query part for: “amenity=fire_station”
  node["amenity"="fire_station"](area.searchArea);
  way["amenity"="fire_station"](area.searchArea);
  relation["amenity"="fire_station"](area.searchArea);
);
// print results
out body;
>;
out skel qt;
```

</p>
</details>

## Automation

[![Get data from Overpass](https://github.com/Schutz-Rettung-Zurich/json-archive/workflows/Get%20data%20from%20Overpass/badge.svg)](https://github.com/Schutz-Rettung-Zurich/json-archive/actions?query=workflow%3A%22Get+data+from+Overpass%22)

In diesem Repository sind GitHub Actions eingerichtet, um täglich aktuelle Daten via [Overpass API](https://wiki.openstreetmap.org/wiki/Overpass_API) abzufragen und als GeoJSON abzulegen.

* Die aktuelle GeoJSON-Dateien sind im [`data` Verzeichnis](https://github.com/Schutz-Rettung-Zurich/json-archive/tree/main/data)
* Die GitHub Actions sind im [`overpass.yml`](https://github.com/Schutz-Rettung-Zurich/json-archive/blob/main/.github/workflows/overpass.yml) Workflow beschrieben
* Der Workflow verwendet das Skript [`run_queries.sh`](https://github.com/Schutz-Rettung-Zurich/json-archive/blob/main/run_queries.sh) um alle Queries laufen zu lassen
* Jedes Overpass-Query ist in einer eigenen Datei im [Verzeichnis `queries`](https://github.com/Schutz-Rettung-Zurich/json-archive/tree/main/queries) abgelegt

### Neues Query hinzufügen

Um ein neues Query hinzuzufügen, müssen folgende Schritte befolgt werden:

1. Query schreiben und via http://overpass-turbo.osm.ch/ testen. **ACHTUNG:** es ist nur die Overpass Query Syntax unterstützt, **keine [Overpass Turbo Shortcuts](https://wiki.openstreetmap.org/wiki/Overpass_turbo/Extended_Overpass_Turbo_Queries)** (z.B. ` {{geocodeArea:CH-ZH}}`)
1. Query als neue Datei in [`queries` Verzeichnis](https://github.com/Schutz-Rettung-Zurich/json-archive/tree/main/queries) ablegen
1. Neues Query in [`run_queries.sh`](https://github.com/Schutz-Rettung-Zurich/json-archive/blob/main/run_queries.sh) aufrufen
