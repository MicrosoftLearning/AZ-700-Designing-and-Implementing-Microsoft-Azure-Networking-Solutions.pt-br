---
Exercise:
  title: M06 - Unidade 9 Proteger seu hub virtual usando o Gerenciador de Firewall do Azure
  module: Module 06 - Design and implement network security
---


# M06-Unidade 9 Proteger seu hub virtual usando o Gerenciador de Firewall do Azure

## Cenário do exercício

Neste exercício, você criará a rede virtual spoke, criará um hub virtual seguro, conectará as redes virtuais hub e spoke e roteará o tráfego para seu hub. Agora, você implantará os servidores de carga de trabalho, criará uma política de firewall, protegerá seu hub e, por fim, testará o firewall.

![Diagrama da arquitetura de rede virtual com um hub seguro.](../media/9-exercise-secure-your-virtual-hub-using-azure-firewall-manager.png)

### Simulações interativas de laboratório

**Observação**: as simulações de laboratório fornecidas anteriormente foram desativadas.


## Criar uma arquitetura de hub e spoke

Nesta parte do exercício, você criará as redes virtuais spoke e sub-redes em que colocará os servidores da carga de trabalho. Você criará o hub virtual seguro e conectará as redes virtuais hub e spoke.

### Habilidades de trabalho

Neste exercício, você vai:

+ Tarefa 1: Criar duas redes virtuais spoke e sub-redes
+ Tarefa 2: Criar o hub virtual seguro
+ Tarefa 3: Conectar as redes virtuais hub e spoke
+ Tarefa 4: Implantar os servidores
+ Tarefa 5: Criar uma política de firewall e proteger seu hub
+ Tarefa 6: Associar a política de firewall
+ Tarefa 7: Rotear o tráfego para o hub
+ Tarefa 8: Testar a regra do aplicativo
+ Tarefa 9: Testar a regra de rede
+ Tarefa 10: Limpar os recursos

### Tempo estimado: 35 minutos

## Tarefa 1: Criar duas redes virtuais spoke e sub-redes

Nesta tarefa, você criará as duas redes virtuais spoke, cada uma contendo uma sub-rede que hospedará seus servidores de carga de trabalho.

1. No portal do Azure, pesquise e selecione `Virtual Networks`.

1. Selecione **Criar**.

1. Em **Grupo de recursos,** selecione **Criar novo** e insira `fw-manager-rg` como o nome e selecione **OK**.

1. Em **Nome da rede virtual**, insira `Spoke-01`.

1. Em **Região**, selecione sua região.

1. Selecione **Avançar**. Examine, mas não faça alterações na guia **Segurança**. 

1. Selecione **Avançar** e vá para a guia **Endereços IP**.

1. Selecione **Excluir espaço de endereço** e, em seguida, **Adicionar espaço de endereço IPv4**. 

1. Verifique se o espaço de endereços IP é **10.0.0.0/16**.

1. Selecione **Adicionar uma sub-rede**. 

1. Altere o **Nome** da sub-rede para `Workload-01-SN`.

1. Altere o **Endereço inicial** para `10.0.1.0`.

1. Selecione **Adicionar**.

1. Selecione **Examinar + criar**.

1. Selecione **Criar**.

Repita as etapas 1 a 14 acima para criar outra rede e sub-rede virtuais semelhantes usando as informações a seguir. Você não precisa esperar que a primeira rede virtual conclua a implantação. 

+ Grupo de Recursos: **fw-manager-rg** (selecionar existente)
+ Nome da Rede Virtual: `Spoke-02`
+ Espaço de endereço: **10.1.0.0/16** – (excluir quaisquer outros espaços de endereço listados)
+ Nome da sub-rede: `Workload-02-SN`
+ Endereço inicial da sub-rede: `10.1.1.0`

## Tarefa 2: Criar o hub virtual seguro

Nesta tarefa, você criará seu hub virtual seguro usando o Gerenciador de Firewall.

1. No portal, pesquise `firewall manager` e selecione **Gerenciador de Firewall com palavra-chave Segurança de Rede**.
   
1. Na folha **Proteger seus recursos**, selecione **Hubs Virtuais**.

1. Selecione **Criar um hub virtual seguro**.

1. Em **Grupo de recursos**, selecione **fw-manager-rg**.

1. Em **Região**, selecione sua região.

1. Para o **Nome do hub virtual seguro**, insira `Hub-01`.

