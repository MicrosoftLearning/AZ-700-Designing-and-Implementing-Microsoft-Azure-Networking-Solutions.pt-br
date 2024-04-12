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

**Observação:** há uma **[simulação interativa de laboratório](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Configure%20DNS%20settings%20in%20Azure)** disponível que permite que você clique neste laboratório no seu próprio ritmo. Você pode encontrar pequenas diferenças entre a simulação interativa e o laboratório hospedado, mas os principais conceitos e ideias que estão sendo demonstrados são os mesmos.

### Tempo estimado: 25 minutos

## Tarefa 1: criar uma zona DNS privada

1. Vá para [Portal do Azure](https://portal.azure.com/).

2. Na home page do Azure, na barra de pesquisa, insira o DNS e selecione **Zonas DNS privadas**.  
   ![Página inicial do Portal do Azure com a pesquisa de DNS.](../media/create-private-dns-zone.png)

3. Nas zonas DNS privadas, escolha **+ Criar**.

4. Use as informações da tabela abaixo para criar a zona DNS privada.

| **Tab**         | **Opção**                             | **Valor**            |
| --------------- | -------------------------------------- | -------------------- |
| Noções básicas          | Grupo de recursos                         | ContosoResourceGroup |
|                 | Nome                                   | Contoso.com          |
| Marcações            | Nenhuma alteração necessária                    |                      |
| Examinar + criar | Examine suas configurações e escolha Criar |                      |

5. Aguarde a conclusão da implantação e, então, escolha **Ir para o recurso**.

6. Verifique se a zona foi criada.

## Tarefa 2: vincular a sub-rede para registro automático

1. Em Contoso.com, sob **Configurações**, escolha **Links de rede virtual**.

2. Em Contoso.com | Links de rede virtual, escolha **+ Adicionar**.

![contoso.com | Links virtuais com a opção + Adicionar realçada.](../media/add-network-link-dns.png)

3. Use as informações da tabela abaixo para adicionar o link de rede virtual.

| **Opção**                          | **Valor**                               |
| ----------------------------------- | --------------------------------------- |
| Nome do link                           | CoreServicesVnetLink                    |
| Subscription                        | Nenhuma alteração necessária                     |
| Rede Virtual                     | CoreServicesVnet (ContosoResourceGroup) |
| Habilitar o registro automático            | Selecionadas                                |
| Examine suas configurações e escolha OK. |                                         |

4. Selecione **Atualizar**.

5. Verifique se CoreServicesVnetLink foi criada e se o registro automático está habilitado.

6. Repita as etapas dois a cinco para ManufacturingVnet usando as informações na tabela abaixo:

| **Opção**                          | **Valor**                                |
| ----------------------------------- | ---------------------------------------- |
| Nome do link                           | ManufacturingVnetLink                    |
| Subscription                        | Nenhuma alteração necessária                      |
| Rede Virtual                     | ManufacturingVnet (ContosoResourceGroup) |
| Habilitar o registro automático            | Selecionadas                                 |
| Examine suas configurações e escolha OK. |                                          |

7. Selecione **Atualizar**.

8. Verifique se ManufacturingVnetLink foi criada e se o registro automático está habilitado.

9. Repita as etapas dois a cinco para ResearchVnet usando as informações na tabela abaixo:

| **Opção**                          | **Valor**                           |
| ----------------------------------- | ----------------------------------- |
| Nome do link                           | ResearchVnetLink                    |
| Subscription                        | Nenhuma alteração necessária                 |
| Rede Virtual                     | ResearchVnet (ContosoResourceGroup) |
| Habilitar o registro automático            | Selecionadas                            |
| Examine suas configurações e escolha OK. |                                     |

10. Selecione **Atualizar**.

11. Verifique se ResearchVnetLink foi criada e se o registro automático está habilitado.

## Tarefa 3: criar máquinas virtuais para testar a configuração

Nesta seção, você criará duas VMs de teste para testar a configuração da zona DNS privada.

1. No portal do Azure, abra a sessão **PowerShell** no painel do **Cloud Shell**.

    > **Observação:** se esta for a primeira vez que o Cloud Shell é aberto, você será solicitado a criar uma conta de armazenamento. Selecione **Criar armazenamento**.

2. Na barra de ferramentas do painel do Cloud Shell, selecione o ícone **Carregar/baixar arquivos**, no menu suspenso, selecione **Carregar** e carregue os seguintes arquivos **azuredeploy.json** e **azuredeploy.parameters.json**, um a um, da pasta de origem **F:\Allfiles\Exercises\M01** para o diretório base do Cloud Shell.

3. Implante os seguintes modelos do ARM para criar as VMs necessárias para este exercício:

    >**Observação**: você será solicitado a fornecer uma senha de Administrador.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```
  
4. Após a conclusão da implantação, acesse a home page do portal do Azure e, depois, escolha **Máquinas Virtuais**.

5. Verifique se as duas máquinas virtuais foram criadas.

## Tarefa 4: verificar se os registros estão presentes na zona DNS

1. Na página inicial do Portal do Azure, selecione **Zonas DNS privadas**.

2. Nas zonas DNS privadas, escolha **contoso.com**.

3. Verifique se os registros de host (A) estão listados para as duas VMs, conforme mostrado:

![Zona DNS de contoso.com mostrando registros do host A registrados automaticamente.](../media/contoso_com-dns-zone.png)

4. Anote os nomes e os endereços IP das VMs.

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

1. Em TestVM1, abra um prompt de comando e insira o comando ipconfig/all.

1. Verifique se o endereço IP é o mesmo que você anotou na zona DNS.

1. Insira o comando ping TestVM2.contoso.com.

1. Verifique se o FQDN é resolvido para o endereço IP que você anotou na zona DNS privada. O próprio ping atingirá o tempo limite por causa do Firewall do Windows habilitado nas VMs.

1. Como alternativa, você pode inserir o comando nslookup TestVM2.contoso.com e verificar se recebeu um registro de resolução de nome com êxito para VM2

Parabéns! Você criou uma zona DNS privada, adicionou uma resolução de nome e um link de registro automático e testou a resolução de nomes em sua configuração.
