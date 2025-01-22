---
Exercise:
  title: M07 - Unidade 6 Criar um ponto de extremidade privado do Azure usando o Azure PowerShell
  module: Module 07 - Design and implement private access to Azure Services
---

# M07-Unidade 6 Criar um ponto de extremidade privado do Azure usando o Azure PowerShell

## Cenário do exercício

Introdução ao Link Privado do Azure usando um Ponto de Extremidade Privado para se conectar com segurança a um aplicativo Web do Azure. Há muitas maneiras de criar pontos de extremidade, incluindo o Portal, a CLI, o PowerShell etc.

![Diagrama de arquitetura de ponto de extremidade privado.](../media/6-exercise-create-azure-private-endpoint-using-azure-powershell.png)


   >**Observação:** uma **[simulação de laboratório interativa](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Create%20an%20Azure%20private%20endpoint%20using%20Azure%20PowerShell)** está disponível e permite que você clique neste laboratório no seu próprio ritmo. Você pode encontrar pequenas diferenças entre a simulação interativa e o laboratório hospedado, mas os principais conceitos e ideias que estão sendo demonstrados são os mesmos.

### Tempo estimado: 45 minutos

Você criará um ponto de extremidade privado para um aplicativo Web do Azure e implantará uma máquina virtual para testar a conexão privada.

Pontos de extremidade privados podem ser criados para diferentes tipos de serviços do Azure, como o SQL do Azure e o Armazenamento do Azure.

**Pré-requisitos**

- Um Aplicativo Web do Azure com um PremiumV2-tier ou um plano de serviço de aplicativo mais alto implantado em sua assinatura do Azure.

- As etapas abaixo oferecem orientações para a criação do grupo de recursos e do aplicativo Web necessário.

1. Localize e abra **parameters.json** na pasta M07. Abra-o no Bloco de notas e localize a linha "value": "GEN-UNIQUE". Substitua a cadeia de caracteres GEN-UNIQUE do espaço reservado por um valor exclusivo de nome do aplicativo Web. Salve essa alteração.

1. No portal do Azure, abra a sessão **PowerShell** no painel do **Cloud Shell**.

1. Na barra de ferramentas do painel do Cloud Shell, clique no ícone **Gerenciar arquivos**. No menu suspenso, clique em **Upload** e faça ulpload dos arquivos **template.json** e **parameters.json** a seguir no diretório base do Cloud Shell, um por um.

Se você optar por instalar e usar o PowerShell localmente, este exemplo exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute ```Get-Module -ListAvailable Az``` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o ```Connect-AzAccount``` para criar uma conexão com o Azure.

Neste exercício, você vai:

- Tarefa 1: criar um grupo de recursos
- Tarefa 2: criar uma rede virtual e um bastion host
- Tarefa 3: criar uma máquina virtual de teste
- Tarefa 4: criar um ponto de extremidade privado
- Tarefa 5: configurar a zona DNS privada
- Tarefa 6: testar a conectividade com o ponto de extremidade privado

