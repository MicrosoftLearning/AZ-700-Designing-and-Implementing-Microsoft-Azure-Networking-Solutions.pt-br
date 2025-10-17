---
Exercise:
  title: M07 - Unidade 5 Restrinja o acesso à rede a recursos de PaaS com pontos de extremidade de serviço de rede virtual
  module: Module 07 - Design and implement private access to Azure Services
---

# M07-Unidade 5 Restrinja o acesso à rede a recursos de PaaS com pontos de extremidade de serviço de rede virtual

## Cenário do exercício

Os pontos de extremidade de serviço de rede virtual permitem limitar o acesso à rede a alguns recursos de serviço do Azure para uma sub-rede da rede virtual. Você também pode remover o acesso à Internet para os recursos. Os pontos de extremidade de serviço fornecerão conexão direta de sua rede virtual a um serviço do Azure, permitindo que você use o espaço de endereço privado da sua rede virtual para acessar os serviços do Azure compatíveis. O tráfego destinado aos recursos do Azure por meio de pontos de extremidade de serviço sempre fica na rede de backbone do Microsoft Azure.

![Diagrama da arquitetura do ponto de extremidade de serviço.](../media/5-exercise-restrict-network-paas-resources-virtual-network-service-endpoints.png)

### Habilidades de trabalho

Neste exercício, você vai:

+ Tarefa 1: criar uma rede virtual
+ Tarefa 2: habilitar um ponto de extremidade de serviço
+ Tarefa 3: restringir o acesso à rede para uma sub-rede
+ Tarefa 4: adicionar outras regras de saída
+ Tarefa 5: permitir o acesso para conexões de RDP
+ Tarefa 6: restringir o acesso à rede para um recurso
+ Tarefa 7: criar um compartilhamento de arquivo na conta de armazenamento
+ Tarefa 8: restringir o acesso à rede para uma sub-rede
+ Tarefa 9: criar máquinas virtuais
+ Tarefa 10: confirmar o acesso à conta de armazenamento


### Simulações interativas de laboratório

>**Observação**: as simulações de laboratório fornecidas anteriormente foram desativadas.

### Tempo estimado: 35 minutos

## Tarefa 1: criar uma rede virtual

1. Faça logon no portal do Azure.

1. Na página inicial do portal do Azure, procure por `virtual network` e selecione **Redes virtuais** nos resultados.

1. Selecione **+** **Criar**.

1. Insira ou selecione as seguintes informações: ![Interface gráfica do usuário, texto, Descrição do aplicativo gerada automaticamente](../media/create-virtual-network.png)

   | **Configuração**    | **Valor**                                     |
   | -------------- | --------------------------------------------- |
   | Assinatura   | Selecionar sua assinatura                      |
   | Grupo de recursos | (Novo) myResourceGroup                         |
   | Nome           | CoreServicesVNet                              |
   | Localização       | Selecione **Leste dos EUA**                            |

1. Selecione a guia **Segurança** e insira os seguintes valores: ![interface gráfica do usuário, texto, aplicativo, Descrição do email gerada automaticamente](../media/ create-virtual-network-security.png)

   | **Configuração**             | **Valor** |
   | ----------------------- | --------- |
   | BastionHost             | Desabilitado  |
   | Proteção de Rede DDoS | Desabilitado  |
   | Firewall                | Desabilitado  |

1. Selecione a guia **Endereços IP** e insira os seguintes valores (selecione **padrão** para alterar o nome da sub-rede): ![interface gráfica do usuário, texto, aplicativo, Descrição do email gerada automaticamente](../media/create-virtual-network-ip.png)

   | **Configuração**          | **Valor**   |
   | -------------------- | ----------- |
   | Espaço de endereço        | 10.0.0.0/16 |
   | Nome da sub-rede          | Público      |
   | Intervalo de endereços da sub-rede | 10.0.0.0/24 |

1. Selecione **Examinar + criar**. Após a validação do recurso, selecione **Criar**.

## Tarefa 2: habilitar um ponto de extremidade de serviço

Pontos de extremidade de serviço são habilitados por serviço, por sub-rede. Criar uma sub-rede e habilitar um ponto de extremidade de serviço para a sub-rede.

1. Na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal, insira CoreServicesVNet. Quando CoreServicesVNet aparecer nos resultados da pesquisa, selecione-a.

1. Adicione uma sub-rede à rede virtual. Em **Configurações**, selecione **Sub-redes** e, em seguida, selecione **+ Sub-rede**, conforme mostrado na imagem a seguir: ![interface gráfica do usuário, Descrição do aplicativo gerada automaticamente](../media/create-subnet.png)

