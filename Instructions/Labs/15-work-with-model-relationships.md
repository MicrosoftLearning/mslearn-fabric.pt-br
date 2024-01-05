---
lab:
  title: Trabalhar com relações de modelo
  module: Design and build tabular models
---

# Trabalhar com relações de modelo

## Visão geral

**O tempo estimado para concluir o laboratório é de 45 minutos**

Neste laboratório, você trabalhará com relações de modelo, especificamente para abordar a necessidade de dimensões de interpretação de papéis. Isso envolverá trabalhar com relações ativas e inativas, e também funções DAX (Data Analysis Expressions) que modificam o comportamento da relação.

Neste laboratório, você aprenderá a:

- Interpretar propriedades de relação no diagrama de modelo.

- Definir propriedades de relação

- Usar funções DAX que modificam o comportamento da relação

## Explorar relações de modelo

Neste exercício, você abrirá uma solução pré-desenvolvida do Power BI Desktop para aprender sobre o modelo de dados. Em seguida, você explorará o comportamento de relações de modelo ativo.

## Introdução
### Clonar o repositório para este curso

1. No menu Iniciar, abra o Prompt de Comando
   
    ![](../images/command-prompt.png)

2. Na janela do prompt de comando, navegue até a unidade D digitando:

    `d:` 

   Pressione ENTER.
   
    ![](../images/command-prompt-2.png)


3. Na janela do prompt de comando, digite o seguinte comando para baixar os arquivos do curso e salve-os em uma pasta chamada DP500.
    
    `git clone https://github.com/MicrosoftLearning/DP-500-Azure-Data-Analyst DP500`
   

1. Quando o repositório tiver sido clonado, feche a janela do prompt de comando. 
   
1. Abra a unidade D no explorador de arquivos para garantir que os arquivos tenham sido baixados.

### Configurar o Power BI Desktop

Nesta tarefa, você abrirá uma solução pré-desenvolvida do Power BI Desktop.

1. Para abrir o Explorador de Arquivos, na barra de tarefas, selecione o atalho do **Explorador de Arquivos**.

2. No Explorador de Arquivos, vá para a pasta **D:\DP500\Allfiles\06\Starter** .

3. Para abrir um arquivo pré-desenvolvido do Power BI Desktop, clique duas vezes no arquivo**Sales Analysis - Work with model relationships.pbix** .

4. Para salvar o arquivo, na guia **Arquivo** da faixa de opções, selecione **Salvar Como**.

5. Na janela **Salvar como**, procure a pasta **D:\DP500\Allfiles\06\MySolution**.

6. Selecione **Salvar**.

### Examinar o modelo de dados

Nesta tarefa, você revisará o modelo de dados.

1. No Power BI Desktop, alterne para a exibição de **Modelo** à esquerda.

    ![](../images/dp500-work-with-model-relationships-image2.png)

2. Use o diagrama de modelo para examinar o design do modelo.

    ![](../images/dp500-work-with-model-relationships-image3.png)

    *O modelo é composto por seis tabelas dimensionais e uma tabela de fatos. A tabela de fatos **Vendas** armazena os detalhes da ordem de venda do cliente. É um design clássico de esquema de estrelas.*
 
3. Observe que há três relações entre as tabelas **Data** **Vendas**

    ![](../images/dp500-work-with-model-relationships-image4.png)

    *A coluna **DateKey** na tabela **Data** é uma coluna exclusiva que representa o lado "um" das relações. Os filtros aplicados a qualquer coluna da tabela **Data** se propagam para a tabela **Vendas** usando uma das relações.*

4. Passe o cursor sobre cada uma das três relações para realçar a coluna lateral "muitas" na tabela **Vendas**.

5. Observe que a relação com a coluna **OrderDateKey** é uma linha sólida, enquanto as outras relações são representadas por uma linha pontilhada.

    *Uma linha sólida representa uma relação ativa. Só pode haver um caminho de relação ativa entre duas tabelas modelo, e o caminho é usado por padrão para propagar filtros entre tabelas. Por outro lado, uma linha pontilhada representa uma relação inativa. Relações inativas são usadas​somente quando invocadas explicitamente por fórmulas DAX.*

    *O design do modelo atual indica que a tabela **Data** é uma dimensão de role-playing. Essa dimensão pode desempenhar a função de data do pedido, data de vencimento ou data de remessa. Qual função depende dos requisitos analíticos do relatório.*

    *Neste laboratório, você aprenderá a projetar um modelo para dar suporte a dimensões de interpretação de funções.*

