# Bosque Knowledge — Instruções do Projeto

## Contexto administrativo — Lapa dos Dinheiros

A Lapa dos Dinheiros passou por várias reorganizações administrativas:

1. Agregada a São Romão
2. Freguesia autónoma (Lapa dos Dinheiros)
3. União das Freguesias de Seia, São Romão e Lapa dos Dinheiros
4. Novamente freguesia autónoma

**Impacto nos registos:**
- O **artigo matricial** (Finanças) muda com cada reorganização administrativa — um terreno pode ter vários artigos ao longo do tempo
- A **descrição predial** (Conservatória do Registo Predial) mantém sempre o mesmo número, independentemente da organização administrativa

**Regra:** para cada terreno, registar sempre:
- A descrição predial (número permanente)
- O(s) artigo(s) matricial(is) conhecidos (podem ser vários, de épocas diferentes)

## Foco do projeto

Apenas parcelas rústicas e mistas — soutos (castanheiros) próximos da coordenada 40.3829, -7.7029. Não incluir prédios urbanos.

## SNIC API — Cadastro Público

Base URL: `https://snic.dgterritorio.gov.pt/geoportal/dgt_snic2/api/app/search`

### Endpoints disponíveis

#### 1. Dados de uma parcela por NIC

```
GET /predio/nic?filter={NIC}
```

Resposta (JSON):
- `id`, `nic`, `nip`, `fonte_id`, `id_cadastro`
- `dico`, `dicofre`, `des_simpli` (freguesia)
- `concelho`
- `area_m2` — área em metros quadrados
- `emq` — erro médio quadrático (metros)
- `wkt_3857` — polígono WKT em EPSG:3857
- `ndt_list` — códigos NDT

#### 2. Confrontações (vizinhos) de uma parcela

```
GET /predio/confronts?filter={NIC}
```

Resposta (JSON array): lista de parcelas vizinhas, cada uma com:
- `pid`, `p_nic` — referência à parcela consultada
- `id`, `nic`, `label` — identificação do vizinho
- `area_m2`, `wkt_3857` — área e polígono do vizinho
- `get_compass_position` — posição cardeal (Norte, Sul, Este, Oeste)
- Restantes campos iguais ao endpoint `/predio/nic`

### Headers obrigatórios

```
accept: application/json
referer: https://snic.dgterritorio.gov.pt/visualizadorCadastro
cookie: scalargis_rgpd=2
user-agent: Mozilla/5.0 (...)
```

### Notas

- Os polígonos vêm em EPSG:3857 (Web Mercator) — converter para EPSG:4326 (WGS84) para GeoJSON
- O NIC tem o formato `AAA000XXXXXX` (ex: `AAA000842173`)
- É possível fazer crawl recursivo: buscar uma parcela, obter confrontantes, buscar os confrontantes desses, etc.
- Formato NIC legível com espaços: `AAA 000 842 173` (campo `label`)