1. Em **Adicionar sub-rede**, selecione ou insira as seguintes informações:

   | **Configuração**                 | **Valor**                    |
   | --------------------------- | ---------------------------- |
   | Nome                        | Privado                      |
   | Intervalo de endereços               | 10.0.1.0/24                  |
   | Pontos de extremidade de serviço: serviços | Selecione **Microsoft.Storage** |

1. Selecione **Adicionar**.

Agora, você deve ter duas sub-redes configuradas:

![Interface gráfica do usuário, texto, aplicativo, Descrição do email gerada automaticamente](../media/configured-subnets.png)

## Tarefa 3: restringir o acesso à rede para uma sub-rede

Por padrão, todas as VMs em uma sub-rede podem se comunicar com todos os recursos. É possível limitar a comunicação com todos os recursos em uma sub-rede, criando um grupo de segurança de rede e o associando à sub-rede.

1. Na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal, insira **security group**. Quando a opção **Grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione-a.

1. Em Grupos de segurança de rede, selecione **+ Criar**.

1. Insira ou selecione as seguintes informações:

   | **Configuração**    | **Valor**                                                    |
   | -------------- | ------------------------------------------------------------ |
   | Assinatura   | Selecionar sua assinatura                                     |
   | Grupo de recursos | myResourceGroup                                              |
   | Nome           | ContosoPrivateNSG                                            |
   | Localização       | Selecione **Leste dos EUA**                                           |

1. selecione **Examinar + criar** e **Criar**:

1. Após a criação do grupo de segurança de rede ContosoPrivateNSG, escolha **Ir para o recurso**.

1. Em **Configurações**, escolha **Regras de segurança de saída**.

1. Selecione **+ Adicionar**.

1. Crie uma regra que permita a comunicação de saída para o serviço de Armazenamento do Azure. Insira ou selecione as seguintes informações: ![interface gráfica do usuário, Descrição do aplicativo gerada automaticamente](../media/add-outbound-security-rule.png)

   | **Configuração**             | **Valor**                 |
   | ----------------------- | ------------------------- |
   | Fonte                  | Selecione **Marca de Serviço**    |
   | Marca de serviço de origem      | Selecione **VirtualNetwork** |
   | Intervalos de portas de origem      | *                         |
   | Destino             | Selecione **Marca de Serviço**    |
   | Marca de serviço de destino | Selecione **Armazenamento**        |
   | Serviço                 | Personalizado                    |
   | Intervalos de portas de destino | *                         |
   | Protocolo                | Qualquer                       |
   | Ação                  | Permitir                     |
   | Prioridade                | 100                       |
   | Nome                    | `Allow-Storage-All`         |

1. Escolha **Adicionar**:

## Tarefa 4: adicionar outras regras de saída

Crie outra regra de segurança de saída que nega a comunicação com a Internet. Essa regra substitui uma regra padrão em todos os grupos de segurança de rede que permite a comunicação de saída à Internet.

1. Escolha **+ Adicionar** em **Regras de segurança de saída**.

1. Insira ou selecione as seguintes informações: ![interface gráfica do usuário, aplicativo, Descrição do email gerada automaticamente](../media/add-outbound-security-rule-deny.png)

   | **Configuração**             | **Valor**                 |
   | ----------------------- | ------------------------- |
   | Fonte                  | Selecione **Marca de Serviço**    |
   | Marca de serviço de origem      | Selecione **VirtualNetwork** |
   | Intervalos de portas de origem      | *                         |
   | Destino             | Selecione **Marca de Serviço**    |
   | Marca de serviço de destino | Selecione **Internet**       |
   | Serviço                 | Personalizado                    |
   | Intervalos de portas de destino | *                         |
   | Protocolo                | Qualquer                       |
   | Ação                  | Negar                      |
   | Prioridade                | 110                       |
   | Nome                    | `Deny-Internet-All`         |

1. Selecione **Adicionar**.

## Tarefa 5: permitir o acesso para conexões de RDP

Crie uma regra de segurança de entrada que permita o tráfego do protocolo RDP à sub-rede de qualquer lugar. A regra substitui uma regra de segurança padrão que nega todo o tráfego da Internet. Conexões de área de trabalho remota são permitidas para a sub-rede para que a conectividade possa ser testada em uma etapa posterior.

1. Em ContosoPrivateNSG | Regras de segurança de saída, em **Configurações**, escolha **Regras de segurança de entrada**.

1. Selecione **+ Adicionar**.

1. Em Adicionar regra de segurança de entrada, insira os seguintes valores: ![interface gráfica do usuário, Descrição do aplicativo gerada automaticamente](../media/add-inbound-security-rule.png)

   | **Configuração**             | **Valor**                 |
   | ----------------------- | ------------------------- |
   | Fonte                  | Qualquer                       |
   | Intervalos de portas de origem      | *                         |
   | Destino             | Qualquer                       |
   | Serviço                 | Personalizado                    |
   | Intervalos de portas de destino | 3389                      |
   | Protocolo                | Qualquer                       |
   | Ação                  | Permitir                     |
   | Prioridade                | 120                       |
   | Nome                    | `Allow-RDP-All`            |

