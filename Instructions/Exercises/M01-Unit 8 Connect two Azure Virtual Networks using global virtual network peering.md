---
Exercise:
  title: M01 - Unidade 8 Cconectar duas redes virtuais do Azure usando o emparelhamento de rede virtual global
  module: Module 01 - Introduction to Azure Virtual Networks
---

# M01 - Unidade 8 Cconectar duas redes virtuais do Azure usando o emparelhamento de rede virtual global

## Cenário do exercício

Nesta unidade, você configurará a conectividade entre a CoreServicesVnet e a ManufacturingVnet adicionando emparelhamentos a fim de permitir o fluxo de tráfego.

![Diagrama do emparelhamento de rede virtual.](../media/8-exercise-connect-two-azure-virtual-networks-global.png)

### Habilidades de trabalho

Neste exercício, você vai:

+ Tarefa 1: criar uma máquina virtual para testar a configuração
+ Tarefa 2: conectar-se às VMs de teste usando RDP
+ Tarefa 3: testar a conexão entre as VMs
+ Tarefa 4: criar emparelhamentos de VNet entre a CoreServicesVnet e a ManufacturingVnet
+ Tarefa 5: testar a conexão entre as VMs

### Simulações interativas de laboratório

>**Observação**: as simulações de laboratório fornecidas anteriormente foram desativadas.

### Tempo estimado: 20 minutos

## Tarefa 1: criar uma máquina virtual para testar a configuração

Nesta seção, você criará uma VM de teste na VNet para testar se pode acessar recursos dentro de outra rede virtual do Azure a partir da VNet.

### Criar ManufacturingVM

1. No portal do Azure, clique no ícone do Cloud Shell (canto superior direito). Se necessário, configure o shell.  
    + Selecione **PowerShell**.
    + Selecione **Nenhuma Conta de Armazenamento necessária** e sua **Assinatura** e, em seguida, selecione **Aplicar**.
    + Aguarde até que o terminal seja criado e um prompt seja exibido. 

1. Na barra de ferramentas do painel do Cloud Shell, clique no ícone **Gerenciar arquivos**. No menu suspenso, clique em **Upload** e faça upload dos seguintes arquivos **ManufacturingVMazuredeploy.json** e **ManufacturingVMazuredeploy.parameters.json**.

    >**Observação:** se você estiver trabalhando em sua própria assinatura, os [arquivos de modelo](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/tree/master/Allfiles/Exercises) estarão disponíveis no repositório do GitHub Lab.