### Visualizar dados da data

Nesta tarefa, você visualizará os dados de vendas por data e alternará o status ativo das relações.

1. Alterne para a exibição de **Relatório**.

    ![](../images/dp500-work-with-model-relationships-image5.png)

2. Para adicionar um visual de tabela, no painel **Visualizações**, selecione o ícone do visual de **Tabela**.

    ![](../images/dp500-work-with-model-relationships-image6.png)

3. Para adicionar colunas ao visual da tabela, no painel **Dados** (localizado à direita), primeiro expanda a tabela **Data**.

    ![](../images/dp500-work-with-model-relationships-image7.png)

4. Arraste a coluna **Ano Fiscal** e solte-a no visual da tabela.

    ![](../images/dp500-work-with-model-relationships-image8.png)

5. Abra a tabela **Vendas** e arraste e solte a coluna **Valores de vendas** no visual da tabela.

    ![](../images/dp500-work-with-model-relationships-image9.png)

6. Examine o visual da tabela.

    ![](../images/dp500-work-with-model-relationships-image10.png)

    *O visual da tabela mostra a soma da coluna **Valor de Vendas** agrupada por ano. Mas o que significa **Ano Fiscal**? Como existe uma relação ativa entre as tabelas **Data** e **Vendas** com a coluna **OrderDateKey**, **Ano Fiscal** significa o ano fiscal em que os pedidos foram feitos.*

    *Para esclarecer qual ano fiscal, é uma boa ideia renomear o campo visual (ou adicionar um título ao visual).*

7. No painel **Visualizações** para o visual da tabela, de dentro da caixa **Valores**, selecione a seta para baixo e **Renomeie para este visual**.

    ![](../images/dp500-work-with-model-relationships-image11.png)

8. Substitua o texto por **Ano do Pedido** e pressione **Enter**.

    ![](../images/dp500-work-with-model-relationships-image12.png)

    *Dica: é mais rápido renomear um campo visual clicando duas vezes em seu nome.*

9. Observe que o cabeçalho da coluna visual da tabela é atualizado para o novo nome.

    ![](../images/dp500-work-with-model-relationships-image13.png)

### Modificar o status ativo da relação

Nesta tarefa, você modificará o status ativo de duas relações.

1. Na faixa de opções **Modelagem**, selecione **Gerenciar relações**.

    ![](../images/dp500-work-with-model-relationships-image14.png)

2. Na janela **Gerenciar Relações**, para a relação entre as tabelas **Vendas** e **Data** da coluna **OrderDateKey** (terceira na lista), desmarque a caixa de seleção **Ativo**.

    ![](../images/dp500-work-with-model-relationships-image15.png)

3. Marque a caixa de seleção **Ativo** para a relação entre as tabelas **Vendas** e **Data** na coluna **ShipDateKey** (última na lista).

    ![](../images/dp500-work-with-model-relationships-image16.png)

4. Selecione **Fechar**.

    ![](../images/dp500-work-with-model-relationships-image17.png)

    *Essas configurações mudaram a relação ativa entre as tabelas **Data** e **Vendas** para a coluna **ShipDateKey**.*

5. Examine o visual da tabela que agora mostra os valores de vendas agrupados por anos de remessa.

    ![](../images/dp500-work-with-model-relationships-image18.png)

6. Renomeie a primeira coluna como **Ano de Remessa**.

    ![](../images/dp500-work-with-model-relationships-image19.png)

    *A primeira linha representa um grupo em branco porque alguns pedidos ainda não foram enviados. Em outras palavras, há BLANKs na coluna **ShipDateKey** da tabela **Vendas**.*

