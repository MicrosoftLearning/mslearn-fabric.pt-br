---
lab:
  title: Consultar dados no banco de dados KQL
  module: Query data from a Kusto Query database in Microsoft Fabric
---
# Introdução à consulta de um banco de dados Kusto no Microsoft Fabric
Um Conjunto de Consultas KQL é uma ferramenta que permite executar consultas, além de modificar e exibir resultados de consultas de um banco de dados KQL. Você pode vincular cada guia no Conjunto de Consultas KQL a um banco de dados KQL diferente, além de salvar suas consultas para uso futuro ou compartilhá-las com outras pessoas para fins de análise de dados. Você também pode alternar o banco de dados KQL para qualquer guia, de modo a poder comparar os resultados de consultas de diferentes fontes de dados.

Para criar consultas, o Conjunto de Consultas KQL usa a Linguagem de Consulta Kusto, que é compatível com diversas funções SQL. Para saber mais sobre a [Linguagem de Consulta Kusto (KQL)](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/?context=%2Ffabric%2Fcontext%2Fcontext), 

Este laboratório levará aproximadamente **25** minutos para ser concluído.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Entre no [Microsoft Fabric](https://app.fabric.microsoft.com) em `https://app.fabric.microsoft.com` e selecione **Power BI**.
2. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
3. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
4. Quando o novo workspace for aberto, ele deverá estar vazio, conforme mostrado aqui:

    ![Captura de tela de um workspace vazio no Power BI.](./Images/new-workspace.png)

Nesse laboratório, você usará a Análise de Dados em Tempo Real (RTA) do Fabric para criar um banco de dados KQL a partir de uma amostra de fluxo de eventos. A Análise de Dados em Tempo Real convenientemente fornece uma amostra de conjunto de dados que você pode usar para explorar os recursos da RTA. Você usará essa amostra de dados para criar consultas KQL|SQL e conjuntos de consultas que analisem alguns dados em tempo real e permitam seu uso adicional nos processos downstream.

## Criar um banco de dados KQL

1. Na **Análise em Tempo Real**, selecione a caixa **Banco de Dados KQL**.

   ![Imagem de escolher kqldatabase](./Images/select-kqldatabase.png)

2. Você precisará **Nomear** o banco de dados KQL

   ![Imagem do nome kqldatabase](./Images/name-kqldatabase.png)

3. Dê ao banco de dados KQL um nome do qual você se lembrará, como **MyStockData**, e pressione **Criar**.

4. No painel **Detalhes do banco de dados**, selecione o ícone de lápis para ativar a disponibilidade no OneLake.

   ![Imagem de habilitar onelake](./Images/enable-onelake-availability.png)

5. Selecione a caixa de **dados de exemplo** nas opções de ***Iniciar obtendo dados***.
 
   ![Imagem de opções de seleção com dados de exemplo realçados](./Images/load-sample-data.png)

6. escolha a caixa **Análise de métricas automotivas** nas opções de dados de exemplo.

   ![Imagem da escolha de dados de análise para laboratório](./Images/create-sample-data.png)

7. Depois que os dados terminarem de ser carregados, podemos verificar se o banco de dados KQL está preenchido.

   ![Dados sendo carregados no Banco de Dados KQL](./Images/choose-automotive-operations-analytics.png)

7. Depois que os dados são carregados, verifique se eles são carregados no banco de dados KQL. Você pode fazer isso selecionando as reticências à direita da tabela, navegando até a **tabela de Consulta** e selecionando **Mostrar quaisquer 100 registros**.

    ![Imagem da seleção dos 100 principais arquivos da tabela RawServerMetrics](./Images/rawservermetrics-top-100.png)

   > **OBSERVAÇÃO**: na primeira vez que você executar isso, pode levar vários segundos para alocar recursos de computação.

    ![Imagem dos 100 registros dos dados](./Images/explore-with-kql-take-100.png)


## Cenário
Nesse cenário, você é um analista encarregado de consultar um conjunto de dados de exemplo de corridas de táxi de NYC de métricas brutas das quais você obterá estatísticas resumidas (criação de perfil) dos dados do ambiente do Fabric. Você usa a KQL para consultar esses dados e coletar informações para obter informações informativas sobre os dados.

## Introdução à Linguagem de Consulta Kusto (KQL) e sua sintaxe

A Linguagem de Consulta Kusto (KQL) é uma linguagem de consulta usada para analisar dados no Microsoft Azure Data Explorer, que faz parte do Azure Fabric. A KQL foi projetada para ser simples e intuitiva, facilitando o aprendizado e o uso para iniciantes. Ao mesmo tempo, ela também é altamente flexível e personalizável, permitindo que usuários avançados executem consultas e análises complexas.

A KQL é baseada em uma sintaxe semelhante ao SQL, mas com algumas diferenças importantes. Por exemplo, a KQL usa um operador de pipe (|) em vez de um ponto e vírgula (;) para separar comandos e usa um conjunto diferente de funções e operadores para filtrar e manipular dados.

Um dos principais recursos da KQL é sua capacidade de lidar com grandes volumes de dados de forma rápida e eficiente. Isso a torna ideal para analisar logs, dados de telemetria e outros tipos de Big Data. A KQL também dá suporte a uma ampla variedade de fontes de dados, incluindo dados estruturados e não estruturados, tornando-os uma ferramenta versátil para análise de dados.

No contexto do Microsoft Fabric, a KQL pode ser usada para consultar e analisar dados de várias fontes, como logs de aplicativo, métricas de desempenho e eventos do sistema. Isso pode ajudá-lo a obter insights sobre a integridade e o desempenho de seus aplicativos e infraestrutura e identificar problemas e oportunidades de otimização.

De modo geral, a KQL é uma linguagem de consulta poderosa e flexível que pode ajudar você a obter insights sobre seus dados de forma rápida e fácil, esteja você trabalhando com o Microsoft Fabric ou outras fontes de dados. Com sua sintaxe intuitiva e funcionalidades poderosas, a KQL definitivamente vale a pena explorar ainda mais.

Neste módulo, nos concentraremos nos conceitos básicos das consultas no Banco de Dados KQL. Você verá rapidamente que, na KQL, não há um ```SELECT```, é possível simplesmente usar o nome da tabela e pressionar executar. Abordaremos as etapas de uma análise simples usando KQL primeiro e, em seguida, SQL em relação ao mesmo Banco de Dados KQL que se baseia no Azure Data Explorer.

Consultas **SELECT**, que são usadas para recuperar dados de uma ou mais tabelas. Por exemplo, você pode usar uma consulta SELECT para obter os nomes e salários de todos os funcionários em uma empresa.

Consultas **WHERE**, que são usadas para filtrar os dados com base em determinadas condições. Por exemplo, você pode usar uma consulta WHERE para obter os nomes dos funcionários que trabalham em um departamento específico ou que têm um salário acima de um determinado valor.

Consultas **GROUP BY**, que são usadas para agrupar os dados por uma ou mais colunas e executar funções de agregação nelas. Por exemplo, você pode usar uma consulta GROUP BY para obter o salário médio dos funcionários por departamento ou por país.

Consultas **ORDER BY**, que são usadas para classificar os dados por uma ou mais colunas em ordem crescente ou decrescente. Por exemplo, você pode usar uma consulta ORDER BY para obter os nomes dos funcionários classificados por seus salários ou por seus sobrenomes.

   > **AVISO:** não é possível criar relatórios do Power BI a partir de conjuntos de consultas com **T-SQL** porque o Power BI não dá suporte ao T-SQL como uma fonte de dados. O **Power BI só dá suporte a KQL como a linguagem de consulta nativa para conjuntos de consultas**. Se você quiser usar o T-SQL para consultar seus dados no Microsoft Fabric, precisará usar o ponto de extremidade T-SQL que emula o Microsoft SQL Server e permite executar consultas T-SQL em seus dados. No entanto, o ponto de extremidade T-SQL tem algumas limitações e diferenças em relação ao SQL Server nativo e não dá suporte à criação ou publicação de relatórios no Power BI.

## Dados ```SELECT``` de nosso conjunto de dados de exemplo usando KQL

1. Nesta consulta, extrairemos 100 registros da tabela Viagens. Usamos a palavra-chave ```take``` para pedir ao mecanismo que retorne 100 registros.

```kql
Trips
| take 100
```
  > **OBSERVAÇÃO:** o caractere pipe ```|``` é usado para duas finalidades na KQL, incluindo a utilização de operadores de consulta separados em uma instrução de expressão tabular. Ele também é usado como um operador OR lógico dentro de colchetes ou colchetes redondos para indicar que você pode especificar um dos itens separados pelo caractere de pipe. 
    
2. Podemos ser mais precisos simplesmente adicionando atributos específicos que gostaríamos de consultar usando a palavra-chave ```project``` e, em seguida, usando a palavra-chave ```take``` para informar ao mecanismo quantos registros retornar.

> **OBSERVAÇÃO:** o uso de ```//``` denota comentários usados na ferramenta de consulta ***Explorar seus dados*** do Microsoft Fabric.

```
// Use 'project' and 'take' to view a sample number of records in the table and check the data.
Trips 
| project vendor_id, trip_distance
| take 10
```

3. Outra prática comum na análise é renomear colunas em nosso conjunto de consultas para torná-las mais amigáveis. Isso pode ser feito usando o novo nome de coluna seguido pelo sinal de igual e a coluna que desejamos renomear.

```
Trips 
| project vendor_id, ["Trip Distance"] = trip_distance
| take 10
```

4. Talvez também queiramos resumir as viagens para ver quantas milhas foram percorridas:

```
Trips
| summarize ["Total Trip Distance"] = sum(trip_distance)
```
## Dados ```GROUP BY``` de nosso conjunto de dados de exemplo usando KQL

1. Em seguida, talvez queiramos ***agrupar pelo*** local de retirada, o que fazemos com o operador ```summarize```. Também podemos usar o operador ```project```, que nos permite selecionar e renomear as colunas que você deseja incluir na saída. Nesse caso, realizamos um agrupamento por bairro dentro do sistema de táxis de NY para fornecer aos nossos usuários a distância total percorrida a partir de cada bairro.

```
Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = pickup_boroname, ["Total Trip Distance"]
```

2. Você observará que temos um valor em branco, o que nunca é bom para análise, e podemos usar a função ```case``` junto com as funções ```isempty``` e ```isnull``` para categorizá-los em nosso 
```
Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
```

## Dados ```ORDER BY``` de nosso conjunto de dados de exemplo usando KQL

1. Para dar mais sentido aos nossos dados, normalmente os ordenamos por uma coluna, e isso é feito na KQL com um operador ```sort by``` ou ```order by``` e eles agem da mesma maneira.
 
```
// using the sort by operators
Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
| sort by Borough asc 

// order by operator has the same result as sort by
Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
| sort by Borough asc 
```

## Cláusula ```WHERE``` para filtrar dados em nossa consulta KQL de exemplo

1. Ao contrário do SQL, nossa cláusula WHERE é imediatamente chamada em nossa consulta KQL. Ainda podemos usar o ```and```, bem como os operadores lógicos ```or``` dentro de sua cláusula where, e ele será avaliado como verdadeiro ou falso em relação à tabela, e pode ser simples ou uma expressão complexa que pode envolver várias colunas, operadores e funções.

```
// let's filter our dataset immediately from the source by applying a filter directly after the table.
Trips
| where pickup_boroname == "Manhattan"
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
| sort by Borough asc

```

## Usar o T-SQL para consultar informações de resumo

O Banco de Dados KQL não dá suporte ao T-SQL nativamente, mas fornece um ponto de extremidade T-SQL que emula o Microsoft SQL Server e permite que você execute consultas T-SQL em seus dados. No entanto, o ponto de extremidade T-SQL tem algumas limitações e diferenças em relação ao SQL Server nativo. Por exemplo, ele não dá suporte à criação, alteração ou remoção de tabelas ou à inserção, atualização ou exclusão de dados. Ele também não dá suporte a algumas funções T-SQL e sintaxe que não são compatíveis com KQL. Ele foi criado para permitir que sistemas que não dão suporte à KQL usem o T-SQL para consultar os dados em um Banco de Dados KQL. Portanto, é recomendável usar a KQL como a linguagem de consulta primária para o Banco de Dados KQL, pois ela oferece mais recursos e desempenho do que o T-SQL. Você também pode usar algumas funções SQL compatíveis com KQL, como count, sum, avg, min, max, etc. 

## Dados ```SELECT``` de nosso conjunto de dados de exemplo usando T-SQL
1.

```
SELECT * FROM Trips

// We can also use the TOP keyword to limit the number of records returned

SELECT TOP 10 * from Trips
```

2. Se você usar o ```//``` que é um comentário na ferramenta ***Explorar seus dados** dentro do banco de dados KQL, não poderá realçá-lo ao executar consultas T-SQL; em vez disso, você deve usar a notação de comentários SQL padrão ```--```. isso também instruirá o Mecanismo KQL a esperar o T-SQL no Azure Data Explorer.

```
-- instead of using the 'project' and 'take' keywords we simply use a standard SQL Query
SELECT TOP 10 vendor_id, trip_distance
FROM Trips
```

3. Novamente, você pode ver que os recursos padrão do T-SQL funcionam bem com a consulta em que renomeamos trip_distance para um nome mais amigável.

-- Não é necessário usar os operadores "project" ou "take" como T-SQL Works SELECT TOP 10 vendor_id padrão, trip_distance como [Distância da Viagem] de Viagens

## Limpar os recursos

Neste exercício, você criou um banco de dados KQL e configurou um conjunto de dados de exemplo para consulta. Depois disso, você consultou os dados usando o KQL e o SQL. Depois de explorar o banco de dados KQL, exclua o workspace criado para este exercício.
1. Na barra à esquerda, selecione o ícone do workspace.
2. No menu … da barra de ferramentas, selecione Configurações do workspace.
3. Na seção Outros, selecione Remover este workspace.