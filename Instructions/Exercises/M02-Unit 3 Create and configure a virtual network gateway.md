---
Exercise:
  title: M02 - Unidade 3 Criar e configurar um gateway de rede virtual
  module: Module 02 - Design and implement hybrid networking
---


# M02-Unidade 3 Criar e configurar um gateway de rede virtual

## Cenário do exercício

Neste exercício, você vai configurar um gateway de rede virtual para conectar a VNet do Contoso Core Services e a VNet de Fabricação.

![Diagrama de gateway de rede virtual.](../media/3-exercise-create-configure-local-network-gateway.png)

### Habilidades de trabalho

Neste exercício, você vai:

+ Tarefa 1: criar CoreServicesVnet e ManufacturingVnet
+ Tarefa 2: criar CoreServicesVM
+ Tarefa 3: criar ManufacturingVM
+ Tarefa 4: conectar-se às VMs usando RDP
+ Tarefa 5: testar a conexão entre as VMs
+ Tarefa 6: criar o gateway da CoreServicesVnet
+ Tarefa 7: criar o gateway da ManufacturingVnet
+ Tarefa 8: conectar a CoreServicesVnet à ManufacturingVnet
+ Tarefa 9: conectar ManufacturingVnet à CoreServicesVnet
+ Tarefa 10: verificar se as conexões se conectam
+ Tarefa 11: testar a conexão entre as VMs

### Simulações interativas de laboratório

>**Observação**: as simulações de laboratório fornecidas anteriormente foram desativadas.

### Tempo estimado: 70 minutos (incluindo ~45 minutos de tempo de espera de implantação)

## Tarefa 1: criar CoreServicesVnet e ManufacturingVnet

1. No portal do Azure, clique no ícone do Cloud Shell (canto superior direito). Se necessário, configure o shell.  
    + Selecione **PowerShell**.
    + Selecione **Nenhuma Conta de Armazenamento necessária** e sua **Assinatura** e, em seguida, selecione **Aplicar**.
    + Aguarde até que o terminal seja criado e um prompt seja exibido. 

1. Na barra de ferramentas do painel do Cloud Shell, selecione o ícone **Gerenciar arquivos**. No menu suspenso, selecione **Upload** e faça upload dos seguintes arquivos **azuredeploy.json** e **azuredeploy.parameters.json** no diretório base do Cloud Shell.

        Note:: If you are working in your own subscription the [template files](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/tree/master/Allfiles/Exercises) are available in the GitHub lab repository.

1. Implante os seguintes modelos do ARM para criar a rede virtual e as sub-redes necessárias para este exercício:

   ```powershell
   $RGName = "ContosoResourceGroup"
   #create resource group if it doesnt exist
   New-AzResourceGroup -Name $RGName -Location "eastus"
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```
   
## Tarefa 2: criar CoreServicesVM

1. No portal do Azure, abra a sessão **PowerShell** no painel do **Cloud Shell**.

1. Na barra de ferramentas do painel do Cloud Shell, clique no ícone **Gerenciar arquivos**. No menu suspenso, clique em **Upload** e faça upload dos seguintes arquivos **CoreServicesVMazuredeploy.json** e **CoreServicesVMazuredeploy.parameters.json**, um a um, da pasta de origem **F:\Allfiles\Exercises\M02** para o diretório base do Cloud Shell.

1. Implante os seguintes modelos do ARM para criar as VMs necessárias para este exercício:

   >**Observação**: você será solicitado a fornecer uma senha de Administrador.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile CoreServicesVMazuredeploy.json -TemplateParameterFile CoreServicesVMazuredeploy.parameters.json
   ```
  
1. Após a conclusão da implantação, acesse a home page do portal do Azure e, depois, escolha **Máquinas Virtuais**.

1. Verifique se a máquina virtual foi criada.

## Tarefa 3: criar ManufacturingVM

1. No portal do Azure, abra a sessão **PowerShell** no painel do **Cloud Shell**.

1. Na barra de ferramentas do painel do Cloud Shell, clique no ícone **Gerenciar arquivos**. No menu suspenso, clique em **Carregar** e carregue os seguintes arquivos **ManufacturingVMazuredeploy.json** e **ManufacturingVMazuredeploy.parameters.json**, um a um, da pasta de origem **F:\Allfiles\Exercises\M02** para o diretório base do Cloud Shell.

1. Implante os seguintes modelos do ARM para criar as VMs necessárias para este exercício:

   >**Observação**: você será solicitado a fornecer uma senha de Administrador.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile ManufacturingVMazuredeploy.json -TemplateParameterFile ManufacturingVMazuredeploy.parameters.json
   ```
  
