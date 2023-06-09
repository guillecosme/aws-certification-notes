# VPC Hybrid Networking (Physical) - Deep Dive 

O direct connect é uma conexação estritamente física entre o data center on-premisses, a zona do DX (DX Location) e uma região da AWS. Na DX Location a AWS disponibiliza uma porta física para a conexão se Estabelecida.

- On-Premisses > DX Location > AWS Region

AWS leva um tempo para disponibilizar a porta na DX Location, pois é uma instalação física. A DX Location não é propriedada da AWS, muitas vezes é um provedor de datacenter, por exemplo Tivit, Equinix.

Uma vez que a infra estrutura esteja habilitada no provedor de serviços (Equinix e Tivit por exemplo). Uma cross connection é feita entre a porta do DX e o router do provedor e o seu roteador no Onpremisses.

Vael lembrar que o DX é conectado a uma AWS region e para estabelecer comunicação com os serviços da AWS, é preciso estabelecer interfaces virtuais sob a conexação física do DX.


## VIFs
As VIFs vascimanete são o mecanismo que permitem estabelecer comunicação de layer 03 sobre a layer 02.

A grosso modo uma VIF é uma sessão BGP.

Existem alguns tipos de VIFs (Virtual Interfaces):

 - Public VIF's: Estabelecem comunicação com  os serviços públicos da AWS (sqs, s3, servi catalog e etc)
 - Private VIF's: Estabelecem comunicação com os serviços privados da AWS (ec2, vpc, rds e etc).
 - Transit VIF's: Estabelecem comunicação com Transit Gateways.

![Conceitos da VIF](./images/dx-vif-concepts.png)
Um detalhe importante é que na arquitetura física do DX, não existem cripotgrafia na leyer física da conexão cross ou até mesmo nas VIFs.

- Conexões Privadas: Para realizar a conexão com algum serviço privado basicamente é necessário estabalecer uma sessão BGP (Private VIF), conectada a uma VPC através de um Virtaul Private Gateway  (VGW) da mesma região da conexão do DX.

A VIF é um serviço regional, essa limitação pode ser contornada por um Direct COnnect Gateway.

![DX VIF Multi Region](./images/dx-vif-multi-region.png)

- Conexões Públicas: Ao contrário das VIFs privadas as VIF's Públicas não necessitam de um VGW, pois a via BGP Advertisment todos os blocos de Ips públicos da AWS são declarados para a VIF.

![DX VIF Publica](./images/dx-vif-public-architecture.png)

 ## Arquitetura do DX:

![Arquitetura do DX](./images/dx-architecture.png)

## Bidirectional Forwarding Detection 
É uma feature que possibilita contruir uma maior redundancia entre VIFs.

Como cada VIF é uma sessão BGP, por padrão o BGP envia Keep ALives a cada 90 segundos para saber se a sessão continua ativa ou não, porém noventa segundos pode ser extremamente perigoso para aplicações críticas, então  BFD ajuda a idenficar uma queda de VIF em até 900ms, pois o BFD envia 3 keep alives com intervalo de 300 ms. Ou seja se a VIF estiver down, ela será identificada em até 900 ms.
## Velocidades de conexão

As portas na DX Location suportam  1 Gbps, 10 Gbps e 100 Gbps.

## Custos

A porta disponibilizada pela AWS (DX Location port) é cobrada por hora disponível e tráfego OUtbound (Data transfer)

## Direct Connect MAC Sec

Feature de Segurança habilitada no DX que pemrite a confidencialidade, nas transmissão de data frames na layer 2 da camada OSI.

Como por padrão o serviço de DX não possuí cripotgrafia na conexão cross dos datacenters, toda comunicação ocorre em texto puro na transmissão dos dados. Para solucionar este ponto seria possível estabelecer uma conexão de VPN IPsec em cima da conexão cross do DX, poreḿ isto seria custoso. O MAC Sec pode ajudar neste ponto uma vez que é possível habilitar esta feature ao criar canis de comunicações segurps entre os roteadores e conexoes cross envolvidas. A partir daí uma assinatura é exigida e adicionada nos frames da Layer 02. Onde apenas os equipamentos assinados e envolvidos na conexeão poderão realizar a decriptografia do payload do dataframe.

![Arquitetura do MacSec](./images/dx-mac-sec-architecture.png)
![DataFrame](./images/dx-mac-sec-frames.png)

## Direct Connect Gateway

Como o Direct Connect é um serviço estritamente regional há uma limitação ao establecer VIF's privadas para VPCs em múltiplas regiões, imagine o cenário onde uma topologia de rede possui mas de 10 VPC's, na mesma região ou não, para acessar s recursos dessas VPCs privadas via On Premisses através da conexão DX seria necessário criar uma VIF privada para cada VPCs, essa solução não escala muito bem.

Ao invés de criar uma VIF chegando em um VGW em cada VPC, seria possível criar um DX Gateway e a VIF privada terminaria do DX Gateway, e ele por si só teria a capacidade de se comunicar com múltiplas VPCs em múltiplas regiões.

Ao invés de cada VIF terminar e se associar a um VGW, você precisaria de apenas uma VIF privada associada a este DX Gateway e partir daí seria possível associar qualquer VGW de qualquer VPC a este Direct Connect Gateway.

- Cenário não escalável: Onpremisses > DX Location (VIF) > AWS Region > VPC VGW
- Cenário Direct Connect: On Premisses > DX Location (VIF) > AWS Region > DX Gateway > VPC VGW (múltiplas associações.)

Direct COnnect Gateway = Serviço Global

![Arquitetura DX Gateway](./images/dx-dx-gateway-architecture.png)

## Direct Connect e Transit Gateways

Como o DX Gateway só habilitaria a comunicação bilateral entre o OnPremisses e os recursos privados de VPC e não habilitaria a comunicação Hub and Spoke entre as VPCs, seria possível estabelecer uma VIF de trânsito sob a concexão cross do DX, e associá-la a Transit Gateways, desta maneira haveria comunicação latera entre as VPCs e também via On-premisses.

O DX Gateway suporta VIF privada e VIF transitiva, mas nunca as duas ao mesmo tempos, caso necessite das duas, você precisará configurar dois DX Gateways.

![TGW-DX-Gateway](./images/dx-tgw-dx-gateway.png)

## Letter of Authorization CUstomer Facility Access (LOA-CFA)

Dcoumento que autoriza a terceiros realizar a conexão do DX.