1. Então selecione **Adicionar**.

> **Aviso**: a porta 3389 RDP é exposta à Internet. Isso só é recomendado para testes. Para ambientes de produção, recomendamos usar uma VPN ou uma conexão privada.

1. Em **Configurações**, selecione **Sub-redes**.

1. Selecione **+ Associar**.

1. Em **Associar sub-rede**, escolha **Rede virtual** e, depois, escolha **CoreServicesVNet** em **Escolher uma rede virtual**.

1. Em **Escolher sub-rede**, selecione **Privada**e selecione **OK**.

## Tarefa 6: restringir o acesso à rede para um recurso

As etapas necessárias para restringir o acesso de rede a recursos criados por meio de serviços do Azure habilitados para pontos de extremidade do serviço variam de acordo com os serviços. Confira a documentação de serviços individuais para obter as etapas específicas para cada serviço. O restante deste exercício inclui etapas para restringir o acesso de rede para uma conta de Armazenamento do Microsoft Azure, como exemplo.

1. No portal do Azure, pesquise e selecione `Storage accounts`.

1. Selecione **Criar**.

1. Insira ou selecione as informações a seguir e aceite os padrões restantes:

   | **Configuração**    | **Valor**                                                    |
   | -------------- | ------------------------------------------------------------ |
   | Assinatura   | Selecionar sua assinatura                                     |
   | Grupo de recursos | myResourceGroup                                              |
   | Nome           | Insira contosostoragexx (em que xx são suas iniciais para torná-la exclusiva) |
   | Serviço principal | Arquivos do Azure                                                |
   | Desempenho    | Standard StorageV2 (uso geral v2)                      |
   | Localização       | Selecione Leste dos EUA                                               |
   | Replicação    | Armazenamento com redundância local (LRS)                              |

1. Selecione **Examinar** e **Criar**.

1. Após a implantação da conta de armazenamento, selecione **Acessar recurso**. 

## Tarefa 7: criar um compartilhamento de arquivos na conta de armazenamento

1. No menu Conta de armazenamento, no painel **Armazenamento de dados**, selecione **Compartilhamentos de arquivos**.

1. Selecione **+Compartilhamento de arquivos**. 

1. Em **Nome**, insira **marketing** e selecione **Avançar: Backup**.

1. Desmarque **Ativar backup**, conforme mostrado na imagem a seguir: 

1. Selecione **Examinar + criar**. Após a validação do recurso, selecione **Criar**.

## Tarefa 8: restringir o acesso à rede para uma sub-rede

Por padrão, as contas de armazenamento aceitam conexões de clientes em qualquer rede, incluindo a Internet. Negar acesso à rede da Internet, e a todas as outras sub-redes em todas as redes virtuais, exceto à sub-rede Privada na rede virtual CoreServicesVNet.

1. Em **Segurança + rede** da conta de armazenamento, escolha **Rede**.

1. Selecione **Gerenciar** na seção **Acesso à rede pública**.

1 Na seção **Escopo de acesso público**, selecione **Ativado nas redes selecionadas**.

1. Selecione **+ Adicionar rede virtual existente** e, em seguida, **Adicionar rede virtual existente**. 

   | **Configuração**      | **Valor**                    |
   | ---------------- | ---------------------------- |
   | Subscription     | Selecione sua assinatura.    |
   | Redes virtuais | **CoreServicesVNet** |
   | Sub-redes          | **Particular**.          |

1. Selecione **Adicionar** e, em seguida, **Salvar**.

1. Em **Segurança e Rede** da conta de armazenamento, escolha **Chaves de acesso**.

1. Use **Exibir** para o valor **Key1**. Copie esse valor, pois você precisará dele mais tarde. 

## Tarefa 9: criar máquinas virtuais

Para testar o acesso à rede para uma conta de armazenamento, implante uma VM para cada sub-rede.

1. No portal do Azure, clique no ícone do Cloud Shell (canto superior direito). Se necessário, configure o shell.  
    + Selecione **PowerShell**.
    + Selecione **Nenhuma Conta de Armazenamento necessária** e sua **Assinatura** e, em seguida, selecione **Aplicar**.
    + Aguarde até que o terminal seja criado e um prompt seja exibido. 

1. Na barra de ferramentas do painel do Cloud Shell, clique no ícone **Gerenciar arquivos**. No menu suspenso, clique em **Upload** e faça upload dos seguintes arquivos **VMs.json** e **VMs.parameters.json** no diretório base do Cloud Shell. Este Módulo 07, Exercício 05. 

