---
lab:
  title: Gerar e salvar previsões em lote
  module: Generate batch predictions using a deployed model in Microsoft Fabric
---

# Gerar e salvar previsões em lote

Neste laboratório, você usará um modelo de machine learning para prever uma medida quantitativa do diabetes. Você usará a função PREDICT no Fabric para gerar as previsões com um modelo registrado.

Ao concluir esse laboratório, você ganhará experiência prática na geração de previsões e na visualização dos resultados.

Este laboratório levará aproximadamente **45** minutos para ser concluído.

> **Observação**: você precisará ter uma licença do Microsoft Fabric para concluir este exercício. Confira [Introdução ao Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para obter detalhes de como habilitar uma licença de avaliação gratuita do Fabric. Você precisará ter uma conta *corporativa* ou de *estudante* da Microsoft para fazer isso. Caso não tenha uma, [inscreva-se em uma avaliação do Microsoft Office 365 E3 ou superior](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Criar um workspace

Antes de trabalhar com os modelos no Fabric, crie um workspace com a versão de avaliação do Fabric habilitada.

1. Entre no [Microsoft Fabric](https://app.fabric.microsoft.com) em `https://app.fabric.microsoft.com` e selecione **Power BI**.
2. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
3. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
4. Quando o novo workspace for aberto, ele deverá estar vazio, conforme mostrado aqui:

    ![Captura de tela de um workspace vazio no Power BI.](./Images/new-workspace.png)

## Carregar o bloco de anotações

Para ingerir dados, treinar e registrar um modelo, você executará as células em um notebook. Você pode carregar o notebook no workspace.

1. No canto inferior esquerdo do portal do Fabric, selecione o ícone **Engenharia de dados** e alterne para a experiência de **Ciência de dados**.
1. Na home page **Ciência de dados**, selecione **Importar notebook**.

    Você receberá uma notificação quando o notebook for importado com sucesso.

1. Navegue até o notebook importado chamado `Generate-Predictions`.

1. Leia as instruções no notebook com cuidado e execute cada célula individualmente.

## Limpar recursos

Neste exercício, você usou um modelo para gerar previsões em lote.

Caso tenha terminado de explorar o notebook, exclua o workspace que você criou para esse exercício.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Outros**, selecione **Remover este workspace**.
