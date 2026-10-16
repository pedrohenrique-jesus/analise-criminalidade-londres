# London Crime Analytics
### Análise exploratória da criminalidade em Londres (2008–2016)

**Autor:** Pedro Henrique Correia de Jesus  
**Projeto Google Cloud:** [london-crime-analytics-497414](https://console.cloud.google.com/welcome?project=london-crime-analytics-497414)

---

## 1. Visão geral

Projeto de análise exploratória de dados sobre registros de criminalidade em Londres, utilizando SQL no BigQuery, Python no Google Colab e Power BI para preparar os dados, investigar padrões e construir um dashboard interativo.

O estudo explora a distribuição das ocorrências por macrocategoria e subcategoria criminal, borough (distrito), ano e mês. O objetivo é transformar registros públicos em indicadores que ajudem a compreender diferenças territoriais, composição dos crimes e evolução temporal.

## 2. Pergunta de negócio

Como os registros de criminalidade em Londres se distribuem entre 2008 e 2016, considerando a evolução temporal, a distribuição territorial e as categorias de crime mais frequentes?

Perguntas complementares:

- Quais macrocategorias concentram a maior parcela das ocorrências registradas?
- Quais boroughs acumulam os maiores volumes?
- Como os registros variam ao longo dos anos e dos meses?
- Como comparar as categorias principais e suas subcategorias?
- Como a distribuição territorial pode apoiar a identificação de áreas para investigação adicional?

## 3. Tecnologias utilizadas

- **Google BigQuery / SQL:** consulta à base pública, agregação e análise exploratória.
- **Python:** extração, validação, limpeza e exportação dos dados.
- **Pandas:** manipulação de dados tabulares.
- **Google Colab:** ambiente de execução do notebook.
- **Power BI:** modelagem visual e dashboard interativo.

## 4. Fonte de dados

- **Tabela pública:** `bigquery-public-data.london_crime.crime_by_lsoa`
- **Período analisado:** 2008–2016 (9 anos).
- **Campos utilizados:** `borough`, `major_category`, `minor_category`, `year`, `month` e `value`.
- **Campo geográfico adicional na tabela original:** `lsoa_code`, presente na tabela de origem, mas não incluído no dataset agregado exportado.

A tabela original retornou **13.490.604 registros** na consulta de validação. A extração do projeto agrega esses registros por borough, macrocategoria, subcategoria, ano e mês, produzindo **112.968 linhas** no arquivo analítico.

**Acesse o projeto:** [Google Cloud Console — London Crime Analytics](https://console.cloud.google.com/welcome?project=london-crime-analytics-497414)

## 5. Pipeline de dados

1. Autenticação no Google Colab e conexão ao BigQuery.
2. Consulta à tabela pública de criminalidade.
3. Agregação de `value` como `total_crimes`, agrupando por borough, major category, minor category, year e month.
4. Salvamento de uma cópia RAW em `/content/raw/london_crime_raw.csv`.
5. Padronização dos nomes das colunas para `snake_case`.
6. Remoção de linhas e colunas totalmente vazias.
7. Remoção de duplicatas explícitas.
8. Conversão de `year`, `month` e `total_crimes` para tipo inteiro.
9. Exportação do dataset limpo para `/content/london_crime_clean.csv`.
10. Importação do dataset no Power BI para construção do dashboard.

### Estrutura do dataset exportado

| Coluna | Descrição |
|---|---|
| `borough` | Borough/distrito associado ao registro |
| `major_category` | Macrocategoria criminal |
| `minor_category` | Subcategoria criminal |
| `year` | Ano do registro |
| `month` | Mês do registro |
| `total_crimes` | Soma de `value` para a combinação dos campos de agrupamento |

**Granularidade:** uma linha por combinação de borough × macrocategoria × subcategoria × ano × mês. O arquivo não preserva a granularidade original por `lsoa_code`.

## 6. Validação e tratamento dos dados

A análise exploratória consultou a tabela de origem para verificar:

- Volume de registros.
- Valores nulos em borough, major category, minor category, year e month.
- Período temporal coberto.
- Distribuição de `value` por macrocategoria.
- Boroughs com maior volume acumulado.

Na consulta registrada no notebook, os cinco campos verificados não apresentaram valores nulos na tabela de origem.

No pipeline aplicado ao dataset agregado:

- Não foram removidas colunas totalmente vazias.
- Não foram removidas linhas totalmente vazias.
- Não foram identificadas duplicatas explícitas.
- `year`, `month` e `total_crimes` foram convertidos para inteiros.

Esses resultados refletem a execução documentada no notebook. Recomenda-se executar novamente o pipeline antes de publicar, caso a fonte ou o código tenham sido alterados.

## 7. Resultados exploratórios

### Distribuição por macrocategoria

| Macrocategoria | Total acumulado de `value` | Participação |
|---|---:|---:|
| Theft and Handling | 2.661.861 | 41,28% |
| Violence Against the Person | 1.558.081 | 24,16% |
| Burglary | 754.293 | 11,70% |
| Criminal Damage | 630.938 | 9,79% |
| Drugs | 470.765 | 7,30% |
| Robbery | 258.873 | 4,01% |
| Other Notifiable Offences | 106.349 | 1,65% |
| Fraud or Forgery | 5.325 | 0,08% |
| Sexual Offences | 1.273 | 0,02% |

Os valores acima reproduzem os resultados apresentados no notebook. A participação foi calculada sobre a soma de `value` entre as macrocategorias consultadas.

### Cinco boroughs com maior volume acumulado

| Borough | Volume registrado |
|---|---:|
| Westminster | 455.028 |
| Lambeth | 292.178 |
| Southwark | 278.809 |
| Camden | 275.147 |
| Newham | 262.024 |

### Período e volume

- **Período identificado:** 2008–2016.
- **Registros na tabela original:** 13.490.604, segundo a consulta de validação.
- **Linhas após a agregação:** 112.968.
- **Soma dos valores apresentados na distribuição por macrocategoria:** 6.447.758.

**Importante:** os totais representam volumes agregados conforme a consulta, e não taxas de criminalidade por habitante. Não há denominadores populacionais no dataset exportado, portanto os volumes não devem ser interpretados como risco individual ou como comparação ajustada pelo tamanho da população.

## 8. Dashboard Power BI

O arquivo `London_BI.pbix` contém quatro páginas:

1. **Executive Overview** — cartões de indicadores, evolução anual do volume de crimes, volume por borough e destaques como principal borough e categoria.
2. **Crime Trends** — análise sazonal mensal, evolução por categoria e comparação entre crimes violentos e não violentos.
3. **Regional Analysis** — visualização territorial em mapa e evolução por região/borough, conforme os campos disponíveis no modelo.
4. **Major & Minor Category** — comparação entre categorias principais e subcategorias criminais.

Os títulos e tipos de visualização foram identificados no arquivo Power BI fornecido. Os valores exatos dos cartões e os detalhes de interação devem ser conferidos no Power BI Desktop antes da publicação final.

## 9. Como reproduzir o projeto

1. Abra o [projeto no Google Cloud](https://console.cloud.google.com/welcome?project=london-crime-analytics-497414).
2. Confirme que a conta possui permissões para executar consultas no BigQuery.
3. Abra o notebook `conexao_bigquery_london_crime.ipynb` no Google Colab.
4. Execute as células em ordem e autorize a autenticação quando solicitado.
5. Verifique os resultados das consultas exploratórias e a contagem de linhas exportadas.
6. Baixe `london_crime_clean.csv` do ambiente Colab.
7. Abra `London_BI.pbix` no Power BI Desktop e atualize ou substitua a fonte de dados pelo CSV exportado, se necessário.
8. Teste filtros, cartões, gráficos e páginas antes de compartilhar o relatório.

Os caminhos `/content/...` são temporários no Google Colab e não representam arquivos automaticamente salvos no GitHub.

## 10. Limitações e cuidados de interpretação

- A análise é descritiva: identifica padrões e diferenças, mas não demonstra causas.
- Os valores são volumes absolutos; sem dados populacionais, não medem taxas de criminalidade ou risco individual.
- A extração agrupa os dados por borough e categoria, removendo a dimensão `lsoa_code` do CSV final. Isso limita análises geográficas em granularidade menor que borough.
- Os resultados registrados no notebook cobrem 2008–2016; não devem ser descritos como retrato atual da criminalidade em Londres.
- As categorias apresentam volumes muito diferentes. Categorias de baixa frequência devem ser interpretadas considerando a definição e a cobertura dos registros.
- O notebook verifica nulos em campos específicos, mas não documenta validações completas de categorias inesperadas, valores negativos, cobertura de todos os boroughs ou regras de negócio de cada categoria.
- Antes de publicar, confirme se o total apresentado no Power BI corresponde à soma de `value` da consulta e se os filtros alteram todos os visuais conforme esperado.

Por tamanho, privacidade e reprodutibilidade, avalie se o CSV deve ser incluído no repositório. Se não for incluído, documente como gerá-lo pelo notebook e evite publicar credenciais ou arquivos de autenticação.

## 11. Conclusão

O projeto demonstra um fluxo de análise de dados que conecta consulta SQL em BigQuery, preparação com Python e comunicação de resultados por meio do Power BI.

Os resultados exploratórios registrados apontam concentração dos volumes em Theft and Handling e Violence Against the Person, além de diferenças entre boroughs. A principal contribuição é organizar os registros em uma estrutura analítica e visual, mantendo explícitas as limitações de período, granularidade e ausência de denominadores populacionais.