1. Após a conclusão da implantação, acesse a home page do portal do Azure e, depois, escolha **Máquinas Virtuais**.

1. Verifique se a máquina virtual foi criada.

## Tarefa 4: conectar-se às VMs usando RDP

1. Na página inicial do Portal do Azure, selecione **Máquinas Virtuais**.

1. Escolha **ManufacturingVM**.

1. Em **ManufacturingVM**, selecione **Conectar** e **RDP**.

1. Selecione **Baixar Arquivo RDP**.

1. Salve o arquivo RDP em sua área de trabalho.

1. Conecte-se ao **ManufacturingVM** usando o arquivo RDP e o nome de usuário **TestUser** e a senha fornecida durante a implantação. Após a conexão, minimize a sessão RDP.

1. Na página inicial do Portal do Azure, selecione **Máquinas Virtuais**.

1. Selecione **CoreServicesVM**.

1. Em **CoreServicesVM**, selecione **Conectar** e **RDP**.

1. Selecione **Baixar Arquivo RDP**.

1. Salve o arquivo RDP em sua área de trabalho.

1. Conecte-se ao **CoreServicesVM** usando o arquivo RDP e o nome de usuário **TestUser** e a senha fornecida durante a implantação.

1. Nas duas VMs, em **Escolher configurações de privacidade para seu dispositivo**, escolha **Aceitar**.

1. Em ambas as VMs, em **Redes**, selecione **Sim**.

1. Em **CoreServicesVM**, abra o PowerShell e execute o seguinte comando: ipconfig

1. Endereço IPv4 inválido.

## Tarefa 5: testar a conexão entre as VMs

1. Em **ManufacturingVM**, abra o PowerShell.

1. Use o comando a seguir para verificar se não há nenhuma conexão com CoreServicesVM em CoreServicesVnet. Use o endereço IPv4 para CoreServicesVM.

   ```Powershell
   Test-NetConnection 10.20.20.4 -port 3389
   ```

1. A conexão de teste deve falhar e você verá um resultado semelhante ao seguinte:

   ![Falha na Test-NetConnection.](../media/test-netconnection-fail.png)

## Tarefa 6: criar o gateway da CoreServicesVnet

1. Em **Pesquisar recursos, serviços e documentos (G+/)**, insira **Gateway de rede virtual** e selecione **Gateways de rede virtual** nos resultados.
   ![Pesquise o gateway de rede virtual no Portal do Azure.](../media/virtual-network-gateway-search.png)

1. Em gateways de rede virtual, selecione **+ Criar**.

1. Use as informações da seguinte tabela para criar o gateway de rede virtual:

   | **Tab**         | **Seção**       | **Opção**                                  | **Valor**                    |
   | --------------- | ----------------- | ------------------------------------------- | ---------------------------- |
   | Noções básicas          | Detalhes do projeto   | Subscription                                | Nenhuma alteração necessária          |
   |                 |                   | ResourceGroup                               | ContosoResourceGroup         |
   |                 | Detalhes da Instância  | Nome                                        | CoreServicesVnetGateway      |
   |                 |                   | Região                                      | Leste dos EUA                      |
   |                 |                   | Tipo de gateway                                | VPN                          |
   |                 |                   | SKU                                         | VpnGw1                       |
   |                 |                   | Generation                                  | Geração1                  |
   |                 |                   | Rede virtual                             | CoreServicesVnet             |
   |                 |                   | Sub-rede                                      | GatewaySubnet (10.20.0.0/27) |
   |                 |                   | Tipo de endereço IP público                      | Standard                     |
   |                 | Endereço IP público | Endereço IP público                           | Criar novo                   |
   |                 |                   | Nome do endereço IP público                      | CoreServicesVnetGateway-ip   |
   |                 |                   | Habilitar o modo ativo-ativo                   | Desabilitado                     |
   |                 |                   | Configurar BGP                               | Desabilitado                     |
   | Examinar + criar |                   | Revise suas configurações e selecione **Criar**. |                              |

   >**Observação**: a criação de um gateway de rede virtual pode levar de 15 a 30 minutos. Você não precisa esperar a conclusão da implantação. Prossiga para a criação do próximo gateway. 