1. Em **Espaço de endereço do hub**, insira `10.2.0.0/16`.

1. Verifique se **Novo vWAN** está selecionado

1. Em **Nome da WAN Virtual**, insira `Vwan-01`.

1. Selecione **Avançar: Firewall do Azure**. Examine, mas não faça nenhuma alteração. 

1. Selecione **Avançar: Provedor de Parceiro de Segurança**. Examine, mas não faça nenhuma alteração. 

1. Selecione **Avançar: Revisar + criar**.

1. Selecione **Criar**.

    > Observação: Pode levar até 30 minutos para implantar.

1. Aguarde até que a implantação seja concluída. 

1. No portal, pesquise `firewall manager` e selecione **Gerenciador de Firewall com palavra-chave Segurança de Rede**.

1. Na página **Gerenciador de Firewall**, selecione **Hubs virtuais**.

1. Selecione **Hub-01**.

1. Selecione **Firewall do Azure** e **Configuração de IP público**.

1. Anote o endereço IP público (por exemplo, **172.191.79.203**), que será usado depois.

## Tarefa 3: Conectar as redes virtuais hub e spoke

Nesta tarefa, você conectará as redes virtuais hub e spoke. Isso é normalmente conhecido como emparelhamento.

1. No portal, pesquise e selecione a WAN virtual `Vwan-01`.

1. Em **Conectividade**, selecione **Conexões de rede virtual**.

1. Selecione **Adicionar conexão**.

1. Em **Nome da conexão**, insira `hub-spoke-01`.

1. Para **Hubs**, selecione **Hub-01**.

1. Em **Grupo de recursos**, selecione **fw-manager-rg**.

1. Para **Rede virtual**, selecione **Spoke-01**.

1. Selecione **Criar**.

1. Repita as etapas de 4 a 9 acima para criar outra conexão semelhante, mas usando o nome da conexão `hub-spoke-02` para conectar à rede virtual **Spoke-02**.

1. **Atualize** a página de conexões de rede virtual e verifique se você tem duas redes virtuais, Spoke-01 e Spoke-02.
   
## Tarefa 4: Implantar os servidores

1. No portal do Azure, clique no ícone do Cloud Shell (canto superior direito). Se necessário, configure o shell.  
    + Selecione **PowerShell**.
    + Selecione **Nenhuma Conta de Armazenamento necessária** e sua **Assinatura** e, em seguida, selecione **Aplicar**.
    + Aguarde até que o terminal seja criado e um prompt seja exibido. 

1. Na barra de ferramentas do painel do Cloud Shell, selecione o ícone **Gerenciar arquivos**. No menu suspenso, selecione **Upload** e faça o upload dos seguintes arquivos **FirewallManager.json** e **FirewallManager.parameters.json** no diretório base do Cloud Shell.

    > **Observação:** se você estiver trabalhando em sua própria assinatura, os [arquivos de modelo](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/tree/master/Allfiles/Exercises) estarão disponíveis no repositório do GitHub Lab.

1. Implante os seguintes modelos do ARM para criar a VM necessária para este exercício:

   >**Observação**: você será solicitado a fornecer uma senha de Administrador. **Você precisará dessa senha em uma etapa posterior.**

   ```powershell
   $RGName = "fw-manager-rg"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile FirewallManager.json -TemplateParameterFile FirewallManager.parameters.json
   ```
  
1. Após a conclusão da implantação, acesse a home page do portal do Azure e, depois, escolha **Máquinas Virtuais**.

1. Na página **Visão geral** do **Srv-workload-01**, no painel à direita, na seção **Rede**, anote o **Endereço IP privado** (por exemplo, **10.0.1.4**).

1. Na página **Visão geral** do **Srv-workload-02**, no painel à direita, na seção **Rede**, anote o **Endereço IP privado** (por exemplo, **10.1.1.4**).

## Tarefa 5: Criar uma política de firewall e proteger seu hub

Nesta tarefa, você criará a política de firewall para proteger seu hub. Uma política de firewall define coleções de regras para direcionar o tráfego em um ou mais Hubs virtuais seguros.

1. No portal, pesquise `firewall manager` e selecione **Gerenciador de Firewall com palavra-chave Segurança de Rede**.

1. Na folha **Gerenciador de Firewall**, selecione **Políticas de Firewall do Azure**.

1. Selecione **Criar**.

1. Em **Grupo de recursos**, selecione **fw-manager-rg**.

