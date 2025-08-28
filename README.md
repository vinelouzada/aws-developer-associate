# AWS Certified Developer Associate

## Anotações importantes

- **Metodologia Canary** (ou implantação Canary) é uma estratégia de lançamento de software que reduz riscos ao implantar uma nova versão de forma gradual.
- Usar imagens Docker personalizadas que já contenham todas as dependências necessárias pode acelerar significativamente o tempo de build, uma vez que reduz o tempo gasto no download de dependências durante cada operação de build.
- O comando aws sts get-caller-identity exibe a identidade da chamada atual na AWS, útil para verificar a função IAM assumida, especialmente em scripts em EC2.
- Arquivos descompactados não podem passar de 250MB
- Depois que cria um fila default, nao tem como modificar, precisa criar outra FIFO
- Indice Local do Dynamo so pode ser criado quando o tabela é criada
- Em uma police da funcao precisa adicionar CloudWatchLogs para conseguir verificar os Logs
- Os arquivos de configurações na pasta .ebeextensions do Elastic Beanstalk devem ser salvos com extensão .config
- No CodeBuild só ações dentro do mesmo estágio podem ser paralelo

## Palavras-Chave

- **Rotação automatica de chaves:** AWS Secret Manager
- **Reutilizar dependências entre funções Lambdas:** Lambda Layers
- **Testar igual em produção, mas localmente:** SAM
- **Upload de arquivos a partir de 100MB S3:** Upload Multipart
- **Identificar gargalos/latencia na app/infra:** AWS X-Ray
- 

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

## AWS Cognito \*

Implemente uma experiência de inscrição e login segura, escalável e personalizada em minutos