## Tarefa 7: criar o gateway da ManufacturingVnet

### Criar o GatewaySubnet

   >**Observação:** o modelo criou o GatewaySubnet para o CoreServicesVnet. Aqui você cria a sub-rede manualmente. 

1. Pesquise e selecione o **ManufacturingVnet**.

1. Na folha **Configurações**, escolha **Sub-redes** e, em seguida, **+ Sub-rede**. 

    | Parâmetro | Valor |
    | --------------- | ----------------- | 
    | Finalidade da sub-rede | **Gateway de Rede Virtual** |
    | Tamanho | **/27 (32 endereços)** |

1. Selecione **Adicionar**. 

### Criar o gateway de rede virtual

1. Em **Pesquisar recursos, serviços e documentos (G+/)**, insira **Gateway de rede virtual** e selecione **Gateways de rede virtual** nos resultados.

1. Em gateways de rede virtual, selecione **+ Criar**.

1. Use essas informações e a guia **Configurações** para criar o gateway de rede virtual. 

   | **Tab**         | **Seção**       | **Opção**                                  | **Valor**                    |
   | --------------- | ----------------- | ------------------------------------------- | ---------------------------- |
   | Noções básicas          | Detalhes do projeto   | Subscription                                | Nenhuma alteração necessária          |
   |                 |                   | ResourceGroup                               | ContosoResourceGroup         |
   |                 | Detalhes da Instância  | Nome                                        | ManufacturingVnetGateway     |
   |                 |                   | Region                                      | Norte da Europa                 |
   |                 |                   | Tipo de gateway                                | VPN                          |
   |                 |                   | SKU                                         | VpnGw1                       |
   |                 |                   | Generation                                  | Geração1                  |
   |                 |                   | Rede virtual                             | ManufacturingVnet            |
   |                 |                   | Sub-rede                                      | GatewaySubnet |
   |                 |                   | Tipo de Endereço IP Público                      | Standard                     |
   |                 | Endereço IP público | Endereço IP público                           | Criar novo                   |
   |                 |                   | Nome do endereço IP público                      | ManufacturingVnetGateway-ip  |
   |                 |                   | Habilitar o modo ativo-ativo                   | Desabilitado                     |
   |                 |                   | Configurar BGP                               | Desabilitado                     |
   | Examinar + criar |                   | Revise suas configurações e selecione **Criar**. |                              |

   >**Observação**: a criação de um gateway de rede virtual pode levar de 15 a 30 minutos.

## Tarefa 8: conectar a CoreServicesVnet à ManufacturingVnet

1. Em **Pesquisar recursos, serviços e documentos (G+/)**, insira **Gateway de rede virtual** e selecione **Gateways de rede virtual** nos resultados.

1. Em gateways de rede virtual, selecione **CoreServicesVnetGateway**.

1. Em CoreServicesGateway, selecione **Conexões** e selecione **+ Adicionar**.

   >**Observação**: você não poderá concluir essa configuração até que os gateways de rede virtual sejam totalmente implantados.

1. Use essas informações e a guia **Configurações** para criar o gateway de rede virtual. 


   | **Opção**                     | **Valor**                         |
   | ------------------------------ | --------------------------------- |
   | Nome                           | CoreServicesGW-to-ManufacturingGW |
   | Tipo de conexão                | VNet a VNet                      |
   | Região                         | Leste dos EUA                           |
   | Primeiro gateway de rede virtual  | CoreServicesVnetGateway           |
   | Segundo gateway de rede virtual | ManufacturingVnetGateway          |
   | Chave compartilhada (PSK)               | abc123                            |
   | Usar endereço IP privado do Azure   | Não selecionada                      |
   | Habilitar BGP                     | Não selecionada                      |
   | Protocolo IKE                   | IKEv2                             |
   | Subscription                   | Nenhuma alteração necessária               |
   | Resource group                 | Nenhuma alteração necessária               |


1. Selecione **Revisar + criar** e, em seguida, **Criar** para criar a conexão.

## Tarefa 9: conectar a ManufacturingVnet à CoreServicesVnet

