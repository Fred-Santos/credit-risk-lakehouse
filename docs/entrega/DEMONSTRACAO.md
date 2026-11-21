# Demonstração curta com dados inventados

## Apresentação da entrega atual

Apresente primeiro os resultados da expansão no [README](../../README.md):
17 fontes, 447.305 casos, duas ABTs retrospectivas e 42 Delta no S3 local.
A [arquitetura](ARQUITETURA.md) e a [conclusão técnica](CONCLUSAO_TECNICA.md)
explicam as garantias verificadas e os limites. Para demonstrar o fluxo atual
sem Kaggle, após os bootstraps, execute:

```bash
bash scripts/project.sh scripts/run_tests.py --suite e2e --integration-required
```

Esse comando executa os e2e do MVP e da expansão. A fixture da expansão percorre
RAW → Bronze → Silver → ABTs → análise → S3 com cinco fontes inventadas e
18 Delta temporárias. Ela demonstra o comportamento; as evidências E00–E08
registram separadamente os volumes reais.

## Demonstração curta histórica do MVP

Pré-requisito: executar os três bootstraps do README, em Linux x86_64. O endpoint
usa somente loopback. O script cria um prefixo próprio, remove seus objetos e
encerra o serviço se tiver sido responsável por iniciá-lo. Não remove dados reais.
Escolha um diretório de saída novo a cada execução.

```bash
bash scripts/objects.sh scripts/demo_portfolio.py --output artifacts/f11/minha-demo
bash scripts/analysis.sh scripts/render_demo.py --output artifacts/f11/minha-demo
```

A demonstração usa três casos e três observações históricas inteiramente inventados.
As entradas já têm schema Silver. Não representa uma nova aquisição ou um novo
replay RAW; esse caminho é exercitado pelos e2e separados do MVP e da expansão.

| Passo para apresentar | Saída observável | Regra conferida pelo script |
|---|---|---|
| Carregar e consultar | `baseline_gold_abt.csv`, `baseline_gold_monthly_metrics.csv` | Três casos, dois meses, soma histórica 5 no caso 1; taxa janeiro 0,5 |
| Corrigir auxiliares | Anuidade 10→11, soma histórica 5→6 | População permanece em três; indicadores mensais permanecem iguais |
| Interromper após MERGE privado | Etapa `failure_after_private_gold_merge` em `report.json` | CURRENT ainda aponta à publicação inicial |
| Retomar | Etapa `resume_and_correct` e CSVs `corrected_*` | Zero linhas inseridas/atualizadas por um novo MERGE no retry |
| Ler publicação inicial | CSVs `historical_*` | Mesmos valores do baseline; CURRENT continua no corrigido |
| Encerrar | `temporary_prefix_removed: true` | Prefixo do ensaio removido; serviço próprio encerrado |

O ponto de falha é `after_commit:gold.abt:merge`. A exceção é injetada após commit
Delta real. Os contratos, escrita, S3A e leitores são reais. Não equivale a cortar
energia do host; indisponibilidade física do serviço foi validada em F09/F10.

O relatório registra IDs reais do ensaio, fingerprints e duração observada. Os
CSVs são resultados consultados das tabelas publicadas. `render_demo.py` verifica
seus hashes e produz `correction.png` e `correction.svg`; não usa mockups ou
métricas inventadas para compor os gráficos.

Ao apresentar, explique primeiro o grão da ABT. Depois mostre o caso 1, a falha
sem nova publicação, a retomada e a leitura histórica. Não apresente esses valores
como clientes, moeda confirmada ou impacto financeiro real.

## Imagem da execução

![Correção observada na fixture inventada](imagens/correcao_sintetica.png)

Imagem gerada pelos CSVs da execução aprovada, sem representar dados reais.

## Gerar o pacote de portfólio

Após a demonstração curta e a renderização, use uma saída nova:

```bash
bash scripts/project.sh scripts/package_portfolio.py \
  --demo artifacts/f11/minha-demo \
  --output artifacts/portfolio/lakehouse.tar.gz \
  --report artifacts/portfolio/package.json
```

O pacote conserva o README atual, inclui sua documentação Markdown referenciada,
o código sanitizado e a demo inventada. O manifesto registra hashes e identifica
a demo como histórica do MVP. Dados reais, credenciais, runtime e relatórios
locais de execução permanecem fora do pacote.
