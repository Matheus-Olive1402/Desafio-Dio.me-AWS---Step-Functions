# AWS Step Functions

## 📌 O que é o AWS Step Functions?

O **AWS Step Functions** é um serviço de orquestração serverless da AWS que permite criar, executar e monitorar workflows utilizando diversos serviços da plataforma AWS.

Em vez de implementar toda a lógica de controle dentro da aplicação, o fluxo é definido utilizando a **Amazon States Language (ASL)**, um formato baseado em JSON que representa cada etapa do processo.

Cada etapa é chamada de **State (Estado)** e pode executar uma ação específica, como:

- Invocar uma função AWS Lambda;
- Executar uma tarefa em uma instância Amazon EC2;
- Iniciar um Job do AWS Glue;
- Executar containers no Amazon ECS;
- Publicar mensagens no Amazon SNS;
- Enviar mensagens para o Amazon SQS;
- Consultar bancos de dados;
- Tomar decisões baseadas em condições;
- Executar tarefas em paralelo;
- Aguardar um período antes de continuar o fluxo.

---

# 🎯 Objetivos

O AWS Step Functions foi desenvolvido para:

- Automatizar processos complexos.
- Reduzir código de orquestração.
- Facilitar a manutenção dos workflows.
- Melhorar a observabilidade das execuções.
- Implementar tratamento de erros.
- Integrar diversos serviços AWS.
- Criar pipelines escaláveis e resilientes.

---

# 🏗 Como Funciona

Cada workflow é composto por uma sequência de estados.

```text
Início
   │
   ▼
Ler Arquivo
   │
   ▼
Validar Dados
   │
   ▼
Processar
   │
   ▼
Salvar Resultado
   │
   ▼
Fim
```

Cada estado executa uma ação e define qual será o próximo estado.

---

# 📚 Principais Tipos de Estados

## Task

Executa uma tarefa.

Exemplos:

- AWS Lambda
- AWS Glue
- Amazon ECS
- Amazon EMR
- Amazon Batch

---

## Choice

Permite criar decisões dentro do fluxo.

```text
Existe arquivo?
       │
   ┌───┴────┐
   │        │
 Sim      Não
   │        │
Processa Encerrar
```

---

## Wait

Pausa o workflow por um período determinado.

Pode aguardar:

- segundos
- minutos
- horário específico

---

## Parallel

Executa diversas tarefas simultaneamente.

```text
           Início
              │
      ┌───────┴────────┐
      ▼                ▼
 Processar CSV     Gerar Logs
      │                │
      └───────┬────────┘
              ▼
             Fim
```

---

## Map

Executa uma mesma tarefa para diversos itens de uma lista.

Muito utilizado para:

- Arquivos
- Objetos S3
- Registros
- Listas

---

## Pass

Encaminha dados para outro estado sem executar nenhuma ação.

---

## Succeed

Finaliza o workflow com sucesso.

---

## Fail

Finaliza o workflow indicando erro.

---

# ☁ Integrações Nativas

O Step Functions possui integração direta com diversos serviços AWS.

Entre eles:

- Amazon S3
- AWS Lambda
- Amazon EC2
- AWS Glue
- Amazon ECS
- Amazon EKS
- Amazon EMR
- Amazon SNS
- Amazon SQS
- Amazon DynamoDB
- Amazon EventBridge
- Amazon Athena
- Amazon SageMaker
- AWS Systems Manager

---

# 🚀 Casos de Uso

## Engenharia de Dados

- ETL
- ELT
- Data Lake
- Data Warehouse
- Processamento de arquivos
- Ingestão de dados

---

## Machine Learning

- Treinamento de modelos
- Inferência
- Pipelines de IA

---

## DevOps

- CI/CD
- Deploy automatizado
- Provisionamento

---

## Integração entre Sistemas

- APIs
- Microserviços
- Sistemas Legados

---

## Automação

- Aprovação de documentos
- Processos internos
- Auditorias

---

# 🛠 Como Criar uma Step Function

## 1. Acesse o Console AWS

```
AWS Console
→ Step Functions
→ Create State Machine
```

---

## 2. Escolha o Tipo

Existem dois tipos principais:

### Standard

Ideal para:

- Processos longos
- Alta confiabilidade
- Auditoria completa

---

### Express

Ideal para:

- Alto volume
- Baixa latência
- Execuções rápidas

---

## 3. Defina o Workflow

