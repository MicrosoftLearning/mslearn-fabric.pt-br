---
lab:
  title: Usar o Copilot em data warehouses do Microsoft Fabric
  module: Get started with Copilot in Fabric for Data Warehouse
---

# Usar o Copilot em data warehouses do Microsoft Fabric

No Microsoft Fabric, um data warehouse fornece um banco de dados relacional para análise em larga escala. Ao contrário do ponto de extremidade SQL somente leitura padrão para tabelas definido em um lakehouse, um data warehouse fornece a semântica completa do SQL, incluindo a capacidade de inserir, atualizar e excluir dados nas tabelas. Neste laboratório, exploraremos como podemos aproveitar o Copilot para criar consultas SQL.

Este exercício deve levar aproximadamente **30** minutos para ser concluído.

## O que você aprenderá

Depois de concluir este laboratório, você será capaz de:

- Entender a função dos data warehouses no Microsoft Fabric.
- Criar e configurar um workspace e data warehouse no Fabric.
- Carregar e explorar exemplos de dados usando SQL.
- Usar o Copilot para gerar, refinar e solucionar problemas de consultas SQL de prompts em linguagem natural.
- Criar exibições e executar análise de dados avançada com a geração de SQL assistida por IA.
- Aplicar os recursos do Copilot para acelerar as tarefas de exploração e análise de dados.

## Antes de começar

Você precisa de uma [Capacidade do Microsoft Fabric (F2 ou superior)](https://learn.microsoft.com/fabric/fundamentals/copilot-enable-fabric) com Copilot habilitado para concluir este exercício.

## Cenário do exercício

Neste exercício, você é analista de dados de uma empresa de varejo que quer entender melhor o desempenho de vendas usando o Microsoft Fabric. Sua equipe adotou recentemente as funcionalidades de data warehouse do Fabric e quer aproveitar o Copilot para acelerar a exploração e os relatórios de dados. Você criará um data warehouse, carregará amostras de dados de vendas de varejo e usará o Copilot para gerar e refinar consultas SQL. Ao final do laboratório, você terá experiência prática no uso de IA para analisar tendências de vendas, criar exibições reutilizáveis e executar análises avançadas de dados, tudo no ambiente do Fabric.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com o Fabric habilitado. Um workspace no Microsoft Fabric serve como um ambiente colaborativo em que você pode organizar e gerenciar todos os seus artefatos de engenharia de dados, incluindo lakehouses, notebooks e conjuntos de dados. Pense nele como uma pasta de projeto que contém todos os recursos necessários para sua análise de dados.

1. Navegue até a [home page do Microsoft Fabric](https://app.fabric.microsoft.com/home?experience=fabric) em `https://app.fabric.microsoft.com/home?experience=fabric` em um navegador e entre com suas credenciais do Fabric.

1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).

1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Premium* ou *Fabric*). Observe que não há suporte para *Avaliação*.
   
    > **Por que isso importa**: o Copilot requer uma capacidade paga do Fabric para funcionar. Isso garante que você tenha acesso aos recursos baseados em IA que ajudarão a gerar código em todo o laboratório.

1. Quando o novo workspace for aberto, ele estará vazio.