7. Na janela **Gerenciar relações**, reverta a relação **OrderDateKey** de volta para ativa com as seguintes etapas:

    - Abra a janela **Gerenciar relações**.

    - Desmarque a caixa de seleção **Ativo** para a relação **ShipDateKey** (última da lista)

    - Marque a caixa de seleção **Ativo** para a relação **OrderDateKey** (terceira da lista).

    - Feche a janela **Gerenciar relações**.

    - Renomear o primeiro campo no visual da tabela como **Ano do pedido**

    ![](../images/dp500-work-with-model-relationships-image20.png)

    *No próximo exercício, você aprenderá como tornar uma relação ativa em uma fórmula DAX.*

## Usar relações inativas

Neste exercício, você aprenderá como tornar uma relação ativa em uma fórmula DAX.

### Usar relações inativas

Nesta tarefa, você usará a função USERELATIONSHIP para ativar uma relação inativa.

1. No painel **Dados**, clique com o botão direito do mouse na tabela **Vendas** e selecione **Nova medida**.

    ![](../images/dp500-work-with-model-relationships-image21.png)

2. Na barra de fórmulas (localizada abaixo da faixa de opções), substitua o texto pela definição de medida a seguir e pressione **Enter**.

    *Dica: é possível copiar e colar todas as fórmulas de **D:\DP500\Allfiles\06\Assets\Snippets.txt**.*

    ```
    Sales Shipped =
    CALCULATE (
    SUM ( 'Sales'[Sales Amount] ),
    USERELATIONSHIP ( 'Date'[DateKey], 'Sales'[ShipDateKey] )
    )
    ``` 
    *Essa fórmula usa a função CALCULATE para modificar o contexto do filtro. É a função USERELATIONSHIP que, para fins deste cálculo, ativa a relação **ShipDateKey**.*

3. Nas faixa de opções contextual **Ferramentas de Medida**, dentro do grupo **Formatação**, defina o número de casas decimais como **2**.

    ![](../images/dp500-work-with-model-relationships-image22.png)

4. Adicione a medida **Vendas Enviadas** ao visual da tabela.

    ![](../images/dp500-work-with-model-relationships-image23.png)

5. Amplie o visual da tabela para que todas as colunas fiquem totalmente visíveis.

    ![](../images/dp500-work-with-model-relationships-image24.png)

    *Criar medidas que definam temporariamente as relações como ativas é uma forma de trabalhar com dimensões de role-playing. No entanto, pode tornar-se entediante quando há necessidade de criar versões de role-playing para muitas medidas. Por exemplo, se houvesse 10 medidas relacionadas com vendas e três datas de role-playing, isso poderia significar a criação de 30 medidas. Criá-las com grupos de cálculo poderia facilitar o processo.*

    *Outra abordagem é criar uma tabela modelo diferente para cada dimensão do role-playing. Você fará isso no próximo exercício.*

6. Para remover a medida do visual da tabela, no painel **Visualizações**, de dentro da caixa**Valores**, para o campo **Vendas Enviadas**, pressione **X**.

    ![](../images/dp500-work-with-model-relationships-image25.png)

## Adicionar outra tabela Data

Neste exercício, você adicionará uma tabela de data para dar suporte à análise de data de remessa.

### Remova as relações inativas

Nesta tarefa, você removerá a relação existente com a coluna **ShipDateKey**.

1. Alterne para a exibição de **Modelo**.

    ![](../images/dp500-work-with-model-relationships-image26.png)

2. No diagrama do modelo, clique com o botão direito na relação **ShipDateKey** e selecione **Excluir**.

    ![](../images/dp500-work-with-model-relationships-image27.png)

3. Quando a solicitação para confirmar a exclusão for exibida, selecione **OK**.

    ![](../images/dp500-work-with-model-relationships-image28.png)

    *A exclusão da relação resulta em um erro com a medida **Vendas Enviadas**. Você reescreverá a fórmula de medida mais tarde neste laboratório.*

### Desabilitar opções de relação

Nesta tarefa, você desabilitará opções de relação.

1. Na guia **Arquivo** da faixa de opções, Selecione **Opções e configurações** e, em seguida, selecione **Opções**.

    ![](../images/dp500-work-with-model-relationships-image29.png)

2. Na janela **Opções**, na parte inferior esquerda, no grupo **CURRENT FILE**, selecione **Carregamento de Dados**.

    ![](../images/dp500-work-with-model-relationships-image30.png)