1. Implante os seguintes modelos do ARM para criar as VMs necessárias para este exercício:

   >**Observação**: você será solicitado a fornecer uma senha de Administrador.

   ```powershell
   $RGName = "myResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile VMs.json -TemplateParameterFile VMs.parameters.json
   ```
  
1. Após a conclusão da implantação, acesse a home page do portal do Azure e, depois, escolha **Máquinas Virtuais**.

## Tarefa 10: confirmar o acesso à conta de armazenamento

1. No portal, pesquise e selecione a máquina virtual **ContosoPrivate**.

1. Selecione **Conectar**, **Conectar** e, em seguida, **Baixar arquivo RDP**. Se solicitado, confirme o download.

1. Na pasta **Downloads**, abra o arquivo ContosoPrivate.rdp.

1. Selecione **Conectar** e forneça a senha da máquina virtual. Selecione **Ok** e indique **Sim** para o aviso de certificado. 

1. Use o PowerShell para criar um compartilhamento de arquivos. Substitua <storage-account-key1-value>, <storage-account-name> (ou seja, contosostoragexx) pelos valores quando você criou a conta de armazenamento. 
    ```powershell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key1-value>" -AsPlainText -Force

    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey

    New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\marketing" -Credential $credential

    ```
1. Confirme se a VM não tem conectividade de saída. Você não recebe nenhuma resposta, pois o grupo de segurança de rede associado à sub-rede Privada não permite acesso de saída para a Internet.

    ```ping bing.com```

1. Feche a sessão de área de trabalho remota da VM ContosoPrivate.

### Confirmar que o acesso é negado para a conta de armazenamento

1. Retorne ao portal do Azure.

1. Navegue até sua conta de armazenamento, selecione **Compartilhamentos de arquivos** e selecione o compartilhamento de arquivos de **marketing**. 

1. Selecione **Procurar** e observe o erro de acesso negado. Seu erro pode parecer diferente.  Acesso negado porque o computador não está na sub-rede Privada da rede virtual CoreServicesVNet.

    ![Interface gráfica do usuário, texto, aplicativo, Descrição do email gerada automaticamente](../media/no-access.png)

## Limpar os recursos

>**Observação**: lembre-se de remover todos os recursos recém-criados do Azure que você não usa mais. Remover recursos não utilizados garante que você não veja encargos inesperados.

1. No portal do Azure, abra a sessão **PowerShell** no painel do **Cloud Shell**.

1. Exclua todos os grupos de recursos criados em todos os laboratórios deste módulo executando o seguinte comando:

   ```powershell
   Remove-AzResourceGroup -Name 'myResourceGroup' -Force -AsJob
   ```

   >**Observação**: o comando é executado de maneira assíncrona (conforme determinado pelo parâmetro -AsJob), portanto, embora você possa executar outro comando do PowerShell imediatamente após na mesma sessão do PowerShell, levará alguns minutos antes dos grupos de recursos serem de fato removidos.

## Estender seu aprendizado com o Copilot

O Copilot pode ajudar você a aprender a usar as ferramentas de script do Azure. O Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precisar de mais informações. Abra um navegador do Edge e escolha Copilot (canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar essas solicitações.
+ Qual é a diferença entre pontos de extremidade de serviço do Azure e pontos de extremidade privados?
+ Quais serviços do Azure podem usar pontos de extremidade de serviço?
+ Quais são as etapas para restringir o acesso ao Armazenamento do Azure usando pontos de extremidade de serviço?

## Saiba mais com treinamento individual

+ [Proteger e isolar o acesso aos recursos do Azure usando grupos de segurança de rede e pontos de extremidade de serviço](https://learn.microsoft.com/training/modules/secure-and-isolate-with-nsg-and-service-endpoints/). Neste módulo, você aprenderá a usar pontos de extremidade do serviço de rede virtual para controlar o tráfego de rede entre os serviços do Azure.

## Principais aspectos a serem lembrados
+ Pontos de extremidade de serviço de rede virtual estendem seu espaço de endereço privado no Azure ao fornecer uma conexão direta com os serviços do Azure.
+ Os pontos de extremidade de serviço permitem que você restrinja o acesso a seus recursos do Azure somente à sua rede virtual. O tráfego do serviço permanecerá no backbone do Azure e não é enviado para a Internet.
+ Os pontos de extremidade de serviço do Azure estão disponíveis para muitos serviços, como: Armazenamento do Azure, Banco de Dados SQL do Azure e Azure Cosmos DB.
+ Os pontos de extremidade de serviço de rede virtual não são, por padrão, acessíveis em redes locais. Para acessar os recursos em uma rede local, use IPs NAT.
