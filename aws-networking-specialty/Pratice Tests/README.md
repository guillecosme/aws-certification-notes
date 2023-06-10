# Questões | Section-Based – Network Design (Advanced Networking)

## Tópicos mais errados:

    - BGP COmnities Tags
    - Pré Requsitos para Estabelecerum DX Connection
    - Tipos de Túneis de uma VPC
    - Cenário de Acesso Multi Region com um DX Gateway
    - Route53 DNS Firewall
    - ENA Linux Kernell Driver & Enhanced Network versus Elastic Fabric Adapter


- Se você adicionar um bloco roteável a uma VPC (exemplo: 34.16.0.0/16) e por algum motivo você precisar adicionar um CIDR secundário a esta VPC, qual seria a particularidade para adicionar este novo bloco?

    - Uma vez que o CIDR Primário da VPC é publicamente roteável, qualquer outro CIDR adicionado a esta VPC deverá estritamente ser roteável também.
    - Referência: https://docs.aws.amazon.com/vpc/latest/userguide/vpc-cidr-blocks.html


- Qual seria o IPD do serviço de NTP (Network Time Protocol) utilizado e diposnibilizado pela AWS acessível de todas as instâncias?

    - https://169.254.169.123 

-  Quias são os tipos de tuneis suportados por uma Amazon Site to SIte VPN?

- BGP communities Tag: QUias são as tags BGP utilizadas para mesmo continente, mesma região, serviço global e etc?
- Revisar CLoud HUB.