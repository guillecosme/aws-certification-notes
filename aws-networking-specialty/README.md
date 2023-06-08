# VPC Hybrid Networking (Physical) - Deep Dive 

O direct connect é uma conexação estritamente física entre o data center on-premisses, a zona do DX (DX Location) e uma região da AWS. Na DX Location a AWS disponibiliza uma porta física para a conexão se Estabelecida.

- On-Premisses > DX Location > AWS Region

AWS leva um tempo para disponibilizar a porta na DX Location, pois é uma instalação física. A DX LOcation não é propriedada da AWS, muitas vezes é um provedor de datacenter, por exemplo Tivit, Equinix.

Uma vez que a infra estrutura esteja habilitada no provedor de serviços (Equinix e Tivit por exemplo). Uma cross connection é feita entre a porta do DX e o router do provedor e o seu roteador no Onpremisses.

Vael lembrar que o DX é conectado a uma AWS region e para estabelecer comunicação com os serviços da AWS, é preciso estabelecer interfaces virtuais sob a conexação física do DX.

Existem alguns tipos de VIFs (Virtual Interfaces):

 - Public VIF's: Estabelecem comunicação com  os serviços públicos da AWS (sqs, s3, servi catalog e etc)
 - Private VIF's: Estabelecem comunicação com os serviços privados da AWS (ec2, vpc, rds e etc).

 Arquitetura do DX:

    ![Arquitetura do DX](./images/dx-architecture.png)

## Custos

A porta disponibilizada pela AWS (DX Location port) é cobrada por hora disponível e tráfego OUtbound (Data transfer)