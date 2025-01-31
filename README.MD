# DocHexagonalArchitecture
## Proposta
Criar uma documentação para servir de referência para iniciantes na criação de um projeto com arquitetura Hexagonal utilizando o framework Spring Boot e linguagem de programação Java.

# Índice

1. [Proposta](#proposta)  
2. [O que é a Arquitetura Hexagonal](#o-que-e-a-arquitetura-hexagonal)  
3. [Benefícios](#beneficios)  
4. [Arquitetura](#arquitetura)  
5. [Explicação](#explicacao)  
   5.1 [Main](#main)  
   5.2 [Adapters](#adapters)  
   5.3 [Application](#application)  
   5.4 [Domain](#domain)  
   5.5 [Infraestructure/Config](#infraestructureconfig)  
   5.6 [Utils](#utils)  
   5.7 [Test](#test)  
       - 5.7.1 [Configs](#configs)  
       - 5.7.2 [IntegrationTest](#integrationtest)  
           - 5.7.2.1 [Controller](#integrationtestcontroller)  
       - 5.7.3 [Unittest](#unittest)  


## O que é a arquitetura Hexagonal
A **Arquitetura Hexagonal**, também conhecida como **Ports and Adapters**, é um modelo de design que busca desacoplar a lógica de negócio da infraestrutura, tornando o sistema mais flexível e testável. Essa abordagem permite que a aplicação se comunique com diferentes tecnologias (bancos de dados, APIs externas, interfaces de usuário) através de "portas" (interfaces) e "adaptadores" (implementações dessas interfaces).

## Benefícios:

- **Desacoplamento:** A lógica de negócio não depende de tecnologias específicas.
- **Facilidade de Testes:** A lógica de negócio pode ser testada isoladamente.
- **Manutenção Simples:** Facilita a substituição de dependências sem afetar a regra de negócio.

## Arquitetura
```
Projeto/src/ 
    ├── main/java/br/com/organizacao/ 
        │   ├── Adapters 
        │   │   ├── Inbound 
        │   │   │   │   ├── UserController.java (Exemplo) 
        │   │   ├── Outbound 
        │   │   │   ├── Entities 
        │   │   │   │   ├── JpaUserEntity.java (Exemplo) 
        │   │   │   ├── Repositories 
        │   │   │   │   ├── JpaUserRepository.java (Exemplo) 
        │   │   │   │   ├── UserRepositoryImpl.java (Exemplo) 
        │   │   │   ├── Storage 
        │   │   │   │   ├── ImageUploaderAdapter.java (Exemplo) 
        │   │   │   │   ├── ImageUploader.java (Exemplo) 
        │   │ 
        │   ├── Application 
        │   │   ├── Services 
        │   │   ├── UseCases 
        │   │ 
        │   ├── Domain 
        │   │   ├── User 
        │   │   │   ├── User.java (Exemplo) 
        │   │   │   ├── UserRepository.java (Exemplo) 
        │   │ 
        │   ├── Infrastructure/Config 
        │   │   ├── Exceptions 
        │   │   ├── AWSConfig.java (Exemplo) 
        │   │   ├── CorsConfig.java (Exemplo) 
        │   │ 
        │   ├── Utils 
        │   │   ├── Mapper 
        │   │   ├── UUIDGenerator.java (Exemplo) 
        │   │   
    ├── test/br/com/organizacao/ 
    │   ├── Configs 
    │   │   ├── TestConfigs.java (Exemplo) 
    │   ├── IntegrationTest 
    │   ├── IntegrationTest/Controller 
    │   ├── Unittest
```

## Explicação
### `Main`
### Adapters
Tudo o que diz respeito à comunicação com o exterior (requisições HTTP do front-end, banco de dados, APIs externas).

- **Inbound**
    - Responsável por receber dados externos e encaminhá-los para a lógica de negócio.
    - Exemplo: Controllers que recebem requisições HTTP.

- **Outbound**
  - Responsável por interagir com sistemas externos, como bancos de dados e APIs.
    - **Entities**
      - Contém classes que representam os modelos do banco de dados. Essas classes geralmente são anotadas com JPA (`@Entity`) para que possam ser gerenciadas pelo Spring Data JPA.
    
    - **Repositories**
      - Contém interfaces que estendem o Spring Data JPA, permitindo consultas no banco de dados sem precisar escrever SQL manualmente. 
      - Exemplo **UserRepositoryImpl.java**
        - Implementa UserRepository do **domínio(pasta Domain)**, faz override dos métodos estendendo **JpaUserRepository.java**
    - **Storage**
      - Contém Classes para lidar com o armazenamento de imagens, por exemplo, enviando arquivos para um serviço externo como Amazon S3, Google Cloud Storage, ou um servidor FTP. 
      - Exemplo **ImageUploaderAdapter**
        - Aqui, implementamos a interface ImageUploader, usando um serviço específico para armazenar imagens.
      -  Exemplo **ImageUploader**
         -  Essa interface define um contrato para upload de imagens, sem especificar como será feito. Isso segue o princípio de **abstração** da Arquitetura Hexagonal.

### Application
Essa camada contém toda a lógica de negócio da aplicação, sem depender de infraestrutura.
- **Services:** 
    - Vai implementar as lógicas da pasta **UseCases**.
- **UseCases:**
    - Definem as regras de negócio e cenários que a aplicação deve suportar.

### Domain
Tudo o que diz a respeito de domínio, entidades puras(Sem ter `@annotations` do SQL, JPA, etc.), etc.
- **User/User.java**
  - Representa o conceito de um usuário dentro do domínio da aplicação. Diferente da JpaUserEntity.java (que faz parte da persistência), essa entidade não contém anotações do JPA, pois deve ser uma classe pura, independente do banco de dados. 
- **User/UserRepository.java** 
  - Define contratos para operações de persistência, mas sem saber como esses dados serão armazenados. Dessa forma, a implementação real pode ser feita por um JpaUserRepository, MongoUserRepository ou até mesmo um repositório em memória para testes.

### Infraestructure/Config
Tudo o que diz respeito as configurações das ferramentas que vamos utilizar. Como por exemplo as configurações do Spring, SDK, outras bibliotecas, etc.
- **Exceptions**:
    - Pasta responsável por criar exceções personalizadas para as ferramentas utilizadas como CORS e Cloud.
- **AWSConfig e CorsConfig**
    - Arquivos java para servir de exemplo quais ferramentas configuram a aplicação.

### Utils
Contém classes auxiliares para otimizar o desenvolvimento.
- **Mapper**:
    - Realiza conversões entre DTOs, entidades e objetos de domínio.
- **UUIDGenerator.java**
    - Exemplo de codigo java utilizado dentro do projeto. Serve para gerar `IDs` únicos.


### `Test`
### Configs
Essa Pasta contém configurações globais para os testes.
-   Exemplo: **TestConfigs.java**
    -   Contém variáveis(**final**) que definem as porta do servidor, parâmetros de autorização do header, content types, origem HTTP, etc.
    -   
### IntegrationTest
Contém os testes de integração que validam componentes reais da aplicação, como serviços e persistência. Eles garantem que as partes do sistema funcionam juntas corretamente.

### IntegrationTest/Controller
Contém os testes que verificam se os endpoints da API funcionam corretamente.

### Unittest
Contém os testes unitários que validam componentes isolados, como serviços e regras de negócio, sem carregar todo o contexto do Spring.


