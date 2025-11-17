# Plano de Ingestão e Normalização dos Dados

Este guia descreve como organizar e preparar os arquivos vindos de **duas fontes principais**:

1. **Celular do Sr. Eli** – contém conversas individuais e em grupo diretamente associadas ao titular.
2. **Celular do Júnior** – contém conversas em múltiplos grupos de WhatsApp e eventuais conversas privadas.

O objetivo é consolidar esses dados (preferencialmente já exportados em CSV) para alimentar os painéis do dashboard forense descrito no plano de UI/UX.

## Estrutura de Pastas Recomendada

```
data/
├── raw/
│   ├── sr_eli/
│   │   ├── whatsapp_export.csv
│   │   └── anexos/...
│   └── junior/
│       ├── whatsapp_groups.csv
│       └── anexos/...
├── interim/
│   ├── sr_eli.parquet
│   └── junior.parquet
└── processed/
    └── conversations.parquet
```

- **raw/** guarda os arquivos originais exportados do WhatsApp (CSV + mídias/texto bruto).
- **interim/** recebe versões limpas e padronizadas, já com tipos tratados.
- **processed/** contém o dataset unificado utilizado pela aplicação.

## Esquema Sugerido para os CSVs

| Coluna               | Tipo       | Descrição                                                                 |
|----------------------|------------|---------------------------------------------------------------------------|
| `source_device`      | string     | `sr_eli` ou `junior`.                                                     |
| `conversation_type`  | string     | `private` ou `group`.                                                     |
| `conversation_name`  | string     | Nome do contato ou grupo.                                                |
| `message_id`         | string     | Identificador único por mensagem (ex.: `<device>-<timestamp>-<row>`).     |
| `sender`             | string     | Nome exibido pelo WhatsApp.                                              |
| `sender_role`        | string     | `owner`, `contact`, `unknown` (útil para destacar falas do titular).      |
| `timestamp_utc`      | datetime   | Timestamp convertido para UTC.                                            |
| `timezone_offset`    | integer    | Offset aplicado durante a conversão.                                     |
| `message_type`       | string     | `text`, `audio`, `image`, `document`, etc.                               |
| `content`            | text       | Corpo da mensagem (texto ou transcrição).                                |
| `media_path`         | string     | Caminho relativo para o arquivo no diretório `raw/<device>/anexos`.      |
| `language`           | string     | Código ISO 639-1 (ex.: `pt`).                                             |
| `tags`               | array/json | Palavras-chave, tópicos ou flags detectados na limpeza inicial.          |

> Caso os CSVs atuais não possuam todas as colunas, inclua pelo menos `source_device`, `conversation_name`, `sender`, `timestamp` e `content`. Os demais campos podem ser enriquecidos durante o ETL.

## Passos de Ingestão

1. **Importar CSV** com `pandas.read_csv`, definindo explicitamente o encoding (UTF-8) e o separador usado no Excel.
2. **Adicionar metadados do dispositivo** (`source_device`, `sender_role`). Para o Sr. Eli, marque as mensagens enviadas pelo titular como `sender_role="owner"`; para o Júnior, utilize `owner` nos grupos relevantes.
3. **Normalizar datas**: converta o timestamp original para timezone conhecido (ex.: America/Sao_Paulo) e depois para UTC, preenchendo `timestamp_utc` e `timezone_offset`.
4. **Classificar tipo de conversa**:
   - `private` quando `conversation_name` corresponder a um contato.
   - `group` quando for um grupo de WhatsApp.
5. **Identificar tipo de mensagem** analisando colunas auxiliares ou padrões de texto (ex.: "<Arquivo de áudio omitido>"). Quando houver mídias, registre o caminho em `media_path`.
6. **Deduplicar**: combine `source_device + conversation_name + timestamp + sender + content` como chave provisória para eliminar duplicidades originadas por exportações diferentes.
7. **Persistir em Parquet** no diretório `interim/`, garantindo schema consistente.
8. **Unificar** os dois datasets via `pandas.concat` e salvar em `processed/conversations.parquet` para servir de base aos módulos analíticos (filtros, contradições, timeline).

## Uso do Texto Bruto

- Utilize o texto bruto apenas quando precisar recuperar contexto não incluído no CSV (ex.: mensagens apagadas, reações ou mensagens do sistema).
- Caso o texto bruto acrescente novas informações, converta-as em colunas adicionais e reexporte o CSV para manter a fonte unificada.

## Próximos Passos

1. Escrever scripts de ETL (ex.: `scripts/ingest.py`) que implementem os passos acima e gerem relatórios de qualidade (linhas processadas, duplicidades, mensagens sem timestamp).
2. Integrar a saída (`processed/conversations.parquet`) com os módulos descritos em `docs/ui_ux_plan.md`, alimentando filtros, timeline e detector de contradições.
3. Automatizar a execução via `make ingest` ou `tox -e ingest` para garantir reprodutibilidade durante o desenvolvimento.