## Tarefa 1: criar um grupo de recursos e implantar o aplicativo Web de pré-requisito

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [New-AzResourceGroup](https://docs.microsoft.com/en-us/powershell/module/az.resources/new-azresourcegroup):

```PowerShell
New-AzResourceGroup -Name 'CreatePrivateEndpointQS-rg' -Location 'eastus'
```

Implante os seguintes modelos do ARM para criar o aplicativo web PremiumV2-tier Azure necessário para este exercício:

   ```powershell
   $RGName = "CreatePrivateEndpointQS-rg"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile template.json -TemplateParameterFile parameters.json
   ```

Se você receber um erro (por exemplo, ao examinar o status de implantação no Portal) como "O site com o nome GEN-UNIQUE já existe". certifique-se de acessar os pré-requisitos mencionados acima sobre a edição do modelo.

## Tarefa 2: criar uma rede virtual e um bastion host

Você criará uma rede virtual, uma sub-rede e um bastion host.

O bastion host será usado para se conectar com segurança à máquina virtual para testar o ponto de extremidade privado.

Criar uma rede virtual e um bastion host com:

- New-AzVirtualNetwork

- New-AzPublicIpAddress

- New-AzBastion

```PowerShell

## Create backend subnet config. ##

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##

$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##

$parameters1 = @{

 Name = 'MyVNet'

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Location = 'eastus'

 AddressPrefix = '10.0.0.0/16'

 Subnet = $subnetConfig, $bastsubnetConfig

}

$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##

$parameters2 = @{

 Name = 'myBastionIP'

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Location = 'eastus'

 Sku = 'Standard'

 AllocationMethod = 'Static'

}

$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##

$parameters3 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Name = 'myBastion'

 PublicIpAddress = $publicip

 VirtualNetwork = $vnet

}

New-AzBastion @parameters3
```

## Tarefa 3: criar uma máquina virtual de teste

Nesta seção, você criará uma máquina virtual que será usada para testar o ponto de extremidade privado.

Crie a máquina virtual com os seguintes comandos:

- Get-Credential (Observação: você será solicitado a fornecer uma senha de administrador.)

- New-AzNetworkInterface

- New-AzVM

- New-AzVMConfig

- Set-AzVMOperatingSystem

- Set-AzVMSourceImage

- Add-AzVMNetworkInterface

```PowerShell
## Set credentials for server admin and password. ##

$cred = Get-Credential

## Command to get virtual network configuration. ##

$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreatePrivateEndpointQS-rg

## Command to create network interface for VM ##

$parameters1 = @{

 Name = 'myNicVM'

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Location = 'eastus'

 Subnet = $vnet.Subnets[0]

}

$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##

$parameters2 = @{

 VMName = 'myVM'

 VMSize = 'Standard_DS1_v2'

}

$parameters3 = @{

 ComputerName = 'myVM'

 Credential = $cred

}

$parameters4 = @{

 PublisherName = 'MicrosoftWindowsServer'

 Offer = 'WindowsServer'

 Skus = '2019-Datacenter'

 Version = 'latest'

}

$vmConfig = New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##

New-AzVM -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Location 'eastus' -VM $vmConfig 


```

O Azure fornece um IP efêmero para Máquinas Virtuais do Azure que não receberam um endereço IP público ou que estão no pool de back-end de um Azure Load Balancer Básico interno. O mecanismo de IP efêmero fornece um endereço IP de saída que não é configurável.

O IP efêmero é desabilitado quando um endereço IP público é atribuído à máquina virtual ou quando a máquina virtual é colocada no pool de back-end de um Standard Load Balancer com ou sem regras de saída. Se um recurso de gateway da NAT da Rede Virtual do Azure for atribuído à sub-rede da máquina virtual, o IP efêmero será desabilitado.

Para obter mais informações sobre conexões de saída no Azure, confira Como usar o SNAT (conversão de endereços de rede de origem) para conexões de saída.

## Tarefa 4: criar um ponto de extremidade privado

Nesta seção, você criará o ponto de extremidade privado e a conexão usando:

- New-AzPrivateLinkServiceConnection

- New-AzPrivateEndpoint

```PowerShell
## Place web app into variable. This assumes that only one web app exists in the resource group. ##

$webapp = Get-AzWebApp -ResourceGroupName CreatePrivateEndpointQS-rg

## Create Private Endpoint connection. ##

$parameters1 = @{

 Name = 'myConnection'

 PrivateLinkServiceId = $webapp.ID

 GroupID = 'sites'

}

$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##

$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Disable private endpoint network policy ##

$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"

$vnet | Set-AzVirtualNetwork

## Create private endpoint

$parameters2 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Name = 'myPrivateEndpoint'

 Location = 'eastus'

 Subnet = $vnet.Subnets[0]

 PrivateLinkServiceConnection = $privateEndpointConnection

}

New-AzPrivateEndpoint @parameters2 
```

## Tarefa 5: configurar a zona DNS privada

Nesta seção, você criará e configurará a zona DNS privada usando:

- New-AzPrivateDnsZone

- New-AzPrivateDnsVirtualNetworkLink

- New-AzPrivateDnsZoneConfig

- New-AzPrivateDnsZoneGroup

```PowerShell
## Place virtual network into variable. ##

$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Create private dns zone. ##

$parameters1 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 Name = 'privatelink.azurewebsites.net'

}

$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##

$parameters2 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 ZoneName = 'privatelink.azurewebsites.net'

 Name = 'myLink'

 VirtualNetworkId = $vnet.Id

}

$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##

$parameters3 = @{

 Name = 'privatelink.azurewebsites.net'

 PrivateDnsZoneId = $zone.ResourceId

}

$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##

$parameters4 = @{

 ResourceGroupName = 'CreatePrivateEndpointQS-rg'

 PrivateEndpointName = 'myPrivateEndpoint'

 Name = 'myZoneGroup'

 PrivateDnsZoneConfig = $config

}

New-AzPrivateDnsZoneGroup @parameters4 
```

## Tarefa 6: testar a conectividade com o ponto de extremidade privado

Nesta seção, você usará a máquina virtual criada na etapa anterior para se conectar ao aplicativo Web no ponto de extremidade privado.

1. Entre no [Portal do Azure](https://portal.azure.com/)

1. Selecione **Grupos de recursos** no painel de navegação à esquerda.

1. Selecione **CreatePrivateEndpointQS-rg**.

1. Selecione **myVM**.

1. Na página de visão geral de **myVM**, escolha **Conectar** e **Bastion**.

1. Selecione o botão azul **Usar Bastion**.

1. Insira o nome de usuário e a senha que você inseriu durante a criação da máquina virtual.

1. Abra o Windows PowerShell no servidor depois de se conectar.

1. Digite nslookup&lt;your- webapp-name&gt;.azurewebsites.net. Substitua &lt;your-webapp-name&gt; pelo nome do aplicativo Web criado nas etapas anteriores. Você receberá uma mensagem semelhante à mostrada abaixo:

  ```
  Server: UnKnown
  
  Address: 168.63.129.16
  
  Non-authoritative answer:
  
  Name: mywebapp8675.privatelink.azurewebsites.net
  
  Address: 10.0.0.5
  
  Aliases: mywebapp8675.azurewebsites.net 
  ```  

O endereço IP privado **10.0.0.5** é retornado para o nome do aplicativo Web. Esse endereço está na sub-rede da rede virtual criada anteriormente.

1. Na conexão do bastion com **myVM**, abra o Internet Explorer.
1. Insira a URL do aplicativo Web,**https://&lt;your-webapp-name&gt;.azurewebsites.net**
1. Você receberá a página de aplicativo Web padrão se seu aplicativo não tiver sido implantado: ![Captura de tela da página no Azure indicando que um serviço de aplicativo está em funcionamento](../media/web-app-default-page.png)
1. Feche a conexão com **myVM**.

## Limpar os recursos

Quando terminar de usar o ponto de extremidade privado e a VM, use [Remove-AzResourceGroup](https://docs.microsoft.com/en-us/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele tem:

```PowerShell
Remove-AzResourceGroup -Name CreatePrivateEndpointQS-rg -Force -AsJob
```

## Estender seu aprendizado com o Copilot

O Copilot pode ajudar você a aprender a usar as ferramentas de script do Azure. O Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precisar de mais informações. Abra um navegador do Edge e escolha Copilot (canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar essas solicitações.
+ Dê um exemplo de quando usar um ponto de extremidade privado em vez de um ponto de extremidade de serviço.
+ Como posso solucionar problemas de conectividade de ponto de extremidade privado?

## Saiba mais com treinamento individual

+ [Introdução ao Link Privado do Azure](https://learn.microsoft.com/training/modules/introduction-azure-private-link/) Neste módulo, você descobrirá como o Link Privado do Azure permite a conectividade particular com os serviços do Azure, incluindo recursos, funcionamento e casos de uso.
+ [Projetar e implementar o acesso privado aos Serviços do Azure](https://learn.microsoft.com/en-us/training/modules/design-implement-private-access-to-azure-services/). Neste módulo, você aprenderá a projetar e a implementar o acesso privado aos Serviços do Azure com o Link Privado do Azure, e os pontos de extremidade do serviço de rede virtual.

## Principais aspectos a serem lembrados

+ O Link Privado do Azure permite acessar os serviços de PaaS do Azure (por exemplo, o Armazenamento do Azure e o Banco de Dados SQL) e serviços de parceiros/de propriedade de clientes hospedados no Azure em um ponto de extremidade privado da sua rede virtual.
+ Um ponto de extremidade privado é uma adaptador de rede que usa um endereço IP privado de sua rede virtual. Essa interface de rede conecta você de forma privada e segura a um serviço da plataforma do Link Privado do Azure. 
