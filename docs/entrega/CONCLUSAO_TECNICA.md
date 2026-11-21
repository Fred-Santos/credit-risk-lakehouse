# Nota de entrega técnica

A entrega atual é um lakehouse local de dados de crédito com Python, PySpark,
Delta Lake e S3 compatível via SeaweedFS. Integra as 17 tabelas lógicas de treino
Home Credit, seleciona 447.305 casos de janeiro–setembro/2020 e entrega ABTs,
indicadores e análise retrospectiva. A expansão E00–E08 está concluída no escopo
registrado; o MVP de três fontes e 50 mil casos permanece preservado.

## Resultados e evidências

| Componente | Resultado e evidência |
|---|---|
| Aquisição e Bronze | 32 Parquets e dicionário; 447.305 casos e 92.174.107 linhas relacionadas, reconciliadas com a RAW selecionada — [E02](../evidencias/E02_BRONZE_TEMPORAL.md) |
| Silver | 17 tabelas Delta, contratos por grão, 58 conversões sem novos nulos e reconciliação de valores — [E03](../evidencias/E03_SILVER_EXPANSAO.md) |
| Atributos e ABTs | 166 atributos em 16 blocos; 259.695 casos de desenvolvimento e 187.610 de avaliação, uma linha por caso, features e labels separadas — [E05](../evidencias/E05_ABTS_TEMPORAIS.md) |
| Análise de público | Nove meses, cinco tabelas analíticas e gráficos, com denominadores e reconciliação independente — [E06](../evidencias/E06_PUBLICO_EXPANSAO.md) |
| Publicação e recuperação | 42 Delta no S3 local, manifestos, CURRENT, falhas, retry, rollback, reinício e consumo independente — [E07](../evidencias/E07_PUBLICACAO_EXPANSAO.md) |
| Aceitação | 174 testes aprovados, zero ignorados, matrizes T01–T24 e X01–X17 — [E08](../evidencias/E08_ACEITACAO_EXPANSAO.md) |

As ABTs têm o mesmo contrato, interseção vazia de casos e agregações conferidas
por SQL independente. Cada conjunto de features tem 170 colunas, incluindo
quatro metadados; a ABT lógica com rótulo tem 171 colunas.

A publicação atual aponta para os dados originais. Uma correção auxiliar
simulada foi rastreada até os produtos derivados e permanece no histórico.
As chegadas/revisões demonstradas são simulações declaradas; a competição não
fornece CDC. A RAW e os snapshots anteriores foram preservados.

## Validação e reprodução

A sequência atual de aquisição, processamento, análise e publicação está no
[README](../../README.md#reproduzir-a-entrega-atual). A
[reprodução](REPRODUCAO.md) distingue a trilha real, os e2e sintéticos e a
revisão histórica F11 em cópia isolada.

A E08 registra a regressão com Spark, Delta, S3 e os dois e2e no ambiente local
existente. A F10 mantém sua prova histórica de instalação nova com 153 testes;
a F11 registra demonstração sintética e autorrevisão em outra cópia. Esses
resultados têm escopos próprios. Não houve avaliador externo ou execução remota
comprovada da CI. Uma mudança documental não representa nova execução dos testes.

Python 3.11.14, Java 17.0.21+9, Spark 3.5.7, Delta 3.3.2, Hadoop S3A 3.3.4,
SDK Java 1.12.262 e SeaweedFS 4.53 compõem o ambiente documentado. Os bootstraps
verificam hashes; dependências Python seguem lockfiles.

O perfil da expansão usa heap de 8 GiB e `local[2]`. A Silver registrou pico RSS
amostrado da árvore de 9,59 GiB. A E07 reutilizou snapshots locais aprovados
para publicação e recuperação; não foi uma recomputação RAW→Gold do zero.
As medições são específicas do host e não representam capacidade de cluster.

## Finalidade e limites

A construção, integridade e publicação das ABTs foram verificadas para análise
retrospectiva. O uso preditivo depende de comprovar disponibilidade dos atributos
na data da decisão e maturação do target. A identidade global de clientes entre
períodos também não foi demonstrada. O leitor mantém `ml_ready=false`;
nenhum modelo foi treinado. [Auditoria temporal](../evidencias/E04_ATRIBUTOS_E_TEMPO.md).

A solução opera em um host/driver com escritores cooperantes. Delta garante
transações por tabela; o protocolo de manifesto/CURRENT coordena a publicação
do conjunto. Cluster, concorrência distribuída, streaming, alta disponibilidade,
SLA, infraestrutura AWS e impacto financeiro medido permanecem fora da entrega.
Os indicadores descrevem a população histórica, sem representar taxa operacional
atual de uma carteira real.

O repositório contém código, contratos, guia em 13 capítulos, procedimentos e
evidências. Dados reais, derivados e credenciais ficam locais, fora do Git.
O pacote sintético F11 tem finalidade própria e não redistribui dados Home Credit.
As condições da fonte continuam aplicáveis à divulgação de dados e resultados.

O [backlog](EVOLUCAO_E_AWS.md) parte da expansão já concluída. Revisão externa,
execução remota da CI, modelagem e eventual migração têm critérios próprios;
nenhum recurso AWS foi provisionado.