3. Na seção **Relações**, desmarque as duas opções habilitadas.

    ![](../images/dp500-work-with-model-relationships-image31.png)

    *Geralmente, no seu dia a dia de trabalho, não há problema em manter essas opções ativadas. No entanto, para este laboratório, você criará relações explicitamente.*

4. Selecione **OK**.

    ![](../images/dp500-work-with-model-relationships-image32.png)

### Adicionar outra tabela Data

Nesta tarefa, você criará uma consulta para adicionar outra tabela de data ao modelo.

1. Na guia **Página Inicial** da faixa de opções, dentro do grupo **Dados**, selecione o ícone **Transformar dados**.

    ![](../images/dp500-work-with-model-relationships-image33.png) 

    *Se você for solicitado a especificar como se conectar, **Edite as credenciais** e especifique como entrar.*

    ![](../images/dp500-work-with-model-relationships-image52.png)

    *Selecione **Conectar***

     ![](../images/dp500-work-with-model-relationships-image53.png)
 
2. Na janela do **Editor do Power Query**, no painel **Consultas** (localizado à esquerda), clique com o botão direito do mouse na consulta **Data** e selecione **Referência**.

    ![](../images/dp500-work-with-model-relationships-image34.png)

    *Uma consulta de referência é aquela que usa outra consulta como origem. Portanto, essa nova consulta origina a data da consulta **Data**.*

3. No painel **Configurações de Consulta** (localizado à direita), na caixa **Nome**, substitua o texto por **Data de Remessa**.

    ![](../images/dp500-work-with-model-relationships-image35.png)

4. Para renomear a coluna **DateKey**, clique duas vezes no cabeçalho da coluna **DateKey**.

5. Substitua o texto por **ShipDateKey** e pressione **Enter**.

    ![](../images/dp500-work-with-model-relationships-image36.png)

6. Além disso, renomeie a coluna **Ano Fiscal** como **Ano de Remessa**.

    *Se possível, é uma boa ideia renomear todas as colunas para que descrevam a função que estão desempenhando. Neste laboratório, para simplificar, você renomeará apenas duas colunas.*

7. Para carregar a tabela no modelo, na guia **Página Inicial** da faixa de opções, selecione o ícone **Fechar &amp; Aplicar**.

    ![](../images/dp500-work-with-model-relationships-image37.png)

8. Quando a tabela for adicionada ao modelo, para criar uma relação, na tabela **Data de Remessa**, arraste a coluna **ShipDateKey** para a coluna **ShipDateKey** da tabela **Vendas**.

    ![](../images/dp500-work-with-model-relationships-image38.png)

9. Observe que agora existe uma relação ativa entre as tabelas **Data da Remessa** e **Vendas**.

### Visualizar dados da data de remessa

Nesta tarefa, você visualizará os dados da data de remessa em um novo visual de tabela.

1. Alterne para a exibição de **Relatório**.

    ![](../images/dp500-work-with-model-relationships-image39.png)

2. Para clonar o visual da tabela, primeiro selecione o visual.

3. Na guia **Página Inicial** da faixa de opções, dentro do grupo **Área de transferência**, selecione **Copiar**.

    ![](../images/dp500-work-with-model-relationships-image40.png)

4. Para colar o visual copiado, na guia **Página Inicial** da faixa de opções, dentro do grupo **Área de transferência**, clique em **Colar**.

    *Dica: você também pode usar os **atalhos Ctrl+C** e **Ctrl+V** .*

    ![](../images/dp500-work-with-model-relationships-image41.png)

5. Mova o novo visual da tabela para a direita do visual da tabela existente.
 
6. Selecione o novo visual da tabela e, no painel **Visualizações**, de dentro da caixa **Valores**, remova o campo **Ano do Pedido**.

    ![](../images/dp500-work-with-model-relationships-image42.png)

7. No painel **Dados**, abra e expanda a tabela **Data de Remessa**.

8. Para adicionar um novo campo ao novo visual de tabela, na tabela **Data de Remessa**, arraste o campo **Ano da Remessa** para a caixa **Valores**, acima do campo **Valor de Vendas**.

    ![](../images/dp500-work-with-model-relationships-image43.png)

