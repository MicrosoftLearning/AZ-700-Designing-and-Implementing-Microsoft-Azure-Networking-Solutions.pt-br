---
demo:
  module: Module 01 - Introduction to Azure Virtual Networks
  title: Resolução de nome DNS (Módulo 01)
---
## Configurar o DNS do Azure

Nesta demonstração, exploraremos o DNS do Azure.

**Referência**: [Tutorial: hospedar seu domínio e subdomínio – DNS do Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)

**Criar uma zona DNS**

1. Acessar o portal do Azure.

1. Procure o serviço de  **zonas DNS** .

1. Crie uma **zona DNS** e explique a finalidade da zona. Para um nome, você pode usar contoso.internal.com.

1.  Aguarde a zona DNS ser criada. Talvez seja necessário **Atualizar** a página.

**Adicionar conjunto de registros DNS**

**Referência**: [Tutorial: criar um registro de alias para se referir a um registro de recurso de zona](https://learn.microsoft.com/azure/dns/tutorial-alias-rr)

1. Depois que sua zona for criada, selecione **+Conjunto de registros**.

1. Use a lista suspensa **Tipo** para exibir os diferentes tipos de registros. Revise como os diferentes tipos de registro são usados. Observe como as informações do registro mudam à medida que você seleciona diferentes tipos de registro.

1. Crie um registro **A** como exemplo. 

