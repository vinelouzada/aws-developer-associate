# AWS Certified Developer Associate

## Anotações importantes
- **Metodologia Canary** (ou implantação Canary) é uma estratégia de lançamento de software que reduz riscos ao implantar uma nova versão de forma gradual.
- Usar imagens Docker personalizadas que já contenham todas as dependências necessárias pode acelerar significativamente o tempo de build, uma vez que reduz o tempo gasto no download de dependências durante cada operação de build.
- O comando aws sts get-caller-identity exibe a identidade da chamada atual na AWS, útil para verificar a função IAM assumida, especialmente em scripts em EC2.

## AWS Secrets Manager

É útil para armazenar credenciais, chaves de API e tokens.

![alt text](https://d1.awsstatic.com/diagrams/Secrets-HIW.e84b6533ffb6bd688dad66cfca36622c2fa7c984.png)

Pode-se utilizar um cache local para guardar essas credênciais com o propósito de diminuir a latência na busca (GetSecretValue) de informações.

## AWS API Gateway

É um serviço **gerenciado** que permite que desenvolvedores criem, publiquem, mantenham, monitorem e protejam APIs em qualquer escala com facilidade. Tipos de APIs RESTful e APIs Websocket

![alt text](https://d1.awsstatic.com/serverless/New-API-GW-Diagram.c9fc9835d2a9aa00ef90d0ddc4c6402a2536de0d.png)

Você paga pelas chamadas feitas para sua API e pelos dados de saída transferidos. Ex. 0.90 USD a cada 1 milhão de solicitações na camada mais alta.

## AWS AppSync

Serviço gerenciado que utiliza **GraphQL** para criar **APIs** para aplicações que necessitam de dados em tempo real.

![alt text](https://d2908q01vomqb2.cloudfront.net/0a57cb53ba59c46fc4b692527a38a87c78d84028/2019/04/05/Picture1-1.png)

## AWS Cognito *

Implemente uma experiência de inscrição e login segura, escalável e personalizada em minutos

![alt text](https://docs.aws.amazon.com/pt_br/cognito/latest/developerguide/images/scenario-standalone.png)

* É possível configurar User poll para permitir acesso de usuário autenticado e não autenticado

## AWS SageMaker

É um serviço da AWS para criação, treinamento e implantação de modelos de machine learning em escala, com infraestrutura gerenciada e otimizada.

## AWS Redshift

É um data warehouse totalmente gerenciado e escalável para análise de grandes volumes de dados.

## AWS Redshift
Crie, treine e implante modelos de machine learning (ML) usando comandos SQL conhecidos

![alt text](https://d1.awsstatic.com/redshift/redshift-ml/product-page-diagram_Redshift-ML%402x.aff0635372bc75fff6c847b9d62ee52657f4f23b.png)

## AWS CloudShell

É um shell baseado em terminal que é preconfigurado com o AWS Command Line Interface (CLI) e outras ferramentas de desenvolvimento, facilitando a interação com serviços da AWS diretamente do navegador sem necessidade de configurar e gerenciar credenciais localmente. Oferece um ambiente seguro e isolado para executar scripts de gerenciamento.

## AWS SNS

Amazon SNS (Simple Notification Service) é um serviço gerenciado de mensageria que provê um sistema robusto e totalmente gerenciado para enviar notificações ou mensagens a uma grande quantidade de recebedores. Utilizar múltiplos tópicos SNS, um em cada região, com inscrições locais, é a estratégia mais eficiente para garantir a entrega rápida e confiável de mensagens em cenários globais distribuídos.

![alt](https://www.cloudbinary.io/assets/blogs/SNS.png)

## AWS Cloud Development Kit (CDK)

O AWS Cloud Development Kit (CDK) é um framework de código aberto que permite definir infraestrutura na AWS usando linguagens de programação como TypeScript, Python, Java e C#.


```
package com.myorg;

import software.amazon.awscdk.*;
import software.amazon.awscdk.services.ec2.*;
import software.constructs.Construct;

public class MyStack extends Stack {
    public MyStack(final Construct scope, final String id, final StackProps props) {
        super(scope, id, props);

        Vpc vpc = Vpc.Builder.create(this, "MyVpc")
                .maxAzs(2) // Usa até duas zonas de disponibilidade
                .build();

        Instance instance = Instance.Builder.create(this, "MyInstance")
                .instanceType(InstanceType.of(InstanceClass.BURSTABLE2, InstanceSize.MICRO))
                .machineImage(MachineImage.latestAmazonLinux())
                .vpc(vpc)
                .build();
    }
}
```

## AWS CloudFormation

O AWS CloudFormation é um serviço da AWS que permite modelar, provisionar e gerenciar infraestrutura na nuvem usando código declarativo (YAML ou JSON).

```
AWSTemplateFormatVersion: "2010-09-09"
Description: "Criação de uma instância EC2 com CloudFormation"

Resources:
  MyEC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      InstanceType: "t2.micro"
      ImageId: "ami-0c55b159cbfafe1f0"
      Tags:
        - Key: "Name"
          Value: "MinhaInstanciaEC2"

```

 - **Parametros no CloudFormation:** permitem que você defina valores de entrada que podem ser passados para o template durante a criação ou atualização de uma pilha (stack). Eles tornam o template reutilizável, permitindo que você personalize os recursos criados.

 ```
AWSTemplateFormatVersion: "2010-09-09"
Description: "Exemplo de instância EC2 com parâmetros"

Parameters:
  InstanceType:
    Description: "Tipo da instância EC2"
    Type: String
    Default: "t2.micro"
    AllowedValues:
      - "t2.micro"
      - "t2.small"
      - "t2.medium"
    ConstraintDescription: "Deve ser um tipo de instância EC2 válido."

Resources:
  MyEC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: "ami-0c55b159cbfafe1f0"
      Tags:
        - Key: "Name"
          Value: "MinhaInstanciaEC2ComParametro"

 ```

 #### Execução

 ```
aws cloudformation create-stack --stack-name MinhaStackComParametro --template-body file://ec2-instance-with-parameter.yaml --parameters ParameterKey=InstanceType,ParameterValue=t2.small

 ```

## AWS Cloud Development Kit x CloudFormation

| Característica        | AWS CloudFormation | AWS CDK |
|----------------------|------------------|--------|
| **Linguagem**        | YAML / JSON      | TypeScript, Python, Java, etc. |
| **Abordagem**        | Declarativa      | Programática |
| **Facilidade de Uso**| Mais verboso     | Mais conciso e reutilizável |
| **Reutilização**     | Limitada         | Alto nível de abstração com classes e métodos |
| **Conversão**        | Não gera código  | Converte código para CloudFormation automati

## AWS CodePipeline

Automatize pipelines de entrega contínua para oferecer atualizações rápidas e confiáveis

![alt](https://k21academy.com/wp-content/uploads/2021/03/php-project-release-pipeline.png)

## AWS IAM
Gerencia usuários, grupos, funções e permissões para controlar o acesso seguro aos recursos da AWS.

Foco no mínimo privilégio

## AWS PCA (Private Certificate Authority)

 É uma solução segura e eficaz para criar e gerenciar uma autoridade certificadora **privada** dentro do ambiente AWS. Essa autoridade pode emitir e revogar certificados SSL/TLS para atender requisitos específicos de segurança e conformidade, ideal para comunicação interna entre microserviços em uma aplicação.

 #### Diferença AWS PCA e AWS ACM

 | *Característica*               | *AWS ACM (AWS Certificate Manager)*                                   | *AWS PCA (Private Certificate Authority)*                                   |
|-----------------------------------|------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| *Tipo de Certificados*          | Certificados públicos para comunicação via HTTPS na internet          | Certificados privados para redes internas, sistemas privados, VPNs, etc.     |
| *Emissão*                       | Emissão de certificados públicos SSL/TLS para serviços da AWS          | Emissão de certificados privados SSL/TLS para uso interno (não acessível publicamente) |
| *Usos Principais*               | Proteção de sites, APIs, e aplicativos expostos à internet            | Proteção de redes internas, sistemas internos e comunicação segura privada  |
| *Gerenciamento Automático*      | Renovação automática de certificados públicos                          | Renovação e gerenciamento de certificados privados sob controle organizacional |
| *Custo*                          | Gratuito para certificados públicos (pode ter custos associados ao uso de certificados privados) | Custo associado à criação e operação de uma CA privada                       |
| *Integração*                    | Integração fácil com serviços AWS como ELB, CloudFront, API Gateway   | Integrável com sistemas internos, dispositivos IoT, redes privadas, etc.     |
| *Controle*                      | Gerenciamento simples e automatizado (sem controle de CA própria)     | Controle total sobre a hierarquia de autoridades certificadoras e políticas internas |
| *Gerenciamento de Certificados Privados* | Não fornece certificados privados, apenas públicos                    | Permite emissão de certificados privados e sua gestão |

## AWS ElastiCache for Memcached 
É um serviço gerenciado de cache na memória, usado para melhorar o desempenho de aplicativos, armazenando dados temporários e frequentemente acessados de forma rápida e escalável.

#### Comparação com Redis
| Característica                   | Memcached                          | Redis                              |
|------------------------------------|------------------------------------|------------------------------------|
| **Persistência**                  | Não suporta persistência          | Suporta persistência (snapshots, AOF) |
| **Estruturas de Dados**           | Simples (strings)                 | Avançadas (listas, conjuntos, hashes, etc.) |
| **Escalabilidade Horizontal**     | Fácil de escalar horizontalmente  | Suporta escalabilidade com mais opções |
| **Desempenho**                    | Mais rápido para cache simples    | Menos rápido, mas eficiente e flexível |
| **Uso Típico**                    | Cache simples de dados           | Cache, filas, pub/sub, e persistência de dados |


## AWS CodeWhisperer

É uma ferramenta de assistente de codificação desenvolvida pela AWS que utiliza inteligência artificial (IA) para sugerir trechos de código enquanto você está programando.


## AWS CodeGuru
Amazon CodeGuru Reviewer é uma ferramenta de revisão de código automatizada que ajuda a identificar problemas e vulnerabilidades de segurança no código. 

## AWS StepFunctions
Permite orquestrar serviços da AWS de forma visível de forma gerenciada.
Ex: Chamada pra vários Lamdas. Aprovação de documentos, onde um fluxo chama Lambdas e DynamoDB para validar e armazenar informações.
- É possível configurar retry

## AWS DynamoDB
O Amazon DynamoDB é um serviço de banco de dados NoSQL oferecido pela Amazon Web Services (AWS) que é conhecido por sua escalabilidade e desempenho. Ele suporta tanto chaves de partição quanto chaves de classificação, que são críticas para a otimização de consultas e para a distribuição eficiente de dados. A escolha de como modelar chaves no DynamoDB pode afetar diretamente o desempenho de leitura e escrita.

- Chave de partição: ?
- Chave de classificação: ?

- O uso do DynamoDB Accelerator (DAX) é uma prática recomendada para reduzir a latência de acesso em consultas de leitura frequente, pois DAX adiciona uma camada de caching transparente para o DynamoDB.

## S3
Amazon S3 oferece uma variedade de opções de segurança para armazenar dados com segurança, incluindo criptografia em trânsito usando HTTPS e criptografia em repouso usando diferentes métodos, como o AWS KMS. Essencialmente, é importante habilitar ambas as criptografias em trânsito e em repouso para proteger dados confidenciais.

## AWS Amplify

Facilita a construção rápidas de aplicações full-stack

## AWS AppSync

AWS AppSync é um serviço gerenciado que facilita o desenvolvimento de aplicações móveis e web permitindo criar APIs GraphQL flexíveis. É ideal para agregar dados de múltiplas fontes - como DynamoDB, Lambda e Elasticsearch - de maneira eficiente. A utilização do Lambda como resolver permite uma orquestração segura e eficiente dos dados, enquanto que o caching ajuda na responsividade e redução de custos.


## AWS SNS

- O uso de filtros de mensagens do Amazon SNS permite enviar notificações apenas para os inscritos que correspondam a certos atributos, como ter optado por receber notificações de promoções.

## CloudFront
Amazon CloudFront é um serviço de rede de entrega de conteúdo (CDN) que fornece uma distribuição segura, escalável e global de conteúdo. CloudFront funciona através do cache de cópias de conteúdo em múltiplos locais geográficos para fornecer conteúdo com alta disponibilidade e alta velocidade. Quando há necessidade de atualizar o conteúdo cacheado, a funcionalidade de invalidação, como por meio da API CreateInvalidation, é usada para remover conteúdos desatualizados.
