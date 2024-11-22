---
lab:
  title: Trabalhar com o Banco de Dados SQL no Microsoft Fabric
  module: Get started with SQL Database in Microsoft Fabric
---

# Trabalhar com o Banco de Dados SQL no Microsoft Fabric

O banco de dados SQL no Microsoft Fabric é um banco de dados transacional fácil de o desenvolvedor usar, baseado no Banco de Dados SQL do Azure, que permite que você crie seu banco de dados operacional no Fabric com facilidade. Um banco de dados SQL no Fabric usa o mecanismo do Banco de Dados SQL como Banco de Dados SQL do Azure.

Este laboratório levará aproximadamente **30** minutos para ser concluído.

> **Observação**: Você precisará uma [avaliação gratuita do Microsoft Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para concluir esse exercício.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Na [home page do Microsoft Fabric](https://app.fabric.microsoft.com/home?experience=fabric) em `https://app.fabric.microsoft.com/home?experience=fabric`.
1. Na barra de menus à esquerda, selecione **Novo espaço de trabalho**.
1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

## Criar um banco de dados SQL com dados de exemplo

Agora que você tem um workspace, é hora de criar um banco de dados SQL.

1. No portal do Fabric, selecione **+ Novo item** no painel esquerdo.
1. Navegue até a seção **Banco de dados** e selecione **Bancos de dados SQL**.
1. Insira **AdventureWorksLT** como o nome do banco de dados e clique em **Criar**.
1. Depois de criar o banco de dados, você poderá carregar os dados de amostra no banco de dados com o cartão **Dados de amostra**.

    Depois de mais ou menos um minuto, seu banco de dados será preenchido com dados de amostra para seu cenário.

    ![Captura de tela de um novo banco de dados carregado com dados de amostra.](./Images/sql-database-sample.png)

## Consultar um banco de dados SQL

O editor de consulta SQL dá suporte para IntelliSense, preenchimento de código, realce de sintaxe, análise no lado do cliente e validação. Você pode executar instruções de DDL (Linguagem de Definição de Dados), DML (Linguagem de Manipulação de Dados) e DCL (Linguagem de Controle de Dados).

1. Na página do banco de dados **AdventureWorksLT**, navegue até **Página Inicial** e clique em **Nova consulta**.

1. No novo painel de consulta em branco, insira e execute o seguinte código T-SQL.

    ```sql
    SELECT 
        p.Name AS ProductName,
        pc.Name AS CategoryName,
        p.ListPrice
    FROM 
        SalesLT.Product p
    INNER JOIN 
        SalesLT.ProductCategory pc ON p.ProductCategoryID = pc.ProductCategoryID
    ORDER BY 
    p.ListPrice DESC;
    ```
    
    Essa consulta une as tabelas `Product` e `ProductCategory` para exibir os nomes dos produtos, suas categorias e seus preços de lista, classificados por preço em ordem decrescente.

1. No novo editor de consultas, insira e execute o seguinte código T-SQL.

    ```sql
   SELECT 
        c.FirstName,
        c.LastName,
        soh.OrderDate,
        soh.SubTotal
    FROM 
        SalesLT.Customer c
    INNER JOIN 
        SalesLT.SalesOrderHeader soh ON c.CustomerID = soh.CustomerID
    ORDER BY 
        soh.OrderDate DESC;
    ```

    Essa consulta recupera uma lista de clientes junto com as datas de pedido e subtotais, classificados pela data do pedido em ordem decrescente. 

1. Feche todas as guias de consulta.

## Integrar dados com fontes de dados externas

Você integrará dados externos sobre feriados com ordem de venda. Em seguida, você identificará pedidos de vendas que coincidem com feriados, fornecendo informações sobre como os feriados podem afetar as atividades de vendas.

1. Navegue até **Página Inicial** e clique em **Nova consulta**.

1. No novo painel de consulta em branco, insira e execute o seguinte código T-SQL.

    ```sql
    CREATE TABLE SalesLT.PublicHolidays (
        CountryOrRegion NVARCHAR(50),
        HolidayName NVARCHAR(100),
        Date DATE,
        IsPaidTimeOff BIT
    );
    ```

    Essa consulta criará a tabela `SalesLT.PublicHolidays` em preparação para a próxima etapa.

1. No novo editor de consultas, insira e execute o seguinte código T-SQL.

    ```sql
    INSERT INTO SalesLT.PublicHolidays (CountryOrRegion, HolidayName, Date, IsPaidTimeOff)
    SELECT CountryOrRegion, HolidayName, Date, IsPaidTimeOff
    FROM OPENROWSET 
    (BULK 'abs://holidaydatacontainer@azureopendatastorage.blob.core.windows.net/Processed/*.parquet'
    , FORMAT = 'PARQUET') AS [PublicHolidays]
    WHERE countryorRegion in ('Canada', 'United Kingdom', 'United States')
        AND YEAR([date]) = 2024
    ```
    
    Essa consulta lerá dados de feriados de arquivos Parquet no Armazenamento de Blobs do Azure, irá filtra-los para incluir apenas feriados no Canadá, no Reino Unido e nos Estados Unidos em 2024 e, em seguida, inserirá esses dados filtrados na tabela `SalesLT.PublicHolidays`.    

1. Em um editor de consultas novo ou já existente, insira e execute o código T-SQL a seguir.

    ```sql
    -- Insert new addresses into SalesLT.Address
    INSERT INTO SalesLT.Address (AddressLine1, City, StateProvince, CountryRegion, PostalCode, rowguid, ModifiedDate)
    VALUES
        ('123 Main St', 'Seattle', 'WA', 'United States', '98101', NEWID(), GETDATE()),
        ('456 Maple Ave', 'Toronto', 'ON', 'Canada', 'M5H 2N2', NEWID(), GETDATE()),
        ('789 Oak St', 'London', 'England', 'United Kingdom', 'EC1A 1BB', NEWID(), GETDATE());
    
    -- Insert new orders into SalesOrderHeader
    INSERT INTO SalesLT.SalesOrderHeader (
        SalesOrderID, RevisionNumber, OrderDate, DueDate, ShipDate, Status, OnlineOrderFlag, 
        PurchaseOrderNumber, AccountNumber, CustomerID, ShipToAddressID, BillToAddressID, 
        ShipMethod, CreditCardApprovalCode, SubTotal, TaxAmt, Freight, Comment, rowguid, ModifiedDate
    )
    VALUES
        (1001, 1, '2024-12-25', '2024-12-30', '2024-12-26', 1, 1, 'PO12345', 'AN123', 1, (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '789 Oak St'), (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '123 Main St'), 'Ground', '12345', 100.00, 10.00, 5.00, 'New Order 1', NEWID(), GETDATE()),
        (1002, 1, '2024-11-28', '2024-12-03', '2024-11-29', 1, 1, 'PO67890', 'AN456', 2, (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '123 Main St'), (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '456 Maple Ave'), 'Air', '67890', 200.00, 20.00, 10.00, 'New Order 2', NEWID(), GETDATE()),
        (1003, 1, '2024-02-19', '2024-02-24', '2024-02-20', 1, 1, 'PO54321', 'AN789', 3, (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '456 Maple Ave'), (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '789 Oak St'), 'Sea', '54321', 300.00, 30.00, 15.00, 'New Order 3', NEWID(), GETDATE()),
        (1004, 1, '2024-05-27', '2024-06-01', '2024-05-28', 1, 1, 'PO98765', 'AN321', 4, (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '789 Oak St'), (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '789 Oak St'), 'Ground', '98765', 400.00, 40.00, 20.00, 'New Order 4', NEWID(), GETDATE());
    ```

    Este código adiciona novos endereços e pedidos ao banco de dados, simulando pedidos fictícios de diferentes países.

1. Em um editor de consultas novo ou já existente, insira e execute o código T-SQL a seguir.

    ```sql
    SELECT DISTINCT soh.SalesOrderID, soh.OrderDate, ph.HolidayName, ph.CountryOrRegion
    FROM SalesLT.SalesOrderHeader AS soh
    INNER JOIN SalesLT.Address a
        ON a.AddressID = soh.ShipToAddressID
    INNER JOIN SalesLT.PublicHolidays AS ph
        ON soh.OrderDate = ph.Date AND a.CountryRegion = ph.CountryOrRegion
    ```

    Reserve um momento para observar os resultados, observando como a consulta identifica pedidos de venda que coincidem com feriados nos respectivos países. Isso pode fornecer informações valiosas sobre padrões de pedidos e possíveis impactos de feriados nas atividades de vendas.

1. Feche todas as guias de consulta.

## Proteger dados

Suponha que um grupo específico de usuários só tenha acesso a dados dos Estados Unidos para gerar relatórios.

Vamos criar uma exibição com base na consulta que usamos anteriormente e adicionar um filtro a ela.

1. No novo painel de consulta em branco, insira e execute o seguinte código T-SQL.

    ```sql
    CREATE VIEW SalesLT.vw_SalesOrderHoliday AS
    SELECT DISTINCT soh.SalesOrderID, soh.OrderDate, ph.HolidayName, ph.CountryOrRegion
    FROM SalesLT.SalesOrderHeader AS soh
    INNER JOIN SalesLT.Address a
        ON a.AddressID = soh.ShipToAddressID
    INNER JOIN SalesLT.PublicHolidays AS ph
        ON soh.OrderDate = ph.Date AND a.CountryRegion = ph.CountryOrRegion
    WHERE a.CountryRegion = 'United Kingdom';
    ```

1. Em um editor de consultas novo ou já existente, insira e execute o código T-SQL a seguir.

    ```sql
    -- Create the role
    CREATE ROLE SalesOrderRole;
    
    -- Grant select permission on the view to the role
    GRANT SELECT ON SalesLT.vw_SalesOrderHoliday TO SalesOrderRole;
    ```

    Qualquer usuário adicionado como membro à função `SalesOrderRole` terá acesso apenas à exibição filtrada. Se um usuário nessa função tentar acessar qualquer outro objeto de usuário, será exibida uma mensagem de erro semelhante a esta:

    ```
    Msg 229, Level 14, State 5, Line 1
    The SELECT permission was denied on the object 'ObjectName', database 'DatabaseName', schema 'SchemaName'.
    ```

> **Mais informações**: Consulte [O que é o Microsoft Fabric?](https://learn.microsoft.com/fabric/get-started/microsoft-fabric-overview) na documentação do Microsoft Fabric para saber mais sobre outros componentes disponíveis na plataforma.

Neste exercício, você criou, importou dados externos, consultou e protegeu dados em um banco de dados SQL no Microsoft Fabric.

## Limpar os recursos

Se você tiver terminado de explorar seu banco de dados, exclua o workspace criado para este exercício.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Geral**, selecione **Remover este espaço de trabalho**.
