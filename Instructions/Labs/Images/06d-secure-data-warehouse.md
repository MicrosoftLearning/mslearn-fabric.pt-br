---
lab:
  title: Proteger os dados em um data warehouse
  module: Get started with data warehouses in Microsoft Fabric
---

# Proteger os dados em um data warehouse

As permissões do Microsoft Fabric e as permissões granulares do SQL trabalham em conjunto para controlar o acesso ao Warehouse e as permissões de usuário. Neste exercício, você protegerá os dados usando permissões granulares, segurança em nível de coluna, segurança em nível de linha e máscara dinâmica de dados.

Este laboratório levará aproximadamente **45** minutos para ser concluído.

> **Observação**: Você precisará uma [avaliação gratuita do Microsoft Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para concluir este exercício.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Na [página inicial do Microsoft Fabric](https://app.fabric.microsoft.com), selecione **Data Warehouse do Synapse**.
1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-empty-workspace.png)

## Criar um data warehouse

Em seguida, crie um data warehouse no espaço de trabalho que você acabou de criar. A página inicial do Data Warehouse do Synapse inclui um atalho para criar um novo warehouse:

1. Na página inicial do **Synapse Data Warehouse**, crie um novo **Warehouse** com um nome de sua escolha.

    Após alguns minutos, um warehouse será criado:

    ![Captura de tela de um novo warehouse.](./Images/new-empty-data-warehouse.png)

## Aplicar Máscara Dinâmica de Dados a colunas em uma tabela

As regras de máscara dinâmica de dados são aplicadas em colunas individuais no nível da tabela, de modo que todas as consultas são afetadas pela máscara. Os usuários que não tiverem permissões explícitas para exibir dados confidenciais verão os valores mascarados nos resultados da consulta, enquanto os usuários com permissão explícita para exibir os dados os verão sem máscara. Há quatro tipos de máscaras: padrão, email, aleatória e cadeia de caracteres personalizada. Neste exercício, você aplicará uma máscara padrão, uma máscara de email e uma máscara de cadeia de caracteres personalizada.

1. Em seu warehouse, selecione o bloco **T-SQL** e substitua o código SQL padrão pelas seguintes instruções T-SQL para criar uma tabela e para inserir e exibir os dados.  As máscaras aplicadas na instrução `CREATE TABLE` fazem o seguinte:

    ```sql
    CREATE TABLE dbo.Customer
    (   
        CustomerID INT NOT NULL,   
        FirstName varchar(50) MASKED WITH (FUNCTION = 'partial(1,"XXXXXXX",0)') NULL,     
        LastName varchar(50) NOT NULL,     
        Phone varchar(20) MASKED WITH (FUNCTION = 'default()') NULL,     
        Email varchar(50) MASKED WITH (FUNCTION = 'email()') NULL   
    );
    GO
    --Users restricted from seeing masked data will see the following when they query the table
    --The FirstName column shows the first letter of the string with XXXXXXX and none of the last characters.
    --The Phone column shows xxxx
    --The Email column shows the first letter of the email address followed by XXX@XXX.com.
    
    INSERT dbo.Customer (CustomerID, FirstName, LastName, Phone, Email) VALUES
    (29485,'Catherine','Abel','555-555-5555','catherine0@adventure-works.com'),
    (29486,'Kim','Abercrombie','444-444-4444','kim2@adventure-works.com'),
    (29489,'Frances','Adams','333-333-3333','frances0@adventure-works.com');
    GO

    SELECT * FROM dbo.Customer;
    GO
    ```

2. Use o botão **&#9655; Executar** para executar o script SQL, que cria uma nova tabela chamada **Cliente** no esquema **dbo** do data warehouse.

3. Em seguida, no painel **Explorador**, expanda **Esquemas** > **dbo** > **Tabelas** e verifique se a tabela **Cliente** foi criada. A instrução SELECT retorna dados sem máscara porque você está conectado como administrador do espaço de trabalho, que pode ver dados sem máscara.

4. Conecte-se como um usuário de teste que seja membro da função de espaço de trabalho **visualizador** e execute a seguinte instrução T-SQL.

    ```sql
    SELECT * FROM dbo.Customer;
    GO
    ```

    Esse usuário não recebeu a permissão UNMASK, portanto, os dados retornados para as colunas FirstName, Telefone e Email estão mascarados porque essas colunas foram definidas com uma máscara na instrução `CREATE TABLE`.

5. Reconecte-se como administrador do espaço de trabalho e execute o seguinte T-SQL para desmascarar os dados para o usuário de teste.

    ```sql
    GRANT UNMASK ON dbo.Customer TO [testUser@testdomain.com];
    GO
    ```

6. Conecte-se novamente como usuário de teste e execute a seguinte instrução T-SQL.

    ```sql
    SELECT * FROM dbo.Customer;
    GO
    ```

    Os dados são retornados sem mascaramento porque o usuário de teste recebeu a permissão `UNMASK`.

## Aplicar segurança em nível de linha

A segurança em nível de linha (RLS) pode ser usada para limitar o acesso às linhas com base na identidade ou função do usuário que executa uma consulta.  Neste exercício, você restringirá o acesso às linhas criando uma política de segurança e um predicado de segurança definido como uma função com valor de tabela embutida.

1. No depósito que você criou no último exercício, selecione a lista suspensa **Nova Consulta SQL**.  No menu suspenso sob o cabeçalho **em Branco**, selecione **Nova Consulta SQL**.

2. Crie uma tabela e insira dados nela. Para que possa testar a segurança em nível de linha em uma etapa posterior, substitua "testuser1@mydomain.com" por um nome de usuário do seu ambiente e substitua "testuser2@mydomain.com" pelo seu nome de usuário.
    ```sql
    CREATE TABLE dbo.Sales  
    (  
        OrderID INT,  
        SalesRep VARCHAR(60),  
        Product VARCHAR(10),  
        Quantity INT  
    );
    GO
     
    --Populate the table with 6 rows of data, showing 3 orders for each test user. 
    INSERT dbo.Sales (OrderID, SalesRep, Product, Quantity) VALUES
    (1, 'testuser1@mydomain.com', 'Valve', 5),   
    (2, 'testuser1@mydomain.com', 'Wheel', 2),   
    (3, 'testuser1@mydomain.com', 'Valve', 4),  
    (4, 'testuser2@mydomain.com', 'Bracket', 2),   
    (5, 'testuser2@mydomain.com', 'Wheel', 5),   
    (6, 'testuser2@mydomain.com', 'Seat', 5);  
    GO
   
    SELECT * FROM dbo.Sales;  
    GO
    ```

3. Use o botão **&#9655; Executar** para executar o script SQL, que cria uma nova tabela chamada **Vendas** no esquema **dbo** do data warehouse.

4. Em seguida, no painel **Explorador**, expanda **Esquemas** > **dbo** > **Tabela** e verifique se a tabela **Vendas** foi criada.
5. Crie um esquema, um predicado de segurança definido como uma função e uma política de segurança.  

    ```sql
    --Create a separate schema to hold the row-level security objects (the predicate function and the security policy)
    CREATE SCHEMA rls;
    GO
    
    --Create the security predicate defined as an inline table-valued function. A predicate evalutes to true (1) or false (0). This security predicate returns 1, meaning a row is accessible, when a row in the SalesRep column is the same as the user executing the query.

    --Create a function to evaluate which SalesRep is querying the table
    CREATE FUNCTION rls.fn_securitypredicate(@SalesRep AS VARCHAR(60)) 
        RETURNS TABLE  
    WITH SCHEMABINDING  
    AS  
        RETURN SELECT 1 AS fn_securitypredicate_result   
    WHERE @SalesRep = USER_NAME();
    GO 
    
    --Create a security policy to invoke and enforce the function each time a query is run on the Sales table. The security policy has a Filter predicate that silently filters the rows available to read operations (SELECT, UPDATE, and DELETE). 
    CREATE SECURITY POLICY SalesFilter  
    ADD FILTER PREDICATE rls.fn_securitypredicate(SalesRep)   
    ON dbo.Sales  
    WITH (STATE = ON);
    GO
6. Use the **&#9655; Run** button to run the SQL script
7. Then, in the **Explorer** pane, expand **Schemas** > **rls** > **Functions**, and verify that the function has been created.
7. Confirm that you're logged as another user by running the following T-SQL.

    ```sql
    SELECT USER_NAME();
    GO
5. Query the sales table to confirm that row-level security works as expected. You should only see data that meets the conditions in the security predicate defined for the user you're logged in as.

    ```sql
    SELECT * FROM dbo.Sales;
    GO

## Implement column-level security

Column-level security allows you to designate which users can access specific columns in a table. It is implemented by issuing a GRANT statement on a table specifying a list of columns and the user or role that can read them. To streamline access management, assign permissions to roles in lieu of individual users. In this exercise, you will create a table, grant access to a subset of columns on the table, and test that restricted columns are not viewable by a user other than yourself.

1. In the warehouse you created in the earlier exercise, select the **New SQL Query** dropdown.  Under the dropdown under the header **Blank**, select **New SQL Query**.  

2. Create a table and insert data into the table.

 ```sql
    CREATE TABLE dbo.Orders
    (   
        OrderID INT,   
        CustomerID INT,  
        CreditCard VARCHAR(20)      
        );
    GO

    INSERT dbo.Orders (OrderID, CustomerID, CreditCard) VALUES
    (1234, 5678, '111111111111111'),
    (2341, 6785, '222222222222222'),
    (3412, 7856, '333333333333333');
    GO

    SELECT * FROM dbo.Orders;
    GO
 ```

3. Negue a permissão para exibir uma coluna na tabela. O Transact SQL abaixo impedirá que '<testuser@mydomain.com>' veja a coluna CreditCard na tabela Pedidos. Na instrução `DENY` abaixo, substitua testuser@mydomain.com por um nome de usuário em seu sistema que tenha permissões de Visualizador no espaço de trabalho.

 ```sql
DENY SELECT ON dbo.Orders (CreditCard) TO [testuser@mydomain.com];
 ```

4. Teste a segurança no nível da coluna fazendo logon no Fabric como o usuário ao qual você negou as permissões de seleção.

5. Consulte a tabela Pedidos para confirmar que a segurança em nível de coluna funciona como esperado. A consulta a seguir retornará apenas as colunas OrderID e CustomerID, não a coluna CrediteCard.  

    ```sql
    SELECT * FROM dbo.Orders;
    GO

    --You'll receive an error because access to the CreditCard column has been restricted.  Try selecting only the OrderID and CustomerID fields and the query will succeed.

    SELECT OrderID, CustomerID from dbo.Orders
    ```

## Configurar permissões granulares de SQL usando T-SQL

O Fabric warehouse tem um modelo de permissões que permite controlar o acesso aos dados no nível do espaço de trabalho e no nível do item. Quando você precisar de um controle mais granular do que os usuários podem fazer com os objetos seguros em um warehouse do Fabric, poderá usar os comandos padrão da linguagem de controle de dados SQL (DCL) `GRANT`,`DENY` e `REVOKE`. Neste exercício, você criará objetos, os protegerá usando `GRANT` e `DENY` e, em seguida, executará consultas para exibir o efeito da aplicação de permissões granulares.

1. No depósito que você criou no exercício anterior, selecione o menu suspenso **Nova Consulta SQL**.  Sob o cabeçalho **em Branco**, selecione **Nova Consulta SQL**.  

2. Crie um procedimento armazenado e uma tabela.

 ```
    CREATE PROCEDURE dbo.sp_PrintMessage
    AS
    PRINT 'Hello World.';
    GO
  
    CREATE TABLE dbo.Parts
    (
        PartID INT,
        PartName VARCHAR(25)
    );
    GO
    
    INSERT dbo.Parts (PartID, PartName) VALUES
    (1234, 'Wheel'),
    (5678, 'Seat');
    GO  
    
    --Execute the stored procedure and select from the table and note the results you get because you're a member of the Workspace Admin. Look for output from the stored procedure on the 'Messages' tab.
      EXEC dbo.sp_PrintMessage;
    GO
    
    SELECT * FROM dbo.Parts
    GO
  ```

3. Em seguida, dê `DENY SELECT` permissões na tabela a um usuário que seja membro da função Visualizador do Espaço de Trabalho e `GRANT EXECUTE` no procedimento para o mesmo usuário.

 ```sql
    DENY SELECT on dbo.Parts to [testuser@mydomain.com];
    GO

    GRANT EXECUTE on dbo.sp_PrintMessage to [testuser@mydomain.com];
    GO

 ```

4. Faça logon no Fabric como o usuário que você especificou nas instruções NEGAR e CONCEDER acima no lugar de [testuser@mydomain.com]. Em seguida, teste as permissões granulares que você acabou de aplicar executando o procedimento armazenado e consultando a tabela.  

 ```sql
    EXEC dbo.sp_PrintMessage;
    GO
    
    SELECT * FROM dbo.Parts
 ```

## Limpar os recursos

Neste exercício, você aplicou o máscara dinâmica de dados às colunas de uma tabela, aplicou a segurança em nível de linha, implementou a segurança em nível de coluna e configurou as permissões granulares do SQL usando o T-SQL.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Geral**, selecione **Remover este espaço de trabalho**.
