---
demo:
  module: Module 01 - Introduction to Azure Virtual Networks
  title: Rotas personalizadas (Módulo 01)
---
## Configurar pontos de extremidade e roteamento de rede

Nesta demonstração, aprenderemos como criar uma tabela de rotas, definir uma rota personalizada e associar a rota a uma sub-rede.

**Observação:** esta demonstração requer uma rede virtual com pelo menos uma sub-rede.

**Referência**: [rotear o tráfego de rede – tutorial – portal do Azure](https://learn.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal#create-a-route-table)

**Criar uma tabela de roteamento**

1. À medida que tiver tempo, reveja o diagrama do tutorial. Explique por que você precisa criar uma rota definida pelo usuário. 

1. Acesse o portal do Azure.

1. Pesquise por **Tabela de rotas** e selecione essa opção. Discuta quando **as rotas de gateway de propagação** devem ser usadas. 

1. Crie uma tabela de roteamento e explique as configurações incomuns. 

1. Aguarde até que a nova tabela de roteamento seja implantada.

**Adicionar uma rota**

1.  Selecione sua nova tabela de roteamento e, em seguida, selecione **Rotas**.

1.  Crie uma nova **rota**. Discuta os diferentes **tipos de saltos** disponíveis. 

1.  Crie a nova rota e aguarde a implantação do recurso.
 
**Associar uma tabela de rotas a uma sub-rede**

1.  Navegue até a sub-rede à qual você deseja associar a tabela de rotas.

1.  Selecione  **Tabela de rotas** e escolha sua nova tabela de roteamento. 

1.  **Salve** suas alterações.


