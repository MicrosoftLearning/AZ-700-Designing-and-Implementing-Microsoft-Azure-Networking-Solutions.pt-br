---
Exercise:
  title: M05 - Unidade 4 Implantar um Gateway de Aplicativo do Azure
  module: Module 05 - Load balancing HTTP(S) traffic in Azure
---

# M05-Unidade 4 Implantar um Gateway de Aplicativo do Azure

## Cenário do exercício

Neste exercício, você usará o portal do Azure para criar um gateway de aplicativo. Em seguida, você o testará para verificar se ele funciona corretamente.

![Diagrama da arquitetura do gateway de aplicativo.](../media/4-exercise-deploy-azure-application-gateway.png)


>**Observação:** uma **[simulação de laboratório interativa](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Deploy%20Azure%20Application%20Gateway)** está disponível e permite que você clique neste laboratório no seu próprio ritmo. Você pode encontrar pequenas diferenças entre a simulação interativa e o laboratório hospedado, mas os principais conceitos e ideias que estão sendo demonstrados são os mesmos.

### Tempo estimado: 25 minutos

O gateway de aplicativo direciona o tráfego da Web do aplicativo para recursos específicos em um pool de back-end. Você atribuirá ouvintes a portas, criará regras e adicionará recursos a um pool de back-end. Para simplificar, este artigo usa uma configuração simples com um IP de front-end público, um ouvinte básico para hospedar um só site no gateway de aplicativo, uma regra de roteamento de solicitação básica e duas máquinas virtuais usadas no pool de back-end.

Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual. Você pode criar uma nova rede virtual ou usar uma existente. Neste exemplo, você criará uma nova rede virtual enquanto cria o gateway de aplicativo. As instâncias do Gateway de Aplicativo são criadas em sub-redes separadas. Crie duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end.

Neste exercício, você vai:

+ Tarefa 1: criar um gateway de aplicativo
+ Tarefa 2: criar máquinas virtuais
+ Tarefa 3: adicionar servidores de back-end ao pool de back-end
+ Tarefa 4: testar o gateway de aplicativo

## Tarefa 1: criar um gateway de aplicativo

