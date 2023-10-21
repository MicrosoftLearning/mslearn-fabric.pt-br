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

6. escolha a caixa **Análise de métricas** nas opções de dados de exemplo.

   ![Imagem da escolha de dados de análise para laboratório](./Images/create-sample-data.png)

7. Depois que os dados são carregados, verifique se eles são carregados no banco de dados KQL. Você pode fazer isso selecionando as reticências à direita da tabela, navegando até a **tabela de Consulta** e selecionando **Mostrar quaisquer 100 registros**.

    <div><video controls src="./Images/check-kql-sample-dataset.mp4" muted="false" autoplay loop></video></div>

> **OBSERVAÇÃO**: na primeira vez que você executar isso, pode levar vários segundos para alocar recursos de computação.

## Cenário
Nesse cenário, você é um analista encarregado de consultar uma amostra de conjunto de dados do ambiente do Fabric que será implementada.



Uma consulta Kusto é uma maneira de ler dados, processá-los e mostrar os resultados. A consulta é escrita em um texto sem formatação com o qual é fácil trabalhar. Uma consulta Kusto pode ter uma ou mais instruções que mostram dados como uma tabela ou um grafo.

Uma instrução em formato de tabela tem alguns operadores que trabalham os dados de uma tabela. Cada operador usa uma tabela como entrada de dados e fornece uma tabela como resultado. Os operadores são colocados em sequência por meio de uma barra vertical (|). Os dados se movimentam de um operador para outro. Cada operador altera os dados de alguma maneira e os passa para a frente.

Você pode imaginar o processo como um funil, no qual você começa com uma tabela inteira de dados. Cada operador filtra, classifica ou resume os dados. A ordem dos operadores é importante porque eles trabalham em sequência, um após o outro. No final do funil, você obtém um resultado final.

Esses operadores são específicos do KQL, mas podem ser semelhantes ao SQL ou outras linguagens.