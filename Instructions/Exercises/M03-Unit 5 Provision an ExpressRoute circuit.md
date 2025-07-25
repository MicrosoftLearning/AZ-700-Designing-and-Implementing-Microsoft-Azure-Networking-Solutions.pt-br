---
Exercise:
  title: M03 - Unidade 5 Provisionar um circuito do ExpressRoute
  module: Module 03 - Design and implement Azure ExpressRoute
---
# M03-Unidade 5 Provisionar um circuito do ExpressRoute

## Cenário do exercício

Neste exercício, você criará um circuito do ExpressRoute usando o portal do Azure e o modelo de implantação do Azure Resource Manager.

### Simulações interativas de laboratório

>**Observação**: as simulações de laboratório fornecidas anteriormente foram desativadas.

### Tempo estimado: 15 minutos

![Diagrama do layout do circuito do ExpressRoute para exercício](../media/5-exercise-provision-expressroute-circuit.png)

### Habilidades de trabalho

Neste exercício, você vai:

+ Tarefa 1: criar e provisionar um circuito do ExpressRoute
+ Tarefa 2: recuperar sua chave de serviço
+ Tarefa 3: desprovisionar um circuito do ExpressRoute


## Tarefa 1: criar e provisionar um circuito do ExpressRoute

1. Em um navegador, acesse o [Portal do Azure](https://portal.azure.com/) e entre com sua conta do Azure.

   >**Importante**: o circuito de ExpressRoute será cobrado a partir do momento em que uma chave de serviço for emitida. Execute esta operação quando o provedor de conectividade estiver pronto para provisionar o circuito.

1. No menu do portal do Azure, pesquise e selecione **Circuitos do ExpressRoute**.

1. Na página **Criar ExpressRoute**, informe o **Grupo de recursos** como `ExpressRouteResourceGroup`. Em seguida, selecione **Resiliência Padrão** em **Resiliência**.

1. Em ***Detalhes do circuito**, especifique a Região correta (**Leste dos EUA 2**), o Nome do circuito (**TestERCircuit**), o Local de emparelhamento (**Seattle**), o Provedor (**Equinix**), a Largura de banda (**50 Mbps**), o nível de SKU (**Padrão**) e o modelo de cobrança de medição de dados (**limitado**).

1. Selecione **Examinar + criar**.

1. Confirme se a configuração do ExpressRoute é aprovada na validação e selecione **Criar**.

![Portal do Azure – guia Criar configuração do ExpressRoute](../media/expressroute-create-configuration2.png)

+ O Tipo de Porta determina se você está se conectando a um provedor de serviços ou diretamente à rede global da Microsoft em uma localização de emparelhamento.
+ Criar ou importar do clássico determina se um circuito está sendo criado ou se você está migrando um circuito clássico para o Azure Resource Manager.
+ Provedor é o provedor de serviços de Internet de quem você solicitará o serviço.
+ Local de Emparelhamento é o local físico em que você está realizando o emparelhamento com a Microsoft.

> [!Important]
>
> O Local de Emparelhamento indica o [local físico](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-locations) em que você está realizando o emparelhamento com a Microsoft. Isso não tem vínculo à propriedade "Local", que se refere à posição geográfica na qual o Provedor de Recursos de Rede do Azure está localizado. Embora eles não estejam relacionados, é uma boa prática escolher um provedor de recursos de rede geograficamente próximo do Local de Emparelhamento do circuito.

+ **SKU** determina se um complemento Local, Standard ou Premium do ExpressRoute está habilitado. Você pode especificar **Local** para obter o SKU local, **Standard** para obter o SKU Standard ou **Premium** para obter o complemento premium. Você pode alterar o SKU para habilitar o complemento premium.

   >**Importante**: não é possível alterar o SKU de Standard/Premium para Local.

+ O **Modelo de cobrança** determina o tipo de cobrança. Você pode especificar **Limitado** para um plano de dados limitado e **Ilimitado** para um plano de dados ilimitado. Você pode alterar o tipo de cobrança de **Limitada** para **Ilimitada**.

> [!Important]
>
> Você não pode alterar o tipo de Ilimitada para Limitada.

+ **Permitir operação clássica** permitirá que redes virtuais clássicas sejam vinculadas ao circuito.

## Tarefa 2: recuperar sua chave de serviço

1. Você pode ver todos os circuitos criados selecionando **Todos os serviços &gt; Rede &gt; Circuitos do ExpressRoute**.

   ![Portal do Azure – menu Criar recurso do ExpressRoute](../media/expressroute-circuit-menu.png)

1. Todos os circuitos do ExpressRoute criados na assinatura serão mostrados aqui.

   ![Portal do Azure – mostrar circuitos do ExpressRoute](../media/expressroute-circuit-list.png)

1. A página Circuito exibe as propriedades do circuito. A chave de serviço é exibida no campo Chave de serviço. O provedor de serviços precisará da chave de serviço para concluir o processo de provisionamento. A chave de serviço é específica para o circuito. **Você deve enviar a chave de serviço para seu provedor de conectividade para provisionamento.**

   ![Portal do Azure – propriedades do Circuito do ExpressRoute mostrando a chave de serviço](../media/expressroute-circuit-overview.png)

1. Nessa página, **Status do provedor** fornece o estado atual de provisionamento no lado do provedor de serviços. **Status de circuito** fornece o estado no lado da Microsoft.

1. Quando você cria um novo circuito do ExpressRoute, ele está no seguinte estado:

   + Status do provedor: não provisionado
   + Status do circuito: Enabled

   + O circuito assumirá o seguinte estado quando o provedor de conectividade estiver habilitando-o para você:
     + Status do provedor: provisionando
     + Status do circuito: habilitado
   + Para usar o circuito do ExpressRoute, ele precisa estar no seguinte estado:
     + Status do provedor: provisionado
     + Status do circuito: habilitado
   + Verifique periodicamente o status do provisionamento e o estado do status do circuito.

![Portal do Azure – propriedades do circuito do ExpressRoute mostrando que o status agora está provisionado](../media/provisioned.png)

Parabéns! Você criou um circuito do ExpressRoute e localizou a chave de serviço, da qual precisará para concluir o provisionamento do circuito.

## Tarefa 3: desprovisionar um circuito do ExpressRoute

Se o estado de provisionamento do provedor de serviço de circuito de ExpressRoute for **Provisionando** ou **Provisionado**, você deverá trabalhar com seu provedor de serviços para que ele desprovisione o circuito. A Microsoft pode continuar a reservar recursos e cobrar você até que o provedor de serviços conclua o desprovisionamento do circuito e nos notifique.

   >**Observação**: você precisa desvincular todas as redes virtuais do circuito de ExpressRoute antes do desprovisionamento. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito. Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como Não provisionado), exclua o circuito. Isso interrompe a cobrança pelo circuito.

