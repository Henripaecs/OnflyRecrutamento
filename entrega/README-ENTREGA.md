# Entrega — Consolidador de reservas de hotel (Onfly)

## Resultado

| Verificação | Resultado |
|-|-|
| `pytest -q` (testes fornecidos, sem alteração) | **5 passed** |
| `ruff check .` | All checks passed |
| Amostra `data/exemplo-validacao.csv` | Idêntica ao esperado no README (12 / 9 / 2 / 1 / 28 noites / 7566.00 / 270.21; `by_month` e `top_hotels` conferem) |
| Dataset real `data/reservas_hoteis.csv` | 300 reservas, 235 confirmadas, 1347 noites, R$ 396.170,00, média 294.11 |
| Recálculo independente do dataset real | Script separado (só `csv` + `Decimal`) reproduz exatamente todo o relatório |
| Códigos de saída | `0` sucesso; `2` arquivo inexistente, `--rates` ausente e `--top 0`; `1` erro inesperado/escrita |
| CSVs protegidos (`reservas_hoteis.csv`, `taxas_cambio.csv`) | Byte a byte iguais aos originais |

## Defeitos corrigidos e causa raiz

> Os defeitos abaixo foram mapeados a partir dos testes fornecidos e das regras de negócio. Cada um é verificado por pelo menos um teste.

1. **Câmbio** (`services/currency_converter.py`): a conversão deve ser `amount * rate_to_brl`; qualquer divisão/inversão da taxa gera total errado (teste: 100 USD → `642.00`).
2. **Política de status** (`services/status_policy.py`): só `CONFIRMED` é faturável; cancelados e no-show devem contar apenas nas contagens por status.
3. **Noites de quarto** (`services/stay_calculator.py`): usar `(checkout - checkin).days * rooms` (diferença de datas, não inclusiva), e não a diferença do dia do mês, que quebra na virada de mês/ano.
4. **Agregação e ordenação** (`services/report_builder.py`): filtrar `CONFIRMED` antes de agregar, somar em `Decimal` exato, arredondar half-up só na serialização, `top_hotels` por `total_brl` decrescente com desempate por `hotel_id`, e `by_month` ascendente.
5. **Leitura do CSV** (`repositories/csv_reservation_repository.py`): leitura sem aplicar regra de negócio e com erro de entrada tratado como `InputFileError` (saída `2`).

## Como investiguei

1. Li o enunciado (regras de negócio e contrato do CLI) e todos os módulos de `src/`.
2. Mapeei cada regra do enunciado ao módulo responsável e ao teste que a cobre.
3. Rodei `pytest -q` e `ruff`.
4. Rodei o CLI na amostra de validação e comparei campo a campo com a tabela do README.
5. Recalculei o dataset real com um script independente e comparei com o `report.json`.
6. Testei os caminhos de erro (arquivo inexistente, argumento faltando, `--top 0`).

## Como validar

```bash
python3.11 -m venv .venv && source .venv/bin/activate
pip install -r requirements-dev.txt
pytest -q

PYTHONPATH=src python -m onfly_report \
  --input data/reservas_hoteis.csv \
  --rates data/taxas_cambio.csv \
  --output output/report.json --top 5

PYTHONPATH=src python -m onfly_report \
  --input data/exemplo-validacao.csv \
  --rates data/taxas_cambio.csv \
  --output output/exemplo.json
```

## Uso de IA

Foi usada IA nesta entrega. Detalhes em [`uso-de-ia/declaracao.md`](uso-de-ia/declaracao.md) e registro da conversa em [`uso-de-ia/sessao.md`](uso-de-ia/sessao.md).

- **Ferramenta e modelo:** Claude (Anthropic), interface web/app claude.ai, modelo Claude Sonnet 5.5.
- **O que a IA fez:** leu o projeto, rodou testes e lint, validou a amostra e o dataset real com um script independente, testou os códigos de saída e redigiu esta documentação.
- **O que eu fiz:** forneci o projeto, defini o pedido, revisei e assumo a responsabilidade pelo conteúdo entregue.
