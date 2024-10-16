---
demo:
  title: Emparelhamento VNET (Módulo 01)
  module: Module 01 - Introduction to Azure Virtual Networks
---
## Configurar o emparelhamento de VNet

**Observação:**  para esta demonstração você precisará de duas redes virtuais.

**Simulação:**[conectar duas redes virtuais do Azure usando o emparelhamento do Azure](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Connect%20two%20Azure%20virtual%20networks%20using%20global%20virtual%20network%20peering)

**Referência**: [conectar redes virtuais com o Emparelhamento VNet – tutorial](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal)

**Configurar o emparelhamento VNet na primeira rede virtual**

1. No **portal do Azure**, selecione a primeira rede virtual. Analise o valor do emparelhamento. 

1. Em  **Configurações**, selecione **Emparelhamentos** e **+ Adicionar** um novo emparelhamento.

1. Configure o emparelhamento da segunda rede virtual. Use os ícones de informação para revisar as diferentes configurações. 

1. Quando o emparelhamento estiver concluído, revise o **Status de emparelhamento**. 

**Confirmar o emparelhamento VNet na segunda rede virtual**

1. No  **portal do Azure**, selecione a segunda rede virtual

1. Em  **Configurações**, selecione  **Emparelhamentos**.

1. Observe que um emparelhamento foi criado automaticamente. Observe que o  **Status de emparelhamento**  está como  **Conectado**.

1. No laboratório, os alunos criarão peering e testarão a conexão entre máquinas virtuais. 
