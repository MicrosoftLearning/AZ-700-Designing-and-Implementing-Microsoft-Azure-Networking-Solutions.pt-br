---
Exercise:
  title: M01 - Unidade 6 Definir configurações de DNS no Azure
  module: Module 01 - Introduction to Azure Virtual Networks
---

# M01 - Unidade 6 Definir configurações de DNS no Azure

## Cenário do exercício

Nesta unidade, você configurará a resolução de nomes DNS para Contoso Ltd. Você criará uma zona DNS privada chamada contoso.com, vinculará as VNets para registro e resolução e, depois, criará duas máquinas virtuais e testará a configuração.

![Diagrama da arquitetura de DNS.](../media/6-exercise-configure-domain-name-servers-configuration-azure.png)

Neste exercício, você vai:

+ Tarefa 1: criar uma zona DNS privada
+ Tarefa 2: vincular a sub-rede para registro automático
+ Tarefa 3: criar máquinas virtuais para testar a configuração
+ Tarefa 4: verificar se os registros estão presentes na zona DNS

   >**Observação:** há uma **[simulação interativa de laboratório](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Configure%20DNS%20settings%20in%20Azure)** disponível que permite que você clique neste laboratório no seu próprio ritmo. Você pode encontrar pequenas diferenças entre a simulação interativa e o laboratório hospedado, mas os principais conceitos e ideias que estão sendo demonstrados são os mesmos.

### Tempo estimado: 25 minutos

## Tarefa 1: criar uma zona DNS privada