1. Entre no [portal do Azure](https://portal.azure.com/) com sua conta do Azure.

1. Em qualquer página do Portal do Azure, em **Pesquisar recursos, serviços e documentos (G+/)**, insira o gateway de aplicativo e selecione **Gateways de aplicativo** nos resultados.
    ![Pesquisar Gateway de Aplicativo no Portal do Azure](../media/search-application-gateway.png)

1. Na página Gateways de aplicativo, selecione **+ Criar**.

1. Na guia **Noções básicas** para criar um gateway de aplicativo, insira ou selecione as seguintes informações:

   | **Configuração**         | **Valor**                                    |
   | ------------------- | -------------------------------------------- |
   | Subscription        | Selecione sua assinatura.                    |
   | Resource group      | Selecione Criar ContosoResourceGroup       |
   | Gateway de Aplicativo | ContosoAppGateway                            |
   | Região              | Selecione **Leste dos EUA**                           |
   | Rede Virtual     | Selecione **Criar**                        |

1. Em Criar rede virtual, insira ou selecione as seguintes informações:

   | **Configuração**       | **Valor**                          |
   | ----------------- | ---------------------------------- |
   | Nome              | ContosoVNet                        |
   | **ESPAÇO DE ENDEREÇO** |                                    |
   | Intervalo de endereços     | 10.0.0.0/16                        |
   | **SUB-REDES**       |                                    |
   | Nome da sub-rede       | Altere o **padrão** para **AGSubnet** |
   | Intervalo de endereços     | 10.0.0.0/24                        |
   | Nome da sub-rede       | BackendSubnet                      |
   | Intervalo de endereços     | 10.0.1.0/24                        |


>**Observação**: se a interface do usuário não tiver a opção de adicionar sub-redes adicionais, conclua as etapas e adicione a sub-rede de back-end depois de criar o gateway. 

1. Selecione **OK** para retornar à guia Noções básicas para criar um gateway de aplicativo.

1. Aceite os valores padrão para as outras configurações e selecione **Avançar: Front-ends**.

1. Na guia **Front-ends**, verifique se **Tipo de endereço IP do front-end** está definido como **Público**.

1. Selecione **Adicionar novo** para o **Endereço IP público**, insira AGPublicIPAddress no nome do endereço IP público, depois selecione **OK**.

1. Selecione **Avançar: Back-ends**.

1. Na guia **Back-ends**, selecione **Adicionar um pool de back-end**.

1. Na janela **Adicionar um pool de back-end** que é aberta, insira os seguintes valores para criar um pool de back-end vazio:

    | **Configuração**                      | **Valor**   |
    | -------------------------------- | ----------- |
    | Nome                             | BackendPool |
    | Adicionar pool de back-end sem destinos | Sim         |

1. Na janela **Adicionar um pool de back-end**, selecione **Adicionar** para salvar a configuração do pool de back-end e retornar à guia **Back-ends**.

1. Na guia **Back-ends**, selecione **Avançar: Configuração**.

1. Na guia **Configuração**, você conectará o front-end e o pool de back-end que você criou usando uma regra de roteamento.

1. Na coluna **Regras de roteamento**, selecione **Adicionar uma regra de roteamento**.

1. Na caixa **Nome da regra**, digite **RoutingRule**.

1. Na guia **Ouvinte**, insira ou selecione as informações a seguir:

    | **Configuração**   | **Valor**         |
    | ------------- | ----------------- |
    | Nome do ouvinte | Ouvinte          |
    | Prioridade      | **100**           |
    | IP de front-end   | Selecione **Público** |

1. Aceite os valores padrão para as outras configurações na guia **Ouvinte**.

    ![Adicionar uma regra de roteamento do Gateway de Aplicativo no Portal do Azure](../media/Routing-rule-listener-tab.png)

1. Selecione a guia **Destinos de back-end** para configurar o restante da regra de roteamento.

1. Na guia **Destinos de back-end**, insira ou selecione as informações a seguir:

    | **Configuração**      | **Valor**      |
    | -------------    | -------------- |
    | Tipo de destino      | Pool de back-end   |
    | Configurações de back-end | **Adicionar nova** |

1. Em **Adicionar uma configuração de back-end**, insira ou selecione as informações a seguir:

    | **Configuração**          | **Valor**   |
    | ------------------   | ----------- |
    | Nome da configuração de back-end | HTTPSetting |
    | Porta de back-end         | 80          |

1. Aceite os valores padrão para as outras configurações na janela **Adicionar uma configuração de back-end**, depois selecione **Adicionar** para retornar para **Adicionar uma regra de roteamento**.

1. Selecione **Adicionar** para salvar a regra de roteamento e voltar para a guia **Configuração**.

1. Selecione **Avançar: Marcas** e, em seguida, **Avançar: Revisar + criar**.

1. Analise as configurações na guia **Revisar + criar**

1. Selecione **Criar** para criar a rede virtual, o endereço IP público e o gateway de aplicativo.

Pode levar vários minutos para que o Azure crie o gateway de aplicativo. Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

## Tarefa 2: criar máquinas virtuais

1. No portal do Azure, abra a sessão **PowerShell** no painel do **Cloud Shell**.
 > **Observação:** se esta for a primeira vez que o Cloud Shell é aberto, você será solicitado a criar uma conta de armazenamento. Selecione **Criar armazenamento**.
1. Na barra de ferramentas do painel do Cloud Shell, selecione o ícone **Carregar/baixar arquivos**, no menu suspenso, selecione **Carregar** e carregue os seguintes arquivos **backend.json** e **backend.parameters.json**, um a um, da pasta de origem **F:\Allfiles\Exercises\M05** para o diretório base do Cloud Shell.

1. Implante os seguintes modelos do ARM para criar as VMs necessárias para este exercício:

>**Observação**: você será solicitado a fornecer uma senha de Administrador.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile backend.json -TemplateParameterFile backend.parameters.json
   ```
  
1. Após a conclusão da implantação, acesse a home page do portal do Azure e, depois, escolha **Máquinas Virtuais**.

1. Verifique se as duas máquinas virtuais foram criadas.

## Tarefa 3: adicionar servidores de back-end ao pool de back-end

1. No menu portal do Azure, selecione **Todos os recursos** ou pesquise e selecione Todos os recursos. Depois, selecione **ContosoAppGateway**.

1. Em **Configurações**, selecione **Pools de back-end**.

1. Selecione **BackendPool**.

1. Na página Editar pool de back-end, em **Destinos de back-end**, em **Tipo de destino**, selecione **Máquina virtual**.

1. Em **Destino**, selecione **BackendVM1.**

1. Em **Tipo de destino**, selecione **Máquina virtual**.

1. Em **Destino**, selecione **BackendVM2.**

   ![Adicionar back-ends de destino ao pool de back-end do Portal do Azure](../media/edit-backend-pool.png)

1. Selecione **Salvar**.

Aguarde a conclusão da implantação antes de prosseguir para a próxima etapa.

## Tarefa 4: testar o gateway de aplicativo

Embora o IIS não seja exigido para criar o gateway de aplicativo, você o instalou neste exercício para verificar se o Azure criou o gateway de aplicativo com êxito.

### Usar o IIS para testar o gateway de aplicativo

1. Localize o endereço IP público para o gateway de aplicativo na página **Visão geral**.

   ![Pesquisar endereço IP público de front-end no Portal do Azure ](../media/app-gw-public-ip.png)

1. Copie o endereço IP público e cole-o na barra de endereços do seu navegador para procurar esse endereço IP.

1. Verifique a resposta. Uma resposta válida verifica se o gateway de aplicativo foi criado com êxito e é capaz de conectar-se com êxito ao back-end.

   ![Navegador – exibir BackendVM1 ou BackendVM2, dependendo de qual servidor e back-end responde à solicitação.](../media/browse-to-backend.png)

1. Ao atualizar o navegador várias vezes, você deverá ver conexões a ambos BackendVM1 e BackendVM2.

Parabéns! Você configurou e testou um Gateway de Aplicativo do Azure.
