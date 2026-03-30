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

### Cadeia de artigos matriciais

Existem 3 gerações de artigos matriciais:

1. **Lapa dos Dinheiros (extinta)** — artigos originais (ex: 3446)
2. **União das Freguesias de Seia, São Romão e Lapa dos Dinheiros** — artigos intermédios (ex: 4856)
3. **Lapa dos Dinheiros (restaurada)** — artigos actuais (ex: 1575)

**Exemplo concreto:** artigo 3446 (extinta) → 4856 (união) → 1575 (restaurada)

**Mapeamentos disponíveis:**
- Extinta → União: `data/artigos_mapping.json` (2717 correspondências, extraído de `input/IMI NOVOS ARTIGOS conversão.xlsx`)
- União → Restaurada: **AINDA NÃO DISPONÍVEL** — quando obtido, completar a cadeia

**Estado actual das fichas:**
- Cada parcela é identificada pela **descrição predial** (ex: 32/19970521) — este número nunca muda
- Os artigos matriciais nas fichas YAML (`data/parcelas/`) são da **extinta**, extraídos dos registos prediais
- Através da descrição predial podemos associar os diferentes artigos matriciais (extinta, união, restaurada) ao mesmo terreno
- Os artigos actuais (restaurada) devem ser obtidos junto das Finanças e registados quando conhecidos
- Para declarações de venda, usar sempre o artigo actual (restaurada), mencionando a proveniência do artigo da união quando relevante

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

## Regras de trabalho

- Fazer commit e push a cada bloco de informação adicionada
- Declarações de venda: template em `docs/templates/declaracao-venda.tex`, variáveis em `dados.tex` (não versionado)
- Dados sensíveis (dados.tex com informação pessoal) não vão para o git
