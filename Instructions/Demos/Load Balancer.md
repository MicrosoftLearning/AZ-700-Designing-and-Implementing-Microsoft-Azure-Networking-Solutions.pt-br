---
demo:
  module: Module 04 - Load balancing non-HTTPS traffic
  title: Balanceador de Carga (Módulo 04)
---
## Configurar o Azure Load Balancer

Nesta demonstração, aprenderemos como criar um balanceador de carga público. 

**Simulação:**[criar e configurar um balanceador de carga do Azure](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Create%20and%20configure%20an%20Azure%20load%20balancer)

**Referência**: [Guia de Início Rápido: criar um balanceador de carga público para balancear cargas de VMs usando o portal do Azure](https://learn.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)

**Observação:** esta demonstração requer uma rede virtual com pelo menos uma sub-rede. 

**Mostrar o recurso ajude-me a escolher do portal**

1. Acesse o portal do Azure.

1. Pesquise e selecione **Balanceamento de carga – ajude-me a escolher**.

1. Use o assistente para percorrer diferentes cenários.
   
**Criar um balanceador de carga**

1. Continue no portal do Azure.

1. Pesquise e selecione **Balanceador de carga**. **Crie um balanceador de carga.** 

1. Na guia **Noções básicas**, discuta **SKU**, **Tipo** e **Camada**.

1. Na guia **Configuração de IP de front-end**, discuta o uso de um endereço IP público.

1. Na guia **Pools de back-end**, selecione a rede virtual com o intervalo de endereço IP.

1. Na guia **Regras de entrada**, crie uma regra de balanceamento de carga. Discuta parâmetros como **Protocolo**, **Portas**, **Investigações de integridade** e **Persistência da sessão**. 