5. Em **Detalhes da política**, para o **Nome**, insira `Policy-01`.

1. Em **Região**, selecione sua região.

1. Em **Camada da política**, selecione **Standard**.

1. Selecione **Próximo: Configurações de DNS**. Examine, mas não faça nenhuma alteração. 

1. Selecione **Avançar: Inspeção TLS**. Examine, mas não faça nenhuma alteração. 

1. Selecione **Avançar: Regras**.

1. Na guia **Regras**, selecione **Adicionar a coleção de regras**.

1. Na página **Adicionar uma coleção de regras**, em **Nome**, insira `App-RC-01`.

1. Para **Tipo de coleção de regras**, selecione **Aplicativo**.

1. Para **Prioridade**, insira **100**.

1. Verifique se a **Ação de coleção de regras** é **Permitir**.

1. Em **Regras**, em **Nome**, insira `Allow-msft`.

1. Para o **Tipo de origem**, selecione **Endereço IP**.

1. Em **Origem**, insira *.

1. Para **Protocolo**, insira `http,https`.

1. Verifique se o **Tipo de destino** é **FQDN**.

1. Para **Destino**, insira `*.microsoft.com`.

1. Selecione **Adicionar**.

1. Para adicionar uma regra DNAT para que você possa conectar uma área de trabalho remota à VM Srv-workload-01, selecione **Adicionar uma coleção de regras**.

1. Para **Nome**, insira `dnat-rdp`.

1. Em **Tipo de coleção de regras**, selecione **DNAT**.

1. Para **Prioridade**, insira **100**.

1. Em **Regras**, em **Nome**, insira `Allow-rdp`.

1. Para o **Tipo de origem**, selecione **Endereço IP**.

1. Em **Origem**, insira *.

1. Em **Protocolo**, selecione **TCP**.

1. Para **Portas de Destino**, insira `3389`.

1. Para **IP de destino**, insira o endereço IP do hub virtual do firewall que você anotou anteriormente (por exemplo, **51.143.226.18**).

1. Em **Tipo convertido**, selecione **Endereço IP**.

1. Para **Endereço traduzido**, insira o endereço IP privado para **Srv-workload-01** que você anotou anteriormente (por exemplo, **10.0.1.4**).

1. Para **Porta traduzida**, insira **3389**.

1. Selecione **Adicionar**.

1. Para adicionar uma Regra de rede para que você possa conectar uma área de trabalho remota de Srv-workload-01 à VM Srv-workload-02, selecione **Adicionar uma coleção de regras**.

1. Para **Nome**, insira `vnet-rdp`.

1. Para **Tipo de coleção de regras**, selecione **Rede**.

1. Para **Prioridade**, insira **100**.

1. Em **Ação de coleção de regras**, selecione **Permitir**.

1. Em **Regras**, em **Nome**, insira `Allow-vnet`.

1. Para o **Tipo de origem**, selecione **Endereço IP**.

1. Em **Origem**, insira *.

1. Em **Protocolo**, selecione **TCP**.

1. Quanto às **Portas de destino**, insira **3389**.

1. Para **Tipo de Destino**, selecione **Endereço IP**.

1. Para **Destino**, insira o endereço IP privado para **Srv-workload-02** que você anotou anteriormente (por exemplo, **10.1.0.4**).

1. Selecione **Adicionar**.

1. Agora você deve ter três coleções de regras listadas.

1. Selecione **Examinar + criar**.

1. Selecione **Criar**.

## Tarefa 6: Associar a política de firewall

Nesta tarefa, você associará a política de firewall ao hub virtual.

1. No portal, pesquise e selecione `Hub-01`.

1. Na folha **Configurações**, selecione **Provedores de segurança**

1. Marque a caixa de seleção **Adicionar Política**.

1. Selecione **Política-01** e **Salvar**.

1. Marque a caixa de seleção para **Hub-01**.

1. Selecione **Adicionar**.

1. Quando a política tiver sido associada, selecione **Atualizar**. A associação deve ser exibida.

## Tarefa 7: Rotear o tráfego para o hub

Nesta tarefa, você deverá garantir que o tráfego de rede seja roteado por meio do firewall.

1. No portal, pesquise e selecione **Vwan-01**.

1. Na folha **Conectividade**, selecione **Hubs** e, em seguida, **Hub-01**.
   
1. Na folha **Segurança**, selecione **Firewall do Azure e Gerenciador de Firewall**, selecione **Hub-01** e, em seguida, **Configuração de segurança**.

