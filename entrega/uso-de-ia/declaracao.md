# Declaração de uso de IA

## Ferramenta e modelo

- **Ferramenta:** Claude (Anthropic), via claude.ai (chat com execução de código em sandbox).
- **Modelo:** Claude Sonnet 5.5.

## O que a IA fez

- Extraiu e leu todo o projeto (`src/`, `tests/`, `data/`, configs).
- Verificou se o código cumpre as regras do enunciado (status, noites, câmbio, `Decimal`/half-up, ordenação, contrato do CLI).
- Rodou `pytest -q` (5 passed) e `ruff check`.
- Rodou o CLI na amostra de validação e comparou com a tabela esperada.
- Recalculou o dataset real com um script independente e comparou com o `report.json`.
- Testou códigos de saída (`0` e `2`) e confirmou que os CSVs protegidos não foram alterados.
- Redigiu `entrega/README-ENTREGA.md` e esta declaração.

## O que eu (autor) fiz

- Entreguei o projeto e pedi a verificação e a criação do README com o uso de IA.
- Revisei o resultado e sou responsável pelo que está sendo entregue.
- Exportei/anexei a sessão em `sessao.md`.

## Observação

O código recebido já continha as correções nos módulos de serviço. A IA não alterou os testes fornecidos nem os CSVs de dados.
