---
demo:
  title: 'Emparelhamento VNET            '
  module: Module 01 - Introduction to Azure Virtual Networks
---
## Configurar o emparelhamento de VNet

**Observação:**  para esta demonstração você precisará de duas redes virtuais.

**Referência**: [Conectar redes virtuais com o emparelhamento VNET – tutorial](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal)

**Configurar o emparelhamento VNet na primeira rede virtual**

1. No **portal do Azure**, selecione a primeira rede virtual. Analise o valor do emparelhamento. 

1. Em  **Configurações**, selecione **Emparelhamentos** e **+ Adicionar** um novo emparelhamento.

1. Configure o emparelhamento da segunda rede virtual. Use os ícones de informação para revisar as diferentes configurações. 

1. Quando o emparelhamento estiver concluído, revise o **Status de emparelhamento**. 

**Confirmar o emparelhamento VNet na segunda rede virtual**

1. No  **portal do Azure**, selecione a segunda rede virtual

1. Em  **Configurações**, selecione  **Emparelhamentos**.

1. Observe que um emparelhamento foi criado automaticamente. Observe que o  **Status de emparelhamento**  está como  **Conectado**.

1. No laboratório, os estudantes farão um emparelhamento e testarão a conexão entre máquinas virtuais. 
