# Bosque da Lapa dos Dinheiros — Base de Conhecimento

Base de conhecimento para suportar a operação de emparcelamento da propriedade
privada proprietária do Bosque da Lapa dos Dinheiros.

## Estrutura

```
data/
  parcelas/          # Fichas individuais de cada parcela (YAML)
  proprietarios/     # Fichas de proprietários (YAML)
  confrontacoes/     # Relações de vizinhança entre parcelas
  cadastro/          # Polígonos e dados do cadastro público (GeoJSON)
  documentos/        # Referências a cadernetas, registos, escrituras
docs/
  contexto.md        # Contexto geral da propriedade e do emparcelamento
  glossario.md       # Termos e conceitos relevantes
```

## Convenções

- Cada parcela tem um identificador único: `P-XXXX` (ex: `P-0001`)
- Cada proprietário tem um identificador único: `PROP-XXXX`
- Dados geográficos em GeoJSON (EPSG:4326 / WGS84)
- Áreas em hectares (ha)
- Ficheiros de dados em YAML para leitura fácil