![alt text](https://docs.aws.amazon.com/pt_br/cognito/latest/developerguide/images/scenario-standalone.png)

- É possível configurar User poll para permitir acesso de usuário autenticado e não autenticado

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

| Característica        | AWS CloudFormation | AWS CDK                                       |
| --------------------- | ------------------ | --------------------------------------------- |
| **Linguagem**         | YAML / JSON        | TypeScript, Python, Java, etc.                |
| **Abordagem**         | Declarativa        | Programática                                  |
| **Facilidade de Uso** | Mais verboso       | Mais conciso e reutilizável                   |
| **Reutilização**      | Limitada           | Alto nível de abstração com classes e métodos |
| **Conversão**         | Não gera código    | Converte código para CloudFormation automati  |

## AWS CodePipeline

Automatize pipelines de entrega contínua para oferecer atualizações rápidas e confiáveis

![alt](https://k21academy.com/wp-content/uploads/2021/03/php-project-release-pipeline.png)

## AWS IAM

Gerencia usuários, grupos, funções e permissões para controlar o acesso seguro aos recursos da AWS.

Foco no mínimo privilégio

## AWS PCA (Private Certificate Authority)

É uma solução segura e eficaz para criar e gerenciar uma autoridade certificadora **privada** dentro do ambiente AWS. Essa autoridade pode emitir e revogar certificados SSL/TLS para atender requisitos específicos de segurança e conformidade, ideal para comunicação interna entre microserviços em uma aplicação.

#### Diferença AWS PCA e AWS ACM

| _Característica_                         | _AWS ACM (AWS Certificate Manager)_                                                              | _AWS PCA (Private Certificate Authority)_                                              |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------- |
| _Tipo de Certificados_                   | Certificados públicos para comunicação via HTTPS na internet                                     | Certificados privados para redes internas, sistemas privados, VPNs, etc.               |
| _Emissão_                                | Emissão de certificados públicos SSL/TLS para serviços da AWS                                    | Emissão de certificados privados SSL/TLS para uso interno (não acessível publicamente) |
| _Usos Principais_                        | Proteção de sites, APIs, e aplicativos expostos à internet                                       | Proteção de redes internas, sistemas internos e comunicação segura privada             |
| _Gerenciamento Automático_               | Renovação automática de certificados públicos                                                    | Renovação e gerenciamento de certificados privados sob controle organizacional         |
| _Custo_                                  | Gratuito para certificados públicos (pode ter custos associados ao uso de certificados privados) | Custo associado à criação e operação de uma CA privada                                 |
| _Integração_                             | Integração fácil com serviços AWS como ELB, CloudFront, API Gateway                              | Integrável com sistemas internos, dispositivos IoT, redes privadas, etc.               |
| _Controle_                               | Gerenciamento simples e automatizado (sem controle de CA própria)                                | Controle total sobre a hierarquia de autoridades certificadoras e políticas internas   |
| _Gerenciamento de Certificados Privados_ | Não fornece certificados privados, apenas públicos                                               | Permite emissão de certificados privados e sua gestão                                  |

## AWS ElastiCache for Memcached

É um serviço gerenciado de cache na memória, usado para melhorar o desempenho de aplicativos, armazenando dados temporários e frequentemente acessados de forma rápida e escalável.

#### Comparação com Redis

| Característica                | Memcached                        | Redis                                          |
| ----------------------------- | -------------------------------- | ---------------------------------------------- |
| **Persistência**              | Não suporta persistência         | Suporta persistência (snapshots, AOF)          |
| **Estruturas de Dados**       | Simples (strings)                | Avançadas (listas, conjuntos, hashes, etc.)    |
| **Escalabilidade Horizontal** | Fácil de escalar horizontalmente | Suporta escalabilidade com mais opções         |
| **Desempenho**                | Mais rápido para cache simples   | Menos rápido, mas eficiente e flexível         |
| **Uso Típico**                | Cache simples de dados           | Cache, filas, pub/sub, e persistência de dados |

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

- aws s3 mb s3://<bucket> Creates an S3 <bucket>
- aws s3 ls s3://<bucket> List S3 objects inside <bucket>
- aws s3 cp <file(s)> s3://<bucket> Copies a local <file(s)> to S3 <bucket>

## AWS Amplify

Facilita a construção rápidas de aplicações full-stack

## AWS AppSync

AWS AppSync é um serviço gerenciado que facilita o desenvolvimento de aplicações móveis e web permitindo criar APIs GraphQL flexíveis. É ideal para agregar dados de múltiplas fontes - como DynamoDB, Lambda e Elasticsearch - de maneira eficiente. A utilização do Lambda como resolver permite uma orquestração segura e eficiente dos dados, enquanto que o caching ajuda na responsividade e redução de custos.

## AWS SNS

- O uso de filtros de mensagens do Amazon SNS permite enviar notificações apenas para os inscritos que correspondam a certos atributos, como ter optado por receber notificações de promoções.

## CloudFront

Amazon CloudFront é um serviço de rede de entrega de conteúdo (CDN) que fornece uma distribuição segura, escalável e global de conteúdo. CloudFront funciona através do cache de cópias de conteúdo em múltiplos locais geográficos para fornecer conteúdo com alta disponibilidade e alta velocidade. Quando há necessidade de atualizar o conteúdo cacheado, a funcionalidade de invalidação, como por meio da API CreateInvalidation, é usada para remover conteúdos desatualizados.

## AWS ECS

É um serviço que facilita a execução e o gerenciamento de aplicativos em contêineres Docker na nuvem. Ele simplifica a orquestração de contêineres, permitindo que você se concentre no desenvolvimento de suas aplicações, sem se preocupar com a infraestrutura subjacente.

- É necessário registrar nossa imagem no **Elastic Container Registry**

## AWS EFS

É um serviço que permite o compartilhamento de arquivos entre instâncias.

## Tipos de Deploy

| Tipo de Deploy                    | Descrição                                                                                    | Quando Usar                                                    | Vantagens                                              | Desvantagens                                                            | Suporte na AWS                                                   |
| --------------------------------- | -------------------------------------------------------------------------------------------- | -------------------------------------------------------------- | ------------------------------------------------------ | ----------------------------------------------------------------------- | ---------------------------------------------------------------- |
| **All at Once**                   | Atualiza **todas as instâncias ao mesmo tempo**.                                             | Atualizações rápidas em ambientes pequenos ou testes.          | Deploy mais rápido; simples de configurar.             | Downtime total durante deploy; risco alto se algo der errado.           | Elastic Beanstalk                                                |
| **Rolling**                       | Substitui gradualmente instâncias antigas pelas novas no mesmo grupo.                        | Atualizações regulares com baixo risco de downtime parcial.    | Downtime mínimo; simples.                              | Problemas podem afetar parte do tráfego; rollback parcial mais difícil. | Elastic Beanstalk, CodeDeploy, Auto Scaling                      |
| **Rolling with Additional Batch** | Cria um **batch adicional de instâncias novas** antes de iniciar a substituição das antigas. | Minimizar downtime durante rolling; ambientes críticos.        | Menor risco de downtime; mais seguro que Rolling puro. | Mais custo temporário (instâncias extras); configuração mais complexa.  | Elastic Beanstalk                                                |
| **Canary**                        | Lança a nova versão para uma pequena parte do tráfego antes de expandir.                     | Testar nova versão com risco controlado.                       | Reduz risco de impacto em produção; feedback rápido.   | Requer monitoramento; rollout mais lento.                               | CodeDeploy, API Gateway                                          |
| **Blue-Green**                    | Mantém dois ambientes completos; troca 100% do tráfego de uma vez.                           | Atualizações críticas que exigem rollback rápido.              | Rollback instantâneo; sem downtime.                    | Custo maior (dois ambientes simultâneos).                               | Elastic Beanstalk, CodeDeploy, ECS/Fargate, Lambda (via aliases) |
| **Immutable**                     | Cria novas instâncias do zero; só redireciona tráfego quando prontas.                        | Garantir atualização segura sem alterar instâncias existentes. | Zero impacto nas instâncias antigas; rollback seguro.  | Mais caro; tempo de deploy maior.                                       | Elastic Beanstalk, CodeDeploy, Auto Scaling                      |

## Tipos de Criptografia na AWS

### Criptografia de Dados em Repouso (Data at Rest)

| Tipo de Criptografia       | Descrição                                                               | Quando Usar                                                         | Vantagens                                                                    | Desvantagens                                                           | Serviços que Suportam            |
| -------------------------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------- | ---------------------------------------------------------------------------- | ---------------------------------------------------------------------- | -------------------------------- |
| **SSE-S3**                 | Criptografia do lado do servidor com chaves gerenciadas pelo Amazon S3. | Proteção básica sem necessidade de gerenciar chaves.                | Fácil configuração; sem custo adicional; gerenciamento automático de chaves. | Menor controle sobre as chaves; não adequado para compliance rigoroso. | S3, EBS, RDS                     |
| **SSE-KMS**                | Criptografia do lado do servidor com chaves gerenciadas pelo AWS KMS.   | Controle granular sobre chaves; auditoria; compliance.              | Controle de acesso; auditoria via CloudTrail; rotação automática de chaves.  | Custo adicional por operação; latência ligeiramente maior.             | S3, EBS, RDS, DynamoDB, SQS, SNS |
| **DSSE-KMS**               | Criptografia dupla do lado do servidor com chaves AWS KMS.              | Compliance ultra-rigoroso; dupla proteção criptográfica.            | Duas camadas independentes de criptografia; máxima segurança.                | Maior custo; performance ligeiramente reduzida.                        | S3                               |
| **SSE-C**                  | Criptografia do lado do servidor com chaves fornecidas pelo cliente.    | Controle total sobre as chaves de criptografia.                     | Cliente mantém controle total das chaves; sem dependência do KMS.            | Cliente responsável pelo gerenciamento de chaves; maior complexidade.  | S3                               |
| **Client-Side Encryption** | Dados criptografados antes de enviar para a AWS.                        | Máximo controle e segurança; dados sensíveis extremamente críticos. | AWS nunca vê dados não criptografados; controle total do cliente.            | Maior complexidade de implementação; cliente gerencia todo o processo. | S3, DynamoDB (via SDK)           |

### Criptografia de Dados em Trânsito (Data in Transit)

| Tipo de Criptografia | Descrição                                                                       | Quando Usar                                                         | Vantagens                                                                      | Desvantagens                                                       | Serviços que Suportam            |
| -------------------- | ------------------------------------------------------------------------------- | ------------------------------------------------------------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------ | -------------------------------- |
| **TLS/SSL**          | Protocolo de segurança para comunicação criptografada entre cliente e servidor. | Toda comunicação entre cliente e serviços AWS.                      | Padrão da indústria; amplamente suportado; performance otimizada.              | Configuração pode ser complexa em alguns cenários.                 | Todos os serviços AWS            |
| **HTTPS**            | HTTP sobre TLS/SSL para comunicação web segura.                                 | APIs REST, sites, aplicações web.                                   | Fácil implementação; suporte nativo em browsers; SEO benefits.                 | Overhead mínimo de performance.                                    | CloudFront, ALB, API Gateway, S3 |
| **SSL Termination**  | TLS terminado no load balancer, tráfego interno em texto claro.                 | Reduzir carga computacional nas instâncias EC2.                     | Melhor performance das instâncias; gerenciamento centralizado de certificados. | Tráfego interno não criptografado.                                 | ALB, NLB, CloudFront             |
| **End-to-End TLS**   | Criptografia TLS mantida até o destino final.                                   | Compliance rigoroso; dados altamente sensíveis.                     | Máxima segurança; criptografia em todo o caminho.                              | Maior carga computacional; configuração mais complexa.             | ALB, NLB, EKS                    |
| **SSL Pass-through** | Load balancer passa o tráfego TLS sem terminar a conexão.                       | Necessidade de TLS até o servidor final; certificados customizados. | Controle total sobre TLS no servidor; flexibilidade máxima.                    | Load balancer não pode inspecionar tráfego; menos funcionalidades. | NLB                              |

### Criptografia Especializada

| Tipo de Criptografia                | Descrição                                              | Quando Usar                                                    | Vantagens                                                             | Desvantagens                                      | Serviços que Suportam |
| ----------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------- | --------------------------------------------------------------------- | ------------------------------------------------- | --------------------- |
| **Field-Level Encryption**          | Criptografia de campos específicos durante o trânsito. | Proteger dados sensíveis específicos (PII, números de cartão). | Proteção granular; dados sensíveis sempre criptografados.             | Configuração complexa; overhead de processamento. | CloudFront            |
| **Envelope Encryption**             | Chave de dados criptografada por uma chave mestre.     | Grandes volumes de dados; performance otimizada.               | Eficiência para grandes datasets; chaves menores transitam pela rede. | Complexidade adicional de implementação.          | KMS, S3, EBS          |
| **Hardware Security Modules (HSM)** | Criptografia baseada em hardware dedicado.             | Compliance FIPS 140-2 Level 3; chaves de alta criticidade.     | Máxima segurança; isolamento físico; certificações.                   | Alto custo; complexidade operacional.             | CloudHSM              |

## Fórmulas de Cálculo WCU e RCU - DynamoDB

## Write Capacity Units (WCU)

| Operação                | Fórmula                                                | Descrição                                 | Exemplo                                                 |
| ----------------------- | ------------------------------------------------------ | ----------------------------------------- | ------------------------------------------------------- |
| **Standard Write**      | `WCU = CEIL(Item Size KB / 1) × Writes per Second`     | Cada WCU = 1 write de até 1KB por segundo | Item 1.5KB, 10 writes/s = CEIL(1.5/1) × 10 = 20 WCU     |
| **Transactional Write** | `WCU = CEIL(Item Size KB / 1) × Writes per Second × 2` | Transações consomem 2x mais WCU           | Item 1.5KB, 10 writes/s = CEIL(1.5/1) × 10 × 2 = 40 WCU |

## Read Capacity Units (RCU)

| Operação                       | Fórmula                                               | Descrição                                                       | Exemplo                                            |
| ------------------------------ | ----------------------------------------------------- | --------------------------------------------------------------- | -------------------------------------------------- |
| **Strongly Consistent Read**   | `RCU = CEIL(Item Size KB / 4) × Reads per Second`     | Cada RCU = 1 read fortemente consistente de até 4KB por segundo | Item 6KB, 10 reads/s = CEIL(6/4) × 10 = 20 RCU     |
| **Eventually Consistent Read** | `RCU = CEIL(Item Size KB / 4) × Reads per Second ÷ 2` | Eventually consistent consome metade dos RCUs                   | Item 6KB, 10 reads/s = CEIL(6/4) × 10 ÷ 2 = 10 RCU |
| **Transactional Read**         | `RCU = CEIL(Item Size KB / 4) × Reads per Second × 2` | Transações consomem 2x mais RCU                                 | Item 6KB, 10 reads/s = CEIL(6/4) × 10 × 2 = 40 RCU |

## Query e Scan Operations

| Operação               | Fórmula                                                         | Descrição                                              | Exemplo                                                              |
| ---------------------- | --------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------- |
| **Query (Strong)**     | `RCU = CEIL(Total Result Size KB / 4) × Queries per Second`     | RCU baseado no tamanho total dos resultados retornados | Query retorna 50KB, 5 queries/s = CEIL(50/4) × 5 = 65 RCU            |
| **Query (Eventually)** | `RCU = CEIL(Total Result Size KB / 4) × Queries per Second ÷ 2` | Eventually consistent Query consome metade             | Query retorna 50KB, 5 queries/s = CEIL(50/4) × 5 ÷ 2 = 32.5 → 33 RCU |
| **Scan**               | `RCU = CEIL(Scanned Data Size KB / 4) × Scans per Second ÷ 2`   | RCU baseado nos dados escaneados, não retornados       | Scan examina 100KB, 2 scans/s = CEIL(100/4) × 2 ÷ 2 = 25 RCU         |

## Burst e On-Demand

| Modo                 | Descrição                                 | Fórmula/Comportamento                        | Quando Usar                            |
| -------------------- | ----------------------------------------- | -------------------------------------------- | -------------------------------------- |
| **Provisioned Mode** | Capacidade fixa definida antecipadamente  | Usar fórmulas acima com capacidade definida  | Tráfego previsível; controle de custos |
| **Burst Capacity**   | Capacidade extra temporária disponível    | Até 300 segundos de capacidade não utilizada | Picos ocasionais de tráfego            |
| **On-Demand Mode**   | Pagamento por request sem provisionamento | `Custo = Requests × Preço por Request`       | Tráfego imprevisível; aplicações novas |
| **Auto Scaling**     | Ajuste automático da capacidade           | Baseado em métricas de utilização (70-90%)   | Tráfego variável mas com padrões       |

## Considerações Importantes

| Aspecto                          | Detalhes                                    | Impacto no Cálculo                                     |
| -------------------------------- | ------------------------------------------- | ------------------------------------------------------ |
| **Tamanho Mínimo**               | Cada item consome no mínimo 1 WCU e 1 RCU   | Items menores que 1KB/4KB ainda consomem 1 WCU/RCU     |
| **Arredondamento**               | Sempre arredondar para cima (CEIL)          | Item 0.1KB = 1 WCU, Item 4.1KB = 2 RCU                 |
| **Hot Partitions**               | Distribuição desigual de acessos            | Pode causar throttling mesmo com capacidade suficiente |
| **Global Secondary Index (GSI)** | Índices consomem capacidade separada        | WCU/RCU adicionais para cada GSI                       |
| **Local Secondary Index (LSI)**  | Compartilha capacidade com tabela principal | Incluir no cálculo total da tabela                     |

## Lambda
- Alias de Função Lambda

Exemplo
arn:aws:lambda:us-east-1:123456789012:function:minha-funcao:${stageVariables.ENV}


## DynamoDB Streams
É um recurso do Amazon DynamoDB que captura, de forma ordenada e quase em tempo real, as alterações (inserts, updates e deletes) feitas nas tabelas.
Essas alterações são gravadas em um fluxo (stream) que pode ser consumido por outras aplicações, como funções AWS Lambda, Kinesis, ou mesmo outro DynamoDB.

## AWS Elastic Beanstaliking

Mapping

## AWS Codebuild
Tem um pasta local que pode ser armazenada em cache dependencias...

## DynamoDB

StreamSpecifications

Para reforçar a consistencia no processo de atualização de valores nas tabelas, precisa utilizar **Gravação Condicionais**

## S3 Events Notifications
Permite que chamamos funcões Lambdas

## API Gateway Stages + Lambda Alias
<img width="1763" height="785" alt="image" src="https://github.com/user-attachments/assets/e11130c0-13e1-4548-9c56-077caa57d13a" />
