# AWS Certified Developer Associate

## Termos importantes
- **Metodologia Canary** (ou implantação Canary) é uma estratégia de lançamento de software que reduz riscos ao implantar uma nova versão de forma gradual.

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

Foco no menor privilégio