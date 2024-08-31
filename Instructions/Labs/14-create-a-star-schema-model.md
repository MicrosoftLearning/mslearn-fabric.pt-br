---
lab:
  title: "Criar e\_explorar um modelo semântico"
  module: Understand scalability in Power BI
---

# Criar e explorar um modelo semântico

Neste exercício, você usará o Microsoft Fabric para desenvolver um modelo de dados para os dados de exemplo de táxi de NYC em um data warehouse.

Você praticará:

- Criação de um modelo semântico personalizado a partir de um data warehouse do Fabric.
- Criar relacionamentos e organizar o diagrama do modelo.
- Explore os dados em seu modelo semântico diretamente no Fabric.

Este laboratório leva cerca de **30** minutos para ser concluído.

> **Observação**: Você precisa de uma [avaliação do Microsoft Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para concluir esse exercício.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Na [página inicial do Microsoft Fabric](https://app.fabric.microsoft.com/home?experience=fabric) no `https://app.fabric.microsoft.com/home?experience=fabric`, selecione **Engenharia de Dados do Synapse**.
1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

## Criar um data warehouse e carregar dados de amostra

Agora que você tem um espaço de trabalho, é hora de criar um data warehouse. A página inicial do Data Warehouse do Synapse inclui um atalho para criar um novo warehouse:

1. Na página inicial do **Data Warehouse do Synapse**, crie um novo **Warehouse** com um nome de sua escolha.

    Após alguns minutos, um warehouse será criado:
    
    ![Captura de tela de um novo warehouse](./Images/new-data-warehouse2.png)

1. No centro da interface do usuário do data warehouse, você verá algumas maneiras diferentes de carregar dados no seu warehouse. Selecione **Dados de amostra** para carregar os dados do NYC Taxi em seu data warehouse. Isso levará alguns minutos.

1. Depois que os dados de amostra forem carregados, use o painel **Explorer** à esquerda para ver quais tabelas e exibições já existem no data warehouse de amostra.

1. Selecione a guia **Relatórios** da faixa de opções e escolha **Novo modelo semântico**. Isso permite que você crie um novo modelo semântico utilizando apenas tabelas e exibições específicas do seu data warehouse, para uso das equipes de dados e da empresa na criação de relatórios.

1. Nomeie o modelo semântico como **Receita de Táxi**, certifique-se de que ele esteja no espaço de trabalho que você acabou de criar e selecione as seguintes tabelas:
   - Data
   - Viagem
   - Geografia
   - Clima
     
   ![Captura de tela da interface do novo modelo semântico com quatro tabelas selecionadas](./Images/new-semantic-model.png)
     
## Criar relações entre tabelas

Agora você criará relacionamentos entre as tabelas para analisar e visualizar seus dados com precisão. Se você estiver familiarizado com a criação de relacionamentos no Power BI Desktop, isso lhe parecerá familiar.

1. Navegue de volta ao seu espaço de trabalho e confirme que você vê seu novo modelo semântico, Receita de Táxi. Observe que o tipo de item é **Modelo semântico**, ao contrário do **Modelo semântico (padrão)** que é criado automaticamente quando você cria um data warehouse.

     *Observação: Um modelo semântico padrão é criado automaticamente quando você cria um ponto de extremidade de análise do Warehouse ou do SQL no Microsoft Fabric, e ele herda a logica de negócios do Lakehouse ou do Warehouse pai. Um modelo semântico criado por você mesmo, como fizemos aqui, é um modelo personalizado que você pode projetar e modificar de acordo com suas necessidades e preferências específicas. Você pode criar um modelo semântico personalizado utilizando o Power BI Desktop, o serviço do Power BI ou outras ferramentas que se conectam ao Microsoft Fabric.*

1. Selecione **Abrir modelo de dados na faixa de opções**.

    Agora, você criará relacionamentos entre as tabelas. Se você estiver familiarizado com a criação de relacionamentos no Power BI Desktop, isso lhe parecerá familiar!

    *Analisando o conceito de esquemas em estrela, organizaremos as tabelas em nosso modelo em uma Tabela de Fatos e Tabela de Dimensões. Nesse modelo, a tabela **Viagem** é nossa tabela de fatos, e nossas dimensões são **Data**, **Geografia** e **Clima**.*

1. Crie uma relação entre a tabela **Data** e a tabela **Viagem** utilizando a coluna **DateID**.

    **Selecione a coluna DateID** na tabela **Data** e *arraste e solte-a acima da coluna DateID na tabela Viagem*.

    Certifique-se de que o relacionamento seja um relacionamento **Um para muitos** da tabela **Data** para a tabela **Viagem**.

1. Crie mais dois relacionamentos com a tabela de fatos **Trip** da seguinte forma:

   - **Geography [GeographyID]** para **Trip [DropoffGeographyID]** (1:Muitos)
   - **Weather [GeographyID]** para **Trip [DropoffGeographyID]** (1:Muitos)

    > **Observação**: você precisa alterar a cardinalidade padrão do relacionamento para **1:Muitos** para ambos os relacionamentos.

1. Arraste as tabelas para a posição de modo que a tabela de fatos **Viagem** fique na parte inferior do diagrama e as tabelas restantes, que são tabelas de dimensões, fiquem ao redor da tabela de fatos.

    ![Captura de tela do diagrama de esquema em estrela](./Images/star-schema-diagram.png)

    *A criação do modelo de esquema em estrela está concluída. Há muitas configurações de modelagem que agora podem ser aplicadas, como adicionar hierarquias, cálculos e definir propriedades como visibilidade de coluna.*

    > **Dica**: No painel Propriedades da janela, alterne *Fixar os campos relacionados à parte superior do cartão* para Sim. Isso ajudará você (e a outras pessoas que fazem relatórios com base nesse modelo) a ver rapidamente quais campos estão sendo usados nos relacionamentos. Você também pode interagir com os campos em suas tabelas utilizando o painel de propriedades. Por exemplo, se quiser confirmar se os tipos de dados estão definidos corretamente, você pode selecionar um campo e revisar o formato no painel de propriedades.

     ![Captura de tela do painel de propriedades](./Images/properties-pane.png)

## Explorar seus dados

Agora você tem um modelo semântico criado a partir do seu warehouse que tem relações estabelecidas que são necessárias para a geração de relatórios. Vamos dar uma olhada nos dados utilizando o recurso **Explorar Dados**.

1. Navegue de volta ao seu espaço de trabalho e selecione o modelo semântico **Receita de Táxi**.

1. Na janela, selecione **Explorar esses dados** na faixa de opções. Aqui você verá seus dados em formato tabular. Isso oferece uma experiência focada para explorar seus dados sem criar um relatório completo do Power BI.

1. Adicione **YearName** e **MonthName** às linhas e explore bem a **número médio de passageiros**, **valor médio da viagem** e **duração média da viagem** no campo de valores.

    *Quando você arrastar e soltar um campo numérico no painel Explorar, o padrão será resumir o número. Para alterar a agregação de **Resumir** para **Média**, selecione o campo e altere a agregação na janela pop-up.*

    ![Captura de tela da janela de exploração de dados, com um visual de matriz que mostra as médias ao longo do tempo.](./Images/explore-data-fabric.png)

1. Para ver esses dados como um visual em vez de apenas uma matriz, selecione **Visual** na parte inferior da janela. Selecione um gráfico de barras para visualizar rapidamente esses dados.

   *Um gráfico de barras não é a melhor maneira de analisar esses dados. Brinque com os diferentes visuais e com os campos que está vendo na seção "Reorganizar dados" do painel Dados, no lado direito da tela.*

1. Agora você pode salvar essa exibição de Exploração no seu espaço de trabalho clicando no botão **Salvar** no canto superior esquerdo. Você também pode **Compartilhar** a exibição selecionando **Compartilhar** no canto superior direito. Isso permitirá que você compartilhe a exploração de dados com colegas.

1. Depois de salvar a exploração, navegue de volta ao espaço de trabalho para ver o data warehouse, o modelo semântico padrão, o modelo semântico que você criou e a exploração.

    ![Captura de tela de um espaço de trabalho no Fabric exibindo um data warehouse, um modelo semântico padrão, um modelo semântico e uma exploração de dados.](./Images/semantic-model-workspace.png)
