---
name: cadastro
description: Download cadastro predial data from SNIC API for a given NIC code. Fetches parcel info and confrontations, converts geometry to GeoJSON, and saves to data/cadastro/.
allowed-tools: Bash, Read, Write, Glob, Grep
---

# Download Cadastro Predial

Download parcel data from the SNIC API for NIC code `$ARGUMENTS`.

## Steps

1. **Normalize NIC**: ensure format `AAA000XXXXXX` (strip spaces if provided as `AAA 000 842 173`)

2. **Fetch parcel data**:
```bash
curl -s "https://snic.dgterritorio.gov.pt/geoportal/dgt_snic2/api/app/search/predio/nic?filter=${NIC}" \
  -H "accept: application/json" \
  -H "referer: https://snic.dgterritorio.gov.pt/visualizadorCadastro" \
  -H "cookie: scalargis_rgpd=2" \
  -H "user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)"
```

3. **Fetch confrontations** (neighbours):
```bash
curl -s "https://snic.dgterritorio.gov.pt/geoportal/dgt_snic2/api/app/search/predio/confronts?filter=${NIC}" \
  -H "accept: application/json" \
  -H "referer: https://snic.dgterritorio.gov.pt/visualizadorCadastro" \
  -H "cookie: scalargis_rgpd=2" \
  -H "user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)"
```

4. **Convert geometry**: The `wkt_3857` field is in EPSG:3857 (Web Mercator). Convert to EPSG:4326 (WGS84) for GeoJSON using a Python script with pyproj:

```python
from pyproj import Transformer
import re, json

transformer = Transformer.from_crs("EPSG:3857", "EPSG:4326", always_xy=True)

def wkt_to_geojson(wkt):
    coords_str = re.search(r'\(\((.+?)\)\)', wkt).group(1)
    coords_3857 = [
        [float(x) for x in pair.strip().split()]
        for pair in coords_str.split(',')
    ]
    coords_4326 = [
        list(transformer.transform(x, y))
        for x, y in coords_3857
    ]
    return {
        "type": "Feature",
        "geometry": {"type": "Polygon", "coordinates": [coords_4326]}
    }
```

5. **Save results** to `data/cadastro/{NIC}.json` with structure:
```json
{
  "nic": "AAA000XXXXXX",
  "label": "AAA 000 XXX XXX",
  "concelho": "...",
  "freguesia": "...",
  "area_m2": 1234,
  "geojson": { "type": "Feature", "geometry": { "type": "Polygon", "coordinates": [...] } },
  "confrontacoes": [
    {
      "nic": "...",
      "label": "...",
      "area_m2": 1234,
      "posicao": "Norte",
      "geojson": { "type": "Feature", "geometry": { "type": "Polygon", "coordinates": [...] } }
    }
  ]
}
```

6. **Report** a summary: NIC, freguesia, área, número de confrontantes, and confirm the file was saved.

## Notes
- Use the project venv for Python (`source .venv/bin/activate` or create one if needed)
- If pyproj is not installed, install it: `pip install pyproj`
- Create `data/cadastro/` directory if it doesn't exist
- If no arguments provided, ask the user for the NIC code