9. Verifique se o novo visual da tabela mostra o valor das vendas agrupadas por ano de remessa.

    ![](../images/dp500-work-with-model-relationships-image44.png)

    *O modelo agora tem duas tabelas de data, cada uma com uma relação ativa com a  tabela **Vendas**. O benefício dessa abordagem de design é que ela é flexível. Agora é possível usar todas as medidas e campos somáveis com qualquer tabela de data.*

    *Existem, no entanto, algumas desvantagens. Cada tabela de role-playing contribuirá para um tamanho de modelo maior, embora as tabelas de dimensão normalmente não sejam grandes em termos de linhas. Cada tabela de role-playing também exigirá a duplicação de configurações do modelo, como marcação da tabela de datas, criação de hierarquias e outras configurações. Além disso, tabelas adicionais contribuem para um possível número esmagador de campos. Os usuários podem achar mais difícil encontrar os recursos do modelo de que precisam.*

    *Por último, não é possível conseguir uma combinação de filtros num único visual. Por exemplo, não é possível combinar vendas encomendadas e vendas enviadas no mesmo visual sem criar uma medida. Você criará essa medida no próximo exercício.*

## Explorar outras funções de relação

Neste exercício, você trabalhará com outras funções de relação do DAX.

### Explorar outras funções de relação

Nesta tarefa, você trabalhará com as funções CROSSFILTER e TREATAS para modificar o comportamento do relação durante os cálculos.

1. No painel **Dados**, dentro da tabela **Vendas**, selecione a medida **Vendas Enviadas**.

    ![](../images/dp500-work-with-model-relationships-image45.png)

2. Na base da fórmula, substitua o texto pela seguinte definição:

    ```
    Sales Shipped =
    CALCULATE (
    SUM ( 'Sales'[Sales Amount] ),
    CROSSFILTER ( 'Date'[DateKey], 'Sales'[OrderDateKey], NONE ),
    TREATAS (
    VALUES ( 'Date'[DateKey] ),
    'Ship Date'[ShipDateKey]
        )
    )
    ```

    *Esta fórmula usa a função CALCULATE para somar a coluna **Valor de Vendas **usando comportamentos de relação modificados. A função CROSSFILTER desabilita a relação ativa com a coluna **OrderDateKey** (esta função também pode modificar a direção do filtro). A função TREATAS cria uma relação virtual aplicando os valores **DateKey** no contexto à coluna **ShipDateKey**.*

3. Adicione a medida **Vendas Enviadas** revisada ao visual da primeira tabela.

    ![](../images/dp500-work-with-model-relationships-image46.png)

4. Examine o visual da primeira tabela.

    ![](../images/dp500-work-with-model-relationships-image47.png)

5. Observe que não há nenhum grupo BLANK.

    *Como não há BLANKs na coluna **OrderDateKey**, um grupo BLANK não foi gerado. Mostrar vendas não enviadas exigirá uma abordagem diferente.*

### Mostrar vendas não enviadas

Nesta tarefa, você criará uma medida para mostrar o valor das vendas não enviadas.

1. Crie uma medida chamada **Vendas Não Enviadas** usando a seguinte definição:

    ```
    Sales Unshipped =
    CALCULATE (
    SUM ( 'Sales'[Sales Amount] ),
    ISBLANK ( 'Sales'[ShipDateKey] )
    )
    ```
    *Esta fórmula soma a coluna **Valor de Vendas** em que a Coluna **ShipDateKey** é BLANK.*

2. Formate a medida para usar duas casas decimais.

3. Para adicionar um novo visual à página, primeiro selecione uma área em branco da página do relatório.

4. No painel de **Visualizações**, selecione o ícone de visual **Cartão**.

    ![](../images/dp500-work-with-model-relationships-image48.png)

5. Arraste a medida **Vendas não Enviadas** ao visual do cartão.

    ![](../images/dp500-work-with-model-relationships-image49.png)

6. Verifique se o layout da página do relatório final é semelhante ao seguinte.

    ![](../images/dp500-work-with-model-relationships-image50.png)

### Conclusão

Nesta tarefa, você vai concluir.

1. Salve o arquivo do Power BI Desktop.

    ![](../images/dp500-work-with-model-relationships-image51.png)

2. Feche o Power BI Desktop.