![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

## Criar um data warehouse

Agora que você tem um espaço de trabalho, é hora de criar um data warehouse. Um data warehouse no Microsoft Fabric é um banco de dados relacional otimizado para cargas de trabalho analíticas. Ao contrário de bancos de dados tradicionais projetados para operações transacionais, os data warehouses são estruturados para lidar com grandes volumes de dados e consultas complexas com eficiência. Localize o atalho para criar um depósito:

1. Na barra de menus à esquerda, selecione **Criar**. Na página *Novo*, na seção *Data Warehouse*, selecione **Warehouse**. Dê um nome exclusivo de sua preferência. Esse nome identificará seu data warehouse dentro do workspace, portanto, escolha algo descritivo que reflita a finalidade dele.

    >**Observação**: se a opção **Criar** não estiver fixada na barra lateral, você precisará selecionar a opção de reticências (**...**) primeiro.

    Após alguns minutos, um warehouse será criado. O processo de provisionamento configura a infraestrutura subjacente e cria os componentes necessários para seu banco de dados analítico:

    ![Captura de tela de um novo warehouse.](./Images/new-data-warehouse2.png)

## Criar tabelas e inserir dados

Um warehouse é um banco de dados relacional no qual você pode definir tabelas e outros objetos. Para demonstrar os recursos do Copilot, precisamos de dados de exemplo com os quais trabalhar. Criaremos um esquema de vendas de varejo típico com tabelas de dimensões (cliente, data, produto) e uma tabela de fatos (pedidos de vendas). Esse é um padrão comum em data warehouse chamado esquema em estrela.

1. Na guia do menu **Página Inicial**, use o botão **Nova Consulta SQL** para criar uma consulta. Isso abre um editor SQL no qual você pode escrever e executar comandos Transact-SQL. Em seguida, copie e cole o código Transact-SQL de `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-fabric/refs/heads/main/Allfiles/Labs/22c/create-dw.txt` no novo painel de consulta. Esse script contém todas as instruções CREATE TABLE e INSERT necessárias para criar nosso conjunto de dados de exemplo.

1. Execute a consulta, que criará um esquema de data warehouse simples e carregará alguns dados. O script levará cerca de 30 segundos para ser executado. Durante esse tempo, o mecanismo de banco de dados está criando as estruturas de tabela e preenchendo-as com amostras de dados de vendas de varejo.

1. Use o botão **Atualizar** da barra de ferramentas para atualizar a exibição. Em seguida, no painel do **Explorer**, verifique se o esquema **dbo** no data warehouse já contém as quatro seguintes tabelas:
   
    - **DimCustomer**: contém informações do cliente, incluindo nomes e endereços
    - **DimDate**: uma tabela de dimensões de data com informações de calendário (ano, mês, nomes de dia etc.)
    - **DimProduct**: catálogo de produtos com categorias, nomes e informações sobre preços
    - **FactSalesOrder**: a tabela de fatos central que contém transações de vendas com chaves estrangeiras para as tabelas de dimensões

    > **Dica**: se o esquema demorar um pouco para ser carregado, basta atualizar a página do navegador. O painel Explorer mostra a estrutura do banco de dados e facilita a navegação em suas tabelas e outros objetos de banco de dados.

## Consultar tabelas de data warehouse

Como o data warehouse é um banco de dados relacional, você pode usar o SQL para consultar as tabelas dele. No entanto, escrever consultas SQL complexas do zero pode ser demorado e propenso a erros. Trabalhar com o Copilot torna acelera ainda mais a geração de consultas SQL. O Copilot usa inteligência artificial para entender suas solicitações de linguagem natural e converte-las em sintaxe SQL adequada, tornando a análise de dados mais acessível.

1. Feche a **Consulta SQL 1** atual. Isso limpa o workspace para que possamos nos concentrar no uso do Copilot para gerar consultas.

1. Na faixa de opções da Página Inicial, selecione a opção Copilot. Isso abre o painel do assistente do Copilot, no qual você pode interagir com a IA para gerar consultas.

    ![Captura de tela do painel do Copilot aberto no depósito.](./Images/copilot-fabric-data-warehouse-start.png)

1. Vamos começar explorando o que o Copilot pode fazer. Clique na sugestão rotulada `What can Copilot do?` e envie-a como seu prompt.

    Leia a saída e observe que o Copilot está em versão prévia e pode ajudar com debate, gerar consultas SQL, explicar e corrigir consultas etc.
    
    ![Captura de tela do painel do Copilot com ajuda no warehouse.](./Images/copilot-fabric-data-warehouse-pane.png)
    
1. Estamos buscando analisar a receita de vendas por mês. Este é um requisito de negócios comum: entender as tendências de receita ao longo do tempo ajuda a identificar padrões sazonais, tendências de crescimento e métricas de desempenho. Insira o prompt a seguir e envie-o.

    ```copilot-prompt
    /generate-sql Calculate monthly sales revenue
    ```

1. Examine a saída gerada, que pode ser um pouco diferente dependendo do seu ambiente e das atualizações mais recentes do Copilot. Observe como o Copilot interpreta sua solicitação e cria instruções JOIN apropriadas entre as tabelas de fatos e dimensões para agregar dados de vendas por mês.

1. Selecione o ícone **Inserir Código** no canto superior direito da consulta. Isso transfere o SQL gerado do painel do Copilot para o editor de SQL, no qual você pode executá-lo.

    ![Captura de tela do painel do Copilot com a primeira consulta sql.](./Images/copilot-fabric-data-warehouse-sql-1.png)

1. Execute a consulta selecionando a opção ▷ **Executar** acima da consulta e observe a saída. Você deve ver os totais de receita mensais que demonstram como os dados de vendas são agregados em períodos de tempo.

    ![Captura de tela dos resultados da consulta SQL.](./Images/copilot-fabric-data-warehouse-sql-1-results.png)

1. Crie uma **Consulta SQL** e faça uma pergunta de acompanhamento para incluir também o nome do mês e a região de vendas nos resultados. Isso demonstra como você pode refinar iterativamente suas consultas com o Copilot com base em solicitações anteriores para criar uma análise mais detalhada:

    ```copilot-prompt
    /generate-sql Retrieves sales revenue data grouped by year, month, month name and sales region
    ```

1. Selecione o ícone **Inserir Código** e ▷ **Executar** a consulta. Observe a saída retornada. Observe como o Copilot adapta a consulta para incluir mais dimensões mantendo a lógica de cálculo de receita principal.

1. Vamos criar uma exibição com base nessa consulta fazendo a seguinte pergunta ao Copilot. As exibições são tabelas virtuais que armazenam a lógica de consulta, facilitando a reutilização de consultas complexas e fornecendo padrões de acesso a dados consistentes para relatórios e análise:

    ```copilot-prompt
    /generate-sql Create a view in the dbo schema that shows sales revenue data grouped by year, month, month name and sales region
    ```

1. Selecione o ícone **Inserir Código** e ▷ **Executar** a consulta. Examine a saída gerada. 

    A consulta não é executada com êxito porque a instrução SQL inclui o nome do banco de dados como um prefixo, o que não é permitido no data warehouse ao definir uma exibição. Esse é um problema de sintaxe comum ao trabalhar em diferentes plataformas de banco de dados. O que funciona em um ambiente pode precisar de ajustes em outro.

1. Selecione a opção **Corrigir erros de consulta**. Observe como o Copilot faz correções na consulta. Isso demonstra um dos recursos avançados do Copilot: não só pode gerar consultas, como também pode automaticamente solucionar problemas e corrigir erros de sintaxe.

    ![Captura de tela da consulta SQL com erro.](./Images/copilot-fabric-data-warehouse-view-error.png)
    
    Veja um exemplo da consulta corrigida. Observe os comentários `Auto-Fix` que explicam quais alterações foram feitas:
    
    ```sql
    -- Auto-Fix: Removed the database name prefix from the CREATE VIEW statement
    CREATE VIEW [dbo].[SalesRevenueView] AS
    SELECT 
        [DD].[Year],
        [DD].[Month],
        [DD].[MonthName],
        -- NOTE: I couldn't find SalesRegion information in your warehouse schema
        SUM([FS1].[SalesTotal]) AS [TotalRevenue]
    FROM 
        [dbo].[FactSalesOrder] AS [FS1] -- Auto-Fix: Removed the database name prefix
    JOIN 
        [dbo].[DimDate] AS [DD] ON [FS1].[SalesOrderDateKey] = [DD].[DateKey] -- Auto-Fix: Removed the database name prefix
    -- NOTE: I couldn't find SalesRegion information in your warehouse schema
    GROUP BY 
        [DD].[Year],
        [DD].[Month],
        [DD].[MonthName]; 
    ```
    
    Observe como o Copilot não apenas corrigiu os erros de sintaxe, como também fez comentários úteis explicando as alterações e notando que as informações da região de vendas não estão disponíveis no esquema atual.

1. Insira outro prompt para recuperar uma listagem detalhada de produtos, organizada por categoria. Essa consulta demonstrará recursos SQL mais avançados, como funções de janela para classificar dados em grupos. Para cada categoria de produto, ele deve exibir os produtos disponíveis e seus preços de lista, além de classificá-los nas respectivas categorias com base no preço. 

    ```copilot-prompt
    /generate-sql Retrieve a detailed product listing, organized by category. For each product category, it should display the available products along with their list prices and rank them within their respective categories based on price. 
    ```

1. Selecione o ícone **Inserir Código** e ▷ **Executar** a consulta. Observe a saída retornada. 

    Isso permite uma fácil comparação de produtos na mesma categoria, ajudando a identificar os itens mais e menos caros. A funcionalidade de classificação é particularmente útil para gerenciamento de produtos, análise de preços e decisões de inventário.

## Resumo

Neste exercício, você criou um data warehouse que contém várias tabelas. Você usou o Copilot para gerar consultas SQL para analisar dados no data warehouse. Você viu como a IA pode acelerar o processo de escrever consultas SQL complexas, corrigir erros automaticamente e ajudar a explorar dados com mais eficiência.

Ao longo deste laboratório, você aprendeu a:
- Aproveitar os prompts de linguagem natural para gerar consultas SQL
- Usar os recursos de correção de erros do Copilot para corrigir problemas de sintaxe
- Criar exibições e consultas analíticas complexas com a assistência de IA
- Aplicar funções de classificação e agrupamento para análise de dados

## Limpar os recursos

Se você terminou de explorar o Copilot no data warehouse do Microsoft Fabric, pode excluir o workspace criado para este exercício.

1. Navegue até o Microsoft Fabric no navegador.
1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
1. Clique em **Configurações do espaço de trabalho** e, na seção **Geral**, role para baixo e selecione **Remover este espaço de trabalho**.
1. Clique em **Excluir** para excluir o espaço de trabalho.