## Limpar os recursos

Você pode excluir seu circuito do ExpressRoute selecionando o ícone **Excluir**. Verifique se o status do provedor está definido como Não provisionado antes de continuar.

![Portal do Azure – excluir um circuito do ExpressRoute](../media/expressroute-circuit-delete.png)

   >**Observação**: lembre-se de remover todos os recursos do Azure recém-criados que você não usa mais. Remover recursos não utilizados garante que você não veja encargos inesperados.

1. No portal do Azure, abra a sessão **PowerShell** no painel do **Cloud Shell**.

1. Exclua todos os grupos de recursos criados em todos os laboratórios deste módulo executando o seguinte comando:

   ```powershell
   Remove-AzResourceGroup -Name 'ContosoResourceGroup' -Force -AsJob
   Remove-AzResourceGroup -Name 'ExpressRouteResourceGroup' -Force -AsJob
   ```

   >**Observação**: o comando é executado de maneira assíncrona (conforme determinado pelo parâmetro -AsJob), portanto, embora você possa executar outro comando do PowerShell imediatamente após na mesma sessão do PowerShell, levará alguns minutos antes dos grupos de recursos serem de fato removidos.

## Estender seu aprendizado com o Copilot

O Copilot pode ajudar você a aprender a usar as ferramentas de script do Azure. O Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precisar de mais informações. Abra um navegador do Edge e escolha Copilot (canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar essas solicitações.
+ Quais provedores de serviços estão disponíveis para o Azure ExpressRoute?
+ Quais são os problemas de configuração mais comuns com o Azure ExpressRoute? O que devo fazer se tiver esse problema?

## Saiba mais com treinamento individual

+ [Introdução ao Azure ExpressRoute](https://learn.microsoft.com/training/modules/intro-to-azure-expressroute/). Neste módulo, você aprenderá o que é o Azure ExpressRoute e a funcionalidade que fornece.
+ [Projetar e implementar o ExpressRoute](https://learn.microsoft.com/training/modules/design-implement-azure-expressroute/). Neste módulo, você aprenderá a projetar e a implementar o Azure ExpressRoute, o Alcance Global do ExpressRoute e o ExpressRoute FastPath.

## Principais aspectos a serem lembrados

Parabéns por concluir o laboratório. Aqui estão as principais lições desse laboratório. 
+ O Azure ExpressRoute permite que uma organização conecte suas redes locais diretamente às nuvens do Microsoft Azure e do Microsoft 365. O Azure ExpressRoute usa uma conexão de alta largura de banda dedicada fornecida por um parceiro da Microsoft.
+ A Microsoft garante um mínimo de 99,95% de disponibilidade para conexões dedicadas do ExpressRoute. A conexão é privada e trafega por uma linha dedicada, terceiros não podem interceptar o tráfego.
+ Você pode criar uma conexão entre sua rede local e a nuvem da Microsoft de quatro maneiras diferentes, Colocalização do CloudExchange, Conexão de Ethernet Ponto a Ponto, Conexão de Qualquer para Qualquer (IPVPN) e ExpressRoute Direct.
+ Os recursos do ExpressRoute são determinados pelo SKU: Local, Standard e Premium. 