1. Em **Tráfego da Internet**, selecione **Firewall do Azure**.

1. Em **Tráfego privado**, selecione **Enviar por meio do Firewall do Azure**.

1. Selecione **Salvar** e clique em **OK** para confirmar sua escolha.

1. Essa ação levará alguns minutos para ser concluída.

1. Após a conclusão da configuração, garanta que **TRÁFEGO DE INTERNET** e **TRÁFEGO PRIVADO** indica **Protegido pelo Firewall do Azure** para conexões hub-spoke.

## Tarefa 8: Testar a regra do aplicativo

Nesta parte do exercício, você conectará uma área de trabalho remota ao endereço IP público do firewall, que é NATed para SRV-Workload-01. Você usará um navegador da Web para testar a regra de aplicativo e conectar uma área de trabalho remota a Srv-Workload-02 para testar a regra de rede.

Nesta tarefa, você testará a regra de aplicativo para confirmar que ela funciona conforme o esperado.

1. Abra **Conexão de Área de Trabalho Remota** em seu computador.

1. Na caixa **Computador**, insira o **endereço IP público do firewall** (por exemplo, **51.143.226.18**).

1. Selecione **Mostrar Opções**.

1. Na caixa **Nome de usuário**, digite **TestUser**.

1. Selecione **Conectar**.

   ![Conexão RDP para srv-workload-01](../media/rdp-srv-workload-01.png)

1. Na caixa de diálogo **Inserir suas credenciais**, entre na máquina virtual do servidor **Srv-workload-01** usando a senha fornecida durante a implantação.

1. Selecione **OK**.

1. Selecione **Sim** na mensagem do certificado.

1. Abra o Internet Explorer e selecione **OK** na caixa de diálogo **Configurar Internet Explorer 11**.

1. Navegue até `https://www.microsoft.com`

1. Na caixa de diálogo **Alerta de Segurança**, selecione **OK**.

1. Selecione **Fechar** nos alertas de segurança do Internet Explorer que podem aparecer.

1. Você deve ver a página inicial da Microsoft.

    ![Sessão RDP navegando em microsoft.com](../media/microsoft-home-page.png)

1. Navegue para **https://** **<www.google.com>**.

1. Você deve ser bloqueado pelo firewall.

    ![Navegador da sessão RDP bloqueado em google.com](../media/google-home-page-fail.png)

1. Portanto, você verificou que pode se conectar a um FQDN permitido, mas que está bloqueado em todos os outros.

## Tarefa 9: Testar a regra de rede

Nesta tarefa, você testará a regra de rede para confirmar que ela funciona conforme o esperado.

1. Embora ainda esteja conectado à sessão RDP **Srv-workload-01**, nesse computador remoto, abra **Conexão de Área de Trabalho Remota**.

1. Na caixa **Computador**, insira o **endereço IP privado** de **Srv-workload-02** (por exemplo, **10.1.1.4**).

1. Na caixa de diálogo **Digite suas credenciais**, entre no servidor **Srv-workload-02** usando o nome de usuário **TestUser** e a senha fornecida durante a implantação.

1. Selecione **OK**.

1. Selecione **Sim** na mensagem do certificado.

   ![Sessão RDP de srv-workload-01 para outra sessão RDP em srv-workload-02](../media/rdp-srv-workload-02-from-srv-workload-01.png)

1. Agora você verificou que a regra de rede do firewall está funcionando, pois conectou uma área de trabalho remota de um servidor a outro servidor localizado em outra rede virtual.

1. Feche as duas sessões RDP para desconectá-las.

## Tarefa 10: Limpar os recursos

>**Observação**: lembre-se de remover todos os recursos do Azure recém-criados que você não usa mais. Remover recursos não utilizados garante que você não veja encargos inesperados.

1. No portal do Azure, abra a sessão **PowerShell** no painel do **Cloud Shell**.

1. Exclua todos os grupos de recursos criados em todos os laboratórios deste módulo executando o seguinte comando:

   ```powershell
   Remove-AzResourceGroup -Name 'fw-manager-rg' -Force -AsJob
   ```

   >**Observação**: o comando é executado de modo assíncrono (conforme determinado pelo parâmetro -AsJob), portanto, embora você possa executar outro comando do PowerShell imediatamente depois na mesma sessão do PowerShell, levará alguns minutos antes de os grupos de recursos serem de fato removidos.