1. Implante os seguintes modelos do ARM para criar as VMs necessárias para este exercício:

   >**Observação**: você será solicitado a fornecer uma senha de Administrador.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile ManufacturingVMazuredeploy.json -TemplateParameterFile ManufacturingVMazuredeploy.parameters.json
   ```
  
1. Após a conclusão da implantação, acesse a home page do portal do Azure e, depois, escolha **Máquinas Virtuais**.

1. Verifique se a máquina virtual foi criada.

## Tarefa 2: conectar-se às VMs de teste usando RDP

1. Na página inicial do Portal do Azure, selecione **Máquinas Virtuais**.

1. Escolha **ManufacturingVM**.

1. Em ManufacturingVM, escolha **Conectar &gt; RDP**.

1. Em ManufacturingVM \| Conectar, selecione **Baixar arquivo RDP**.

1. Salve o arquivo RDP em sua área de trabalho.

1. Conecte-se a ManufacturingVM usando o arquivo RDP e o nome de usuário **TestUser** e a senha fornecida durante a implantação.

1. Na página inicial do Portal do Azure, selecione **Máquinas Virtuais**.

1. Escolha **TestVM1**.

1. Em TestVM1, selecione **Conectar &gt; RDP**.

1. Em TestVM1 \| Conectar, selecione **Baixar arquivo RDP**.

1. Salve o arquivo RDP em sua área de trabalho.

1. Conecte-se à TestVM1 usando o arquivo RDP e o nome de usuário **TestUser** e a senha fornecida durante a implantação.

1. Nas duas VMs, em **Escolher configurações de privacidade para seu dispositivo**, escolha **Aceitar**.

1. Em ambas as VMs, em **Redes**, selecione **Sim**.

1. Na TestVM1, abra um prompt do PowerShell e execute o comando a seguir: ipconfig

1. Endereço IPv4 inválido.

## Tarefa 3: testar a conexão entre as VMs

1. Na ManufacturingVM, abra um prompt do PowerShell.

1. Use o comando a seguir para verificar se não há conexão com TestVM1 em CoreServicesVnet. Use o endereço IPv4 para TestVM1.

   ```powershell
    Test-NetConnection 10.20.20.4 -port 3389
    ```

1. A conexão de teste deve falhar, e você verá um resultado semelhante ao seguinte: ![ janela do PowerShell com Test-NetConnection 10.20.20.4 -port 3389 mostrando falha ](../media/test-netconnection-fail.png)

## Tarefa 4: criar emparelhamentos de VNet entre a CoreServicesVnet e a ManufacturingVnet

1. No home page do Azure, escolha **Redes Virtuais** e, depois, escolha **CoreServicesVnet**.

1. Em CoreServicesVnet, em **Configurações**, escolha **Emparelhamentos**.
   ![Captura de tela das configurações do emparelhamento VNet dos serviços principais ](../media/create-peering-on-coreservicesvnet.png)

1. Em CoreServicesVnet \| Emparelhamentos, selecione **+ Adicionar**.

1. Use essas informações para criar o emparelhamento. Quando terminar, selecione **Adicionar**. 

   **Resumo da rede virtual remota**

   | **Opção**                                    | **Valor**                             |
   | ------------------------------------ | --------------------------------------------- | 
   | Nome do link de emparelhamento    | `ManufacturingVnet-to-CoreServicesVnet` |
   | Rede virtual | ManufacturingVnet |

    **Configurações do emparelhamento da rede virtual remota**
   
   | **Opção**                                    | **Valor**                             |
   | ------------------------------------ | --------------------------------------------- | 
   | Permitir que "ManufacturingVnet" acesse "CoreServicesVnet" | Habilitado |
   |"ManufacturingVnet" receba tráfego encaminhado de "CoreServicesVnet" | Habilitado |
 
    **Resumo da rede virtual local**

    | **Opção**                                    | **Valor**                             |
    | ------------------------------------ | --------------------------------------------- | 
    | Nome do link de emparelhamento | `CoreServicesVnet-to-ManufacturingVnet` |
 
    **Configurações de emparelhamento da rede virtual local**
   
    | **Opção**                                    | **Valor**                             |
    | ------------------------------------ | --------------------------------------------- | 
    | Permitir que "CoreServicesVnet" acesse "ManufacturingVnet" | Habilitado
    | Permitir que "CoreServicesVnet" receba tráfego encaminhado de "ManufacturingVnet" | Habilitado |
 
1. Em Emparelhamentos \| CoreServicesVnet, verifique se o emparelhamento **CoreServicesVnet-to-ManufacturingVnet** está **Conectado**.

1. Em Redes virtuais, escolha **ManufacturingVnet** e verifique se o emparelhamento **ManufacturingVnet-to-CoreServicesVnet** está **Conectado**.

## Tarefa 5: testar a conexão entre as VMs

1. Na ManufacturingVM, abra um prompt do PowerShell.

1. Use o comando a seguir para verificar se já há conexão com TestVM1 em CoreServicesVnet.

   ```powershell
    Test-NetConnection 10.20.20.4 -port 3389
    ```

1. A conexão de teste deve ter êxito, e você verá um resultado semelhante ao seguinte: ![ janela do PowerShell com Test-NetConnection 10.20.20.4 -port 3389 mostrando que o teste de TCP foi bem-sucedido: true ](../media/test-connection-succeeded.png)


## Limpar os recursos

   >**Observação**: lembre-se de remover todos os recursos recém-criados do Azure que você não usa mais. Remover recursos não utilizados garante que você não veja encargos inesperados.

1. No portal do Azure, abra a sessão **PowerShell** no painel do **Cloud Shell**. (Crie armazenamento do Cloud Shell, se necessário, usando as configurações padrão.)

1. Exclua todos os grupos de recursos criados em todos os laboratórios deste módulo executando o seguinte comando:

   ```powershell
   Remove-AzResourceGroup -Name 'ContosoResourceGroup' -Force -AsJob
   ```
   >**Observação**: o comando é executado de maneira assíncrona (conforme determinado pelo parâmetro -AsJob), portanto, embora você possa executar outro comando do PowerShell imediatamente após na mesma sessão do PowerShell, levará alguns minutos antes dos grupos de recursos serem de fato removidos.
   
## Estender seu aprendizado com o Copilot

O Copilot pode ajudar você a aprender a usar as ferramentas de script do Azure. O Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precisar de mais informações. Abra um navegador do Edge e escolha Copilot (canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar essas solicitações.
+ Quais são os erros mais comuns ao configurar o emparelhamento de rede virtual do Azure?
+ No Azure, se eu emparelhar a Vnet1 com a Vnet2 e, em seguida, emparelhar a Vnet2 com a Vnet3, a Vnet1 será emparelhada com a Vnet3?
+ Os firewalls e gateways podem afetar o emparelhamento de rede virtual do Azure?


## Saiba mais com treinamento individual

+ [Introdução às Redes Virtuais do Azure](https://learn.microsoft.com/training/modules/introduction-to-azure-virtual-networks/). Neste módulo, você aprenderá a projetar e implementar os serviços de rede do Azure. Você aprenderá sobre redes virtuais, IPs públicos e privados, DNS, emparelhamento de rede virtual, roteamento e NAT Virtual do Azure.
+ [Distribuir seus serviços em redes virtuais do Azure e integre-os usando o emparelhamento de rede virtual](https://learn.microsoft.com/training/modules/integrate-vnets-with-vnet-peering/). Neste módulo, você aprenderá a configurar o emparelhamento de rede virtual.

## Principais aspectos a serem lembrados

Parabéns por concluir o laboratório. Aqui estão as principais lições desse laboratório. 

+ O emparelhamento de rede virtual permite que você conecte perfeitamente duas redes virtuais do Azure. As redes virtuais aparecerão como uma só para fins de conectividade.
+ O Azure dá suporte à conexão de redes virtuais na mesma região do Azure e entre regiões do Azure (global).
+ O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas é roteado diretamente pela infraestrutura de backbone da Microsoft, não por um gateway ou pela Internet pública.
+ É possível redimensionar o espaço de endereço de redes virtuais do Azure emparelhadas sem gerar nenhum tempo de inatividade no espaço de endereço emparelhado no momento.
