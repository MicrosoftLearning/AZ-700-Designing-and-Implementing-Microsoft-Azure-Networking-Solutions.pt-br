---
demo:
  title: Gateways VPN
  module: Module 02 - Design and implement hybrid networking
---
Nesta demonstração, exploraremos gateways de rede virtual.

**Observação:** esta demonstração funciona melhor com duas redes virtuais com sub-redes.

## Explore a folha de sub-rede do Gateway
1. Em uma de suas redes virtuais, selecione a folha  **Sub-redes** .
1. Selecione  **+ Sub-rede de gateway**. O nome da sub-rede não pode ser alterado. Observe o  **intervalo de endereços**  da sub-rede do gateway. O endereço deve estar contido no espaço de endereço da rede virtual.
1. Lembre-se de que cada rede virtual precisa de uma sub-rede de gateway.
1. Feche a página Adicionar sub-rede de gateway. Não é necessário salvar as alterações.

## Explore a folha Dispositivos Conectados
1. Na rede virtual, selecione a folha  **Dispositivos Conectados** .
1. Depois que uma sub-rede de gateway for implantada, ela aparecerá na lista de dispositivos conectados.

## Explore a adição de um gateway de rede virtual
1. Pesquise  **Gateways de rede virtual**.
1. Clique em  **+Adicionar**.
1. Revise cada configuração do gateway de rede virtual.
1. Use os ícones Informações para saber mais sobre as configurações.
1. Observe o  **Tipo de gateway**,  **tipo de VPN** e  **SKU**.
1. Veja se é necessário um  **endereço IP público**.
1. Lembre-se de que cada rede virtual precisará de um gateway de rede virtual.
1. Feche Adicionar gateway de rede virtual. Não é necessário salvar as alterações.
   
## Explore a adição de uma conexão entre as redes virtuais
1. Pesquise  **Conexões**.
1. Clique em  **+Adicionar**.
1. O  **Tipo de conexão**  pode ser VNet a VNet, Site a site (IPsec) ou ExpressRoute.
1. Forneça informações suficientes e clique no botão  **Ok** .
1. Na página  **Configurações**  você precisará selecionar as duas redes virtuais diferentes.
1. Leia as informações da Ajuda na caixa de seleção  **Estabelecer conectividade bidirecional** .
1. Observe as informações de  **PSK (chave compartilhada)** .
1. Feche a página Adicionar conexão. Não é necessário salvar as alterações.
