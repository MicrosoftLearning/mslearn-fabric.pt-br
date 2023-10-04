---
lab:
  title: Usar o Data Activator no Fabric
  module: Get started with Data Activator in Microsoft Fabric
---

# Usar o Data Activator no Fabric

O Data Activator no Microsoft Fabric executa uma ação com base no que está acontecendo em seus dados. O Data Activator permite monitorar seus dados e criar gatilhos para reagir às alterações de dados.

Este laboratório leva cerca de **30** minutos para ser concluído.

> **Observação**: você precisará ter uma licença do Microsoft Fabric para concluir este exercício. Confira [Introdução ao Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para obter detalhes de como habilitar uma licença de avaliação gratuita do Fabric. Você precisará ter uma conta *corporativa* ou de *estudante* da Microsoft para fazer isso. Caso não tenha uma, [inscreva-se em uma avaliação do Microsoft Office 365 E3 ou superior](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Entre no [Microsoft Fabric](https://app.fabric.microsoft.com) em `https://app.fabric.microsoft.com` e selecione **Power BI**.
2. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
3. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
4. Quando o novo workspace for aberto, ele deverá estar vazio, conforme mostrado aqui:

    ![Captura de tela de um workspace vazio no Power BI.](./Images/new-workspace.png)

Neste laboratório, você usa o Data Activator no Fabric para criar um *Reflex*. O Data Activator fornece convenientemente um conjunto de dados de exemplo que você pode usar para explorar os recursos do Data Activator. Você usa esses dados de exemplo para criar um *Reflex* que analisa alguns dados em tempo real e cria um gatilho para enviar um email quando uma condição é atendida.

> **Observação**: o processo de exemplo do Data Activator está gerando alguns dados aleatórios em segundo plano.  Quanto mais complexas forem suas condições e filtros criados, maior a probabilidade de que nenhum evento ainda atenda às condições e filtros do gatilho. Se você não vir nenhum dado no grafo, aguarde alguns minutos e atualize a página. Dito isso, você não precisa esperar que os dados sejam exibidos nos grafos para continuar com o laboratório.

## Cenário

Nesse cenário, você é analista de dados de uma empresa que vende e envia uma variedade de produtos.  Você é responsável pelos dados de todas as remessas e vendas para a cidade de Redmond. Você deseja criar um Reflex que monitore os pacotes que saíram para entrega. Uma categoria de produtos que você envia são prescrições médicas que precisam ser refrigeradas a uma temperatura específica durante o trânsito. Você deseja criar um Reflex que envie um email para o departamento de remessa se a temperatura de um pacote que contém uma prescrição for maior ou menor do que um determinado limite. A temperatura ideal deve estar entre 33 graus e 41 graus. Como os eventos Reflex já contêm um gatilho semelhante, você cria um especificamente para os pacotes enviados para a cidade de Redmond. Vamos começar!

## Criar um Reflex

1. No portal de experiência do **Microsoft Fabric**, selecione a experiência do **Data Activator** selecionando primeiro o ícone de experiência atual do Fabric no canto inferior esquerdo da tela e, em seguida, selecionando o **Data Activator** no menu. Por exemplo, na captura de tela a seguir, a experiência atual do Fabric é o **Power BI**.

    ![Captura de tela da seleção da Experiência do Data Activator.](./Images/data-activator-select-experience.png)

1. Agora você deve estar na tela Inicial do Data Activator. O ícone Experiência do Fabric no canto inferior direito também foi alterado para o Data Activator. Vamos criar um novo Reflex selecionando o botão **Reflex (versão prévia).**

    ![Captura de tela da tela Inicial do Data Activator.](./Images/data-activator-home-screen.png)

1. Em um ambiente de produção real, você usaria seus próprios dados. No entanto, para este laboratório, você usa os dados de exemplo fornecidos pelo Data Activator. Selecione o botão **Usar dados de exemplo** para concluir a criação do Reflex.

    ![Captura de tela da tela de obtenção de dados do Data Activator.](./Images/data-activator-get-started.png)

1. Por padrão, o Data Activator cria seu Reflex com o nome *Reflex AAAA-MM-DD hh:mm:ss*. Como você pode ter vários Reflex em seu workspace, altere o nome do Reflex padrão para um nome mais descritivo. Selecione o botão de menu suspenso ao lado do nome do reflexo atual no canto superior esquerdo e altere o nome para ***Contoso Shipping Reflex*** para o nosso exemplo.

    ![Captura de tela da tela inicial do Reflex do Data Activator.](./Images/data-activator-reflex-home-screen.png)

Nosso Reflex agora está criado e podemos começar a adicionar gatilhos e ações a ele.

## Familiarizar-se-se com a tela inicial do Reflex

A tela inicial do Reflex é dividida em duas seções, o modo *Design*e o modo *Dados*. Você pode selecionar o modo selecionando a respectiva guia na parte inferior esquerda da tela.  A guia do modo *Design* é onde você define seus objetos com seus gatilhos, propriedades e eventos. A guia do modo *Dados* é onde você pode adicionar suas fontes de dados e exibir os dados que o Reflex processa. Vamos dar uma olhada na guia do modo *Design*, que deve ser aberta por padrão quando você cria o Reflex.

### modo Design

Se você não estiver no modo *Design* no momento, selecione a guia **Design** na parte inferior esquerda da tela.

![Captura de tela do modo Design do Reflex do Data Activator.](./Images/data-activator-design-tab.png)

Para se familiarizar com o modo *Design*, selecione as diferentes seções da tela, gatilhos, propriedades e eventos. Abordamos cada seção mais detalhadamente nas seções a seguir.

### Modo de dados

1. Se você não estiver no modo *Dados* no momento, selecione a guia **Dados** na parte inferior esquerda da tela. Em um exemplo do mundo real, você adicionaria suas próprias fontes de dados de seus visuais do EventStreams e Power BI aqui. Para este laboratório, você usa os dados de exemplo fornecidos pelo Data Activator. Os dados de exemplo fornecidos pelo Data Activator já estão configurados com três EventStreams que estão monitorando o status de entrega do pacote.

![Captura de tela do modo Dados do Reflex do Data Activator.](./Images/data-activator-data-tab.png)

1. Selecione cada um dos diferentes eventos para ver os dados que o evento processa.

![Captura de tela dos eventos do modo Dados do Reflex do Data Activator.](./Images/data-activator-get-data-tab-event-2.png)

É hora de adicionar um gatilho ao nosso Reflex, mas primeiro, vamos criar um novo objeto.

## Criar um objeto

Em um cenário do mundo real, talvez não seja necessário criar um novo objeto para esse Reflex, pois o exemplo do Data Activator já inclui um objeto chamado *Package*. Mas para este laboratório, criamos um novo objeto para demonstrar como criar um. Vamos criar um novo objeto chamado *Pacotes para Redmond*.

1. Se você não estiver no modo *Dados* no momento, selecione a guia **Dados** na parte inferior esquerda da tela.

1. Selecione o evento ***Pacote em trânsito***. Preste muita atenção aos valores nas colunas *PackageId*, *Temperatura*, *ColdChainType*, *Cidade* e *SpecialCare*. Use estas colunas para criar o gatilho.

1. Se a caixa de diálogo *Atribuir seus dados* ainda não estiver aberta no lado direito, selecione o botão **Atribuir seus dados** à direita da tela.

    ![Captura de tela do botão para seus dados do modo Dados do Reflex do Data Activator.](./Images/data-activator-data-tab-assign-data-button.png)

1. Na caixa de diálogo *Atribuir seus dados*, selecione a guia ***Atribuir a um novo objeto*** e insira os seguintes valores:

    - **Nome do objeto**: *Pacotes para Redmond*
    - **Atribuir coluna de chave**: *PackageId*
    - **Atribuir propriedades**: *City, ColdChainType, SpecialCare, Temperature*

    ![Captura de tela da caixa de diálogo para atribuir seus dados do modo Dados do Reflex do Data Activator.](./Images/data-activator-data-tab-assign-data.png)

1. Selecione **Salvar** e, em seguida, selecione **Salvar e ir para o modo de design**.

1. Agora você deve estar de volta ao modo *Design*. Um novo objeto chamado ***Pacotes para Redmond*** foi adicionado. Selecione esse novo objeto, expanda seus *Eventos* e selecione o evento **Pacote em trânsito**.

    ![Captura de tela do modo Design do Reflex do Data Activator com um novo objeto.](./Images/data-activator-design-tab-new-object.png)

Hora de criar o gatilho.

## Escolha um gatilho

Vamos examinar o que você deseja que o gatilho faça: *você deseja criar um Reflex que envie um email para o departamento de remessa se a temperatura de um pacote que contém uma prescrição for maior ou menor do que um determinado limite. A temperatura ideal deve estar entre 33 graus e 41 graus. Como os eventos Reflex já contêm um gatilho semelhante, você cria um especificamente para os pacotes enviados para a cidade de Redmond*.

1. Selecione o botão **Novo gatilho** no menu superior. Um novo gatilho é criado com o nome padrão *Sem título*, altere o nome para ***Temperatura dos medicamentos fora do intervalo*** para definir melhor o gatilho.

    ![Captura de tela da criação de novo gatilho do Design do Reflex do Data Activator.](./Images/data-activator-trigger-new.png)

1. Hora de selecionar a propriedade ou coluna de evento que dispara o Reflex. Como você criou várias propriedades quando criou o objeto, selecione o botão **Propriedade existente** e selecione a propriedade ***Temperatura***. A seleção dessa propriedade deve retornar um grafo com um exemplo de valores de histórico de temperatura.

    ![Captura de tela da seleção de uma propriedade do Design do Reflex do Data Activator.](./Images/data-activator-trigger-select-property.png)

    ![Captura de tela do grafo de propriedades de valores históricos do Data Activator.](./Images/data-activator-trigger-property-sample-graph.png)

1. Agora você precisa decidir que tipo de condição você deseja disparar dessa propriedade. Nesse caso, você deseja disparar seu Reflexo quando a temperatura estiver acima de 41 ou abaixo de 33 graus. Como estamos procurando um intervalo numérico, selecione o botão **Numérico** e selecione a condição **Fora do intervalo**.

    ![Captura de tela da escolha do tipo de condição do Design do Reflex do Data Activator.](./Images/data-activator-trigger-select-condition-type.png)

1. Agora você precisa inserir os valores para sua condição. Insira ***33*** e ***44*** como os valores de intervalo. Como você escolheu a condição *fora do intervalo numérico*, o gatilho deve ser acionado quando a temperatura estiver abaixo de *33* ou acima de *44* graus.

    ![Captura de tela da inserção de valores de condição do Design do Reflex do Data Activator.](./Images/data-activator-trigger-select-condition-define.png)

1. Até agora, você define a propriedade e a condição em que deseja que o gatilho seja acionado, mas isso ainda não inclui todos os parâmetros necessários. Você ainda precisa ter certeza de que o gatilho só dispara para a *cidade* de **Redmond** e para o tipo *cuidados especiais* de **Medicamentos**. Vamos em frente e vamos adicionar alguns filtros para essas condições.  Selecione o botão **Adicionar filtro** e selecione a propriedade ***Cidade***. Insira ***Redmond*** como o valor. Em seguida, selecione o botão **Adicionar filtro** novamente e selecione a propriedade ***SpecialCare***. Insira ***Medicamentos*** como o valor.

    ![Captura de tela da adição de filtro do Design do Reflex do Data Activator.](./Images/data-activator-trigger-select-condition-add-filter.png)

1. Vamos adicionar mais um filtro só para garantir que o medicamento seja refrigerado. Selecione o botão **Adicionar filtro** e selecione a propriedade ***ColdChainType***. Insira ***Refrigerado*** como o valor.

    ![Captura de tela da adição de filtro do Design do Reflex do Data Activator.](./Images/data-activator-trigger-select-condition-add-filter-additional.png)

1. Você está quase lá! Você só precisa definir qual ação deseja executar quando o gatilho é acionado. Nesse caso, você deseja enviar um email para o departamento de remessa. Selecione o botão **Email**.

    ![Captura de tela da ação de adição do Data Activator.](./Images/data-activator-trigger-select-action.png)

1. Insira os seguintes valores para sua ação de email:

    - **Enviar para**: sua conta de usuário atual deve ser selecionada por padrão, o que deve ser bom para este laboratório.
    - **Assunto**: *Pacote de medicamentos para Redmond fora do intervalo de temperatura aceitável*
    - **Título**: *Temperatura muito quente ou muito fria*
    - **Informações adicionais**: selecione a propriedade *Temperatura* na lista de caixas de seleção.

    ![Captura de tela de início de gatilho do Design do Reflex do Data Activator.](./Images/data-activator-trigger-start.png)

1. Selecione **Salvar** e **Iniciar** no menu superior.

Agora você criou e iniciou um gatilho no Data Activator.

## Atualizar um gatilho

O único problema com esse gatilho é que, embora o gatilho tenha enviado um email com a temperatura, o gatilho não enviou o *PackageId* do pacote. Vamos atualizar o gatilho para incluir o *PackageId*.

1. Selecione o evento **Pacotes em trânsito** no objeto **Pacotes para Redmond** e selecione **Nova propriedade** no menu superior.

    ![Captura de tela do evento de seleção do Data Activator do objeto.](./Images/data-activator-trigger-select-event.png)

1. Vamos adicionar a propriedade **PackageId** selecionando a coluna no evento *Pacotes em trânsito*. Não se esqueça de alterar o nome da propriedade de *Sem título* para *PackageId*.

    ![Captura de tela da propriedade de criação do Data Activator.](./Images/data-activator-trigger-create-new-property.png)

1. Vamos atualizar nossa ação de gatilho. Selecione o gatilho **Temperatura dos medicamentos fora do intervalo**, role até a seção **Ato** na parte inferior, selecione **Informações adicionais** e adicione a propriedade **PackageId**. NÃO selecione o botão **Salvar** ainda.

    ![Captura de tela de adicionar propriedade ao gatilho do Data Activator.](./Images/data-activator-trigger-add-property-existing-trigger.png)

1. Como você atualizou o gatilho, a ação correta deve ser atualizar e não salvar o gatilho, mas para este laboratório fazemos o oposto e selecionamos o botão **Salvar** em vez do botão **Atualizar** para ver também o que acontece. O motivo pelo qual você deve ter selecionado o botão *Atualizar* é porque quando você seleciona a opção de *atualizar* o gatilho, ele salva o gatilho e atualiza o gatilho em execução com as novas condições. Se você selecionar apenas o botão *Salvar*, o gatilho em execução não usará as novas condições até que você selecione atualizar o gatilho. Vamos em frente e vamos selecionar o botão **Salvar**.

1. Como você selecionou *Salvar* em vez de *Atualizar*, você deve ter notado que a mensagem *Há uma atualização de propriedade disponível. Atualize agora para garantir que o gatilho tenha as alterações mais recentes exibidas* na parte superior da tela. Além disso, a mensagem tem o botão *Atualizar*. Vamos em frente e vamos selecionar o botão **Atualizar**.

    ![Captura de tela da atualização de gatilho do Data Activator.](./Images/data-activator-trigger-updated.png)

O gatilho agora está atualizado.

## Interromper um gatilho

Para interromper o gatilho, selecione o botão **Parar** no menu superior.

## Limpar os recursos

Neste exercício, você criou um Reflex com um gatilho no Data Activator. Agora você deve estar familiarizado com a interface do Data Activator e como criar um Reflex e seus objetos, gatilho e propriedades.

Se você tiver terminado de explorar seu Data Activator Reflex, exclua o workspace criado para este exercício.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Outros**, selecione **Remover este workspace**.