1. Em **Pesquisar recursos, serviços e documentos (G+/)**, insira **Gateway de rede virtual** e selecione **Gateways de rede virtual** nos resultados.

1. Em gateways de rede virtual, selecione **ManufacturingVnetGateway**.

1. Em CoreServicesGateway, selecione **Conexões** e selecione **+ Adicionar**.

1. Use as informações da seguinte tabela para criar a conexão:

   | **Opção**                     | **Valor**                         |
   | ------------------------------ | --------------------------------- |
   | Nome                           | ManufacturingGW-to-CoreServicesGW |
   | Tipo de conexão                | VNet a VNet                      |
   | Localidade                       | Norte da Europa                      |
   | Primeiro gateway de rede virtual  | ManufacturingVnetGateway          |
   | Segundo gateway de rede virtual | CoreServicesVnetGateway           |
   | Chave compartilhada (PSK)               | abc123                            |
   | Usar endereço IP privado do Azure   | Não selecionada                      |
   | Habilitar BGP                     | Não selecionada                      |
   | Protocolo IKE                   | IKEv2                             |
   | Subscription                   | Nenhuma alteração necessária               |
   | Resource group                 | Nenhuma alteração necessária               |


1. Selecione **Revisar + criar** e, em seguida, **Criar** para criar a conexão.

## Tarefa 10: verificar se as conexões se conectam

1. Em **Pesquisar recursos, serviços e documentos (G +/)**, insira **vpn** e selecione **conexões** nos resultados.

1. Aguarde até que o status de ambas as conexões seja **Conectado**. Talvez seja necessário atualizar a tela.

   ![Conexões de Gateway de VPN criadas com êxito.](../media/connections-status-connected.png)

## Tarefa 11: testar a conexão entre as VMs

1. Em **ManufacturingVM**, abra o PowerShell.

1. Use o comando a seguir para verificar se agora há uma conexão com o CoreServicesVM na CoreServicesVnet. Use o endereço IPv4 para CoreServicesVM.

   ```Powershell
   Test-NetConnection 10.20.20.4 -port 3389
   ```

1. A conexão de teste deve ser bem-sucedida e você verá um resultado semelhante ao seguinte:

   ![Test-NetConnection bem-sucedida.](../media/test-connection-succeeded.png)

1. Feche as janelas de conexão da Área de Trabalho Remota.

## Estender seu aprendizado com o Copilot

O Copilot pode ajudar você a aprender a usar as ferramentas de script do Azure. O Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precisar de mais informações. Abra um navegador do Edge e escolha Copilot (canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar essas solicitações.
+ Quais são os principais tipos de gateways de VPN do Azure e por que você usaria cada tipo?
+ Quais fatores devo considerar ao selecionar o SKU do gateway de VPN do Azure? Forneça exemplos
+ Há custos associados aos gateways de VPN do Azure?


## Saiba mais com treinamento individual

+ [Conectar sua rede local ao Azure com o Gateway de VPN](https://learn.microsoft.com/training/modules/connect-on-premises-network-with-vpn-gateway/). Neste módulo, você aprenderá a usar a CLI para provisionar gateways de VPN.
+ [Solucionar problemas de gateways de VPN no Microsoft Azure](https://learn.microsoft.com/training/modules/troubleshoot-vpn-gateways/). Neste módulo, você aprenderá como monitorar e solucionar problemas relacionados a VPNs site a site e ponto a site.

## Principais aspectos a serem lembrados

Parabéns por concluir o laboratório. Aqui estão as principais lições desse laboratório. 

+ O Gateway de VPN do Azure é um serviço que fornece conectividade segura entre suas redes locais e as redes virtuais do Azure.
+ As conexões S2S (site a site) conectam sua rede local a uma rede virtual do Azure através de túneis VPN IPsec/IKE. Ideal para cenários de nuvem híbrida.
+ As conexões P2S (ponto a site) conectam clientes individuais a uma rede virtual do Azure a partir de locais remotos. Os protocolos VPN incluem OpenVPN, IKEv2 ou SSTP. Útil para trabalhadores remotos.
+ As conexões VNet a VNet conectam duas ou mais redes virtuais do Azure usando túneis VPN IPsec/IKE. Adequado para implantações de várias regiões ou várias VNets.
+ Diferentes SKUs do Gateway de VPN oferecem níveis variados de desempenho, taxa de transferência e recursos. 

