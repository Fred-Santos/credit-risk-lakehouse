# Credit Risk Lakehouse — demonstração sintética

Este pacote contém código, notebook sem outputs e uma demonstração executada
com dados inteiramente inventados. Não inclui Home Credit, resultados individuais
reais, credenciais, logs locais, ambientes instalados ou histórico Git.
A licença do código está em LICENSE; ela não concede direitos sobre o dataset.

A entrega atual integra 17 fontes e 447.305 casos, com duas ABTs, 166 atributos
e 42 tabelas Delta publicadas no S3 local. O README principal do pacote preserva
a apresentação atual e a documentação referenciada acompanha a distribuição.
Os números reais são resultados documentados; os dados reais não são incluídos.

A demonstração curta preserva o MVP: três Silver e duas Gold, com três casos
e dois meses inventados, começando em Silver tipada. Para exercitar a expansão
desde RAW, execute a suíte e2e abaixo: ela cobre também cinco fontes inventadas
de níveis 0/1/2 e 18 Delta temporárias. A diferença de volume entre fixture e
entrega real está explicitada na documentação.

## Preparar e executar

Em Linux x86_64 com Python 3 e acesso à rede:

```bash
bash scripts/bootstrap_environment.sh
bash scripts/bootstrap_analysis.sh
bash scripts/bootstrap_objects.sh
bash scripts/objects.sh scripts/demo_portfolio.py --output artifacts/nova-demo
bash scripts/analysis.sh scripts/render_demo.py --output artifacts/nova-demo
```

Use uma pasta de saída nova. Java fica em `.runtime/java/jdk-17.0.21+9-jre`;
remova JAVA_HOME herdado ou aponte-o para essa instalação. Não é necessário Kaggle.
O serviço S3 usa loopback e credenciais locais geradas. Objetos temporários da
demo são removidos ao final. Para o teste completo desde RAW:

```bash
bash scripts/project.sh scripts/run_tests.py --suite e2e --integration-required
```

## Conferir as saídas incluídas

`demo/report.json` registra a execução aprovada. Os seis CSVs mostram baseline,
correção e leitura histórica. A anuidade do caso inventado 1 muda de 10 para 11
e a soma histórica de 5 para 6; a população permanece em três casos. A falha
após commit privado preserva CURRENT; o retry não repete o MERGE e a leitura
histórica recupera os valores iniciais. `demo/correction.png` é derivado dos CSVs.

[Sequência de apresentação](DEMONSTRACAO.md),
[arquitetura e limites](ARQUITETURA.md),
[reprodução por instruções](REPRODUCAO.md).
`portfolio_manifest.json` permite conferir o inventário e hashes. O manifesto
atesta integridade dos arquivos do pacote, não é assinatura de autoria externa.

A ABT é retrospectiva, sem elegibilidade temporal comprovada para treino. Não há
ML, impacto financeiro medido, alta disponibilidade, multi-host ou AWS provisionada.
A preparação deste arquivo não é uma publicação remota; a disponibilização externa
é uma ação separada. O pacote inclui a documentação Markdown referenciada pelo README, com os
registros de evidências agregadas. Dados, relatórios locais em `artifacts/` e
snapshots referenciados nessas evidências ficam fora da distribuição.