O workflow pode ser criado:

- Pelo Workflow Studio (visual)
- Escrevendo diretamente em JSON (Amazon States Language)

---

## 4. Configure as Permissões

A Step Function precisa de uma IAM Role com permissões para acessar os serviços utilizados.

Exemplo:

- Amazon S3
- AWS Lambda
- Amazon CloudWatch
- Amazon SNS

---

## 5. Teste a Execução

Após publicada, basta clicar em:

```
Start Execution
```

O console exibirá toda a execução em tempo real.

---

# 📌 Exemplo de Projeto

## Cenário

Uma empresa recebe arquivos CSV contendo posições geográficas de sensores instalados em diferentes regiões.

Esses arquivos são enviados para um bucket denominado:

```
geodata-input
```

O objetivo é:

- verificar se existem arquivos;
- validar os CSVs;
- copiar os arquivos válidos;
- armazenar em outro bucket;
- registrar logs.

---

# 🏗 Fluxograma

```text
                    Início
                       │
                       ▼
      Verificar Bucket geodata-input
                       │
              Existem arquivos?
               ┌────────┴────────┐
               │                 │
              Sim               Não
               │                 │
               ▼                 ▼
      Validar Arquivos       Encerrar
               │
               ▼
      Copiar para Bucket
     geodata-processed
               │
               ▼
      Registrar Logs
               │
               ▼
              Fim
```

---

# 📂 Fluxo Detalhado

## Etapa 1

Consultar o bucket:

```
geodata-input
```

Objetivo:

- Listar objetos.
- Contar arquivos.
- Verificar novos uploads.

---

## Etapa 2

Estado **Choice**

Caso existam arquivos:

```
Continuar
```

Caso contrário:

```
Finalizar execução
```

---

## Etapa 3

Validar os arquivos CSV.

Verificações:

- Extensão `.csv`
- Arquivo vazio
- Cabeçalho
- Quantidade de colunas
- Codificação UTF-8

Exemplo:

```csv
latitude,longitude,data
-23.55052,-46.63330,2026-07-07
```

---

## Etapa 4

Copiar o arquivo para:

```
geodata-processed/
```

Estrutura:

```text
geodata-processed/
│
├── 2026/
│   ├── 07/
│   │   ├── sensores_sp.csv
│   │   └── sensores_rj.csv
```

---

## Etapa 5

Registrar informações da execução.

Exemplo:

- Horário
- Quantidade de arquivos
- Tempo de processamento
- Status

Essas informações podem ser enviadas ao Amazon CloudWatch.

---

# 🔒 Tratamento de Erros

O Step Functions possui mecanismos nativos de:

- Retry
- Catch
- Timeout

Exemplo:

```text
Copiar Arquivo
      │
      ▼
 Ocorreu erro?
   ┌──────┴──────┐
   │             │
 Não           Sim
   │             │
 Continua     Retry
                 │
                 ▼
          Continua falhando?
                 │
          ┌──────┴──────┐
          │             │
         Não          Sim
          │             │
 Continua      Registrar Erro
                    │
                    ▼
                  Encerrar
```

---

# 📈 Benefícios

- Arquitetura Serverless.
- Fluxos visuais.
- Fácil manutenção.
- Escalabilidade automática.
- Monitoramento integrado.
- Tratamento automático de erros.
- Auditoria completa das execuções.
- Integração com dezenas de serviços AWS.

---

# 🚀 Possíveis Melhorias

Este fluxo pode evoluir para:

- Validar os arquivos utilizando AWS Lambda.
- Processar dados com AWS Glue.
- Consultar dados utilizando Amazon Athena.
- Enviar notificações via Amazon SNS.
- Armazenar informações em Amazon RDS.
- Publicar eventos no Amazon EventBridge.
- Integrar com dashboards do Amazon QuickSight.

---

# 📖 Conclusão

O AWS Step Functions é uma solução de orquestração poderosa para aplicações distribuídas e pipelines de dados. Com uma abordagem visual e baseada em estados, permite integrar diversos serviços da AWS, automatizar processos complexos e implementar fluxos escaláveis, resilientes e de fácil manutenção.

No exemplo apresentado, a Step Function automatiza a verificação de arquivos CSV contendo posições geográficas, valida os dados, realiza a movimentação entre buckets S3 e registra toda a execução, demonstrando um cenário comum em projetos de Engenharia de Dados na AWS.