1. Vá para [Portal do Azure](https://portal.azure.com/).

1. Na home page do Azure, na barra de pesquisa, insira o DNS e selecione **Zonas DNS privadas**.  
   ![Home page do portal do Azure com a pesquisa de DNS.](../media/create-private-dns-zone.png)

1. Nas zonas DNS privadas, escolha **+ Criar**.

1. Use as informações da tabela abaixo para criar a zona DNS privada.

    | **Tab**         | **Opção**                             | **Valor**            |
    | --------------- | -------------------------------------- | -------------------- |
    | Noções básicas          | Grupo de recursos                         | ContosoResourceGroup |
    |                 | Nome                                   | Contoso.com          |
    | Marcações            | Nenhuma alteração necessária                    |                      |
    | Examinar + criar | Examine suas configurações e escolha Criar |                      |

1. Aguarde a conclusão da implantação e, então, escolha **Ir para o recurso**.

1. Verifique se a zona foi criada.

## Tarefa 2: vincular a sub-rede para registro automático

1. Em Contoso.com, em **Gerenciamento de DNS**, escolha **Links de rede virtual**.

1. Em Contoso.com \| Links de rede virtual, selecione **+ Adicionar**.

    ![contoso.com \| Links virtuais com a opção + Adicionar destacada.](../media/add-network-link-dns.png)

1. Use as informações da tabela abaixo para adicionar o link de rede virtual.

    | **Opção**                          | **Valor**                               |
    | ----------------------------------- | --------------------------------------- |
    | Nome do link                           | CoreServicesVnetLink                    |
    | Subscription                        | Nenhuma alteração necessária                     |
    | Rede Virtual                     | CoreServicesVnet (ContosoResourceGroup) |
    | Habilitar o registro automático            | Selecionadas                                |
    | Examine suas configurações e escolha OK. |                                         |

1. Selecione **Atualizar**.

1. Verifique se CoreServicesVnetLink foi criada e se o registro automático está habilitado.

1. Repita as etapas dois a cinco para ManufacturingVnet usando as informações na tabela abaixo:

    | **Opção**                          | **Valor**                                |
    | ----------------------------------- | ---------------------------------------- |
    | Nome do link                           | ManufacturingVnetLink                    |
    | Subscription                        | Nenhuma alteração necessária                      |
    | Rede Virtual                     | ManufacturingVnet (ContosoResourceGroup) |
    | Habilitar o registro automático            | Selecionadas                                 |
    | Examine suas configurações e escolha OK. |                                          |

1. Selecione **Atualizar**.

1. Verifique se ManufacturingVnetLink foi criada e se o registro automático está habilitado.

1. Repita as etapas dois a cinco para ResearchVnet usando as informações na tabela abaixo:

    | **Opção**                          | **Valor**                           |
    | ----------------------------------- | ----------------------------------- |
    | Nome do link                           | ResearchVnetLink                    |
    | Subscription                        | Nenhuma alteração necessária                 |
    | Rede Virtual                     | ResearchVnet (ContosoResourceGroup) |
    | Habilitar o registro automático            | Selecionadas                            |
    | Examine suas configurações e escolha OK. |                                     |

1. Selecione **Atualizar**.

1. Verifique se ResearchVnetLink foi criada e se o registro automático está habilitado.

## Tarefa 3: criar máquinas virtuais para testar a configuração

Nesta seção, você criará duas VMs de teste para testar a configuração da zona DNS privada.

1. No portal do Azure, clique no ícone do Cloud Shell (canto superior direito). Se necessário, configure o shell.  
    + Selecione **PowerShell**.
    + Selecione **Nenhuma Conta de Armazenamento necessária** e sua **Assinatura** e, em seguida, selecione **Aplicar**.
    + Aguarde até que o terminal seja criado e um prompt seja exibido. 

1. Na barra de ferramentas do painel do Cloud Shell, clique no ícone **Gerenciar arquivos**. No menu suspenso, clique em **Upload** e faça upload dos seguintes arquivos **azuredeploy.json** e **azuredeploy.parameters.json**, um a um, da pasta de origem **F:\Allfiles\Exercises\M01** para o diretório base do Cloud Shell.

1. Implante os seguintes modelos do ARM para criar as VMs necessárias para este exercício:

   >**Observação**: você será solicitado a fornecer uma senha de Administrador.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```
  
1. Após a conclusão da implantação, acesse a home page do portal do Azure e, depois, escolha **Máquinas Virtuais**.

1. Verifique se as duas máquinas virtuais foram criadas.

## Tarefa 4: verificar se os registros estão presentes na zona DNS

1. Na página inicial do Portal do Azure, selecione **Zonas DNS privadas**.

1. Nas zonas DNS privadas, escolha **contoso.com**.

1. Verifique se os registros de host (A) estão listados para as duas VMs, conforme mostrado:

    ![Zona DNS de contoso.com mostrando registros do host A registrados automaticamente.](../media/contoso_com-dns-zone.png)

1. Anote os nomes e os endereços IP das VMs.

### Conectar-se às VMs de teste usando RDP

1. Na página inicial do Portal do Azure, selecione **Máquinas Virtuais**.

1. Escolha **TestVM1**.

1. Em TestVM1, selecione **Conectar &gt; RDP** e baixe o arquivo RDP.

    ![TestVM1 com Conectar e RDP realçados.](../media/connect-to-am.png)

1. Salve o arquivo RDP em sua área de trabalho.

1. Siga as mesmas etapas para **TestVM2**

1. Conecte-se à TestVM1 usando o arquivo RDP e o nome de usuário **TestUser** e a senha fornecida durante a implantação.

1. Conecte-se à TestVM2 usando o arquivo RDP e o nome de usuário **TestUser** e a senha fornecida durante a implantação.

1. Nas duas VMs, em **Escolher configurações de privacidade para seu dispositivo**, escolha **Aceitar**.

1. Se solicitado, em **Redes**, selecione **Sim** para as duas VMs.

1. Em TestVM1, abra um prompt de comando e insira o comando `ipconfig /all`.

1. Verifique se o endereço IP é o mesmo que você anotou na zona DNS.

1. Insira o comando ping TestVM2.contoso.com.

1. Verifique se o FQDN é resolvido para o endereço IP que você anotou na zona DNS privada. O próprio ping atingirá o tempo limite por causa do Firewall do Windows habilitado nas VMs.

1. Como alternativa, você pode inserir o comando nslookup TestVM2.contoso.com e verificar se recebeu um registro de resolução de nome com êxito para VM2

## Estender seu aprendizado com o Copilot

O Copilot pode ajudar você a aprender a usar as ferramentas de script do Azure. O Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precisar de mais informações. Abra um navegador do Edge e escolha Copilot (canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar essas solicitações.
+ Qual é a diferença entre o DNS do Azure e o DNS Privado do Azure? Forneça exemplos de quando usar o DNS privado do Azure.
+ Qual é a finalidade do registro automático ao criar uma zona DNS do Azure?

## Saiba mais com treinamento individual

+ [Introdução ao DNS do Azure](https://learn.microsoft.com/training/modules/intro-to-azure-dns/). Este módulo explica o que o DNS do Azure faz, como ele funciona e quando você deve optar por usar o DNS do Azure como uma solução para atender às necessidades da sua organização.
+ [Hospede seu domínio no DNS do Azure](https://learn.microsoft.com/training/modules/host-domain-azure-dns/). Neste módulo, você criará uma zona DNS e registros DNS para mapear o domínio em um endereço IP. Você também testará para garantir que o nome de domínio resolve para o seu servidor Web.

## Principais aspectos a serem lembrados

Parabéns por concluir o laboratório. Aqui estão as principais lições desse laboratório. 

+ O DNS do Azure é um serviço de nuvem que permite hospedar e gerenciar domínios DNS (sistema de nomes de domínio), também conhecidos como zonas DNS. 
+ As zonas públicas do DNS do Azure hospedam dados de zona de nome de domínio para registros que você pretende que sejam resolvidos por qualquer host na Internet.
+ As zonas DNS privadas do Azure permitem que você configure um namespace de zona DNS privada para recursos privados do Azure.
+ Uma zona DNS é uma coleção de registros DNS. Registros DNS fornecem informações sobre o domínio.
