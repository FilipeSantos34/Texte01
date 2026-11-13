
# 📘 Sistema de Empréstimo de Equipamentos

<p align="center">

![Java](https://img.shields.io/badge/Java-25-orange?logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-4.1.2-6DB33F?logo=springboot&logoColor=white)
![Maven](https://img.shields.io/badge/Maven-Build-C71A36?logo=apachemaven&logoColor=white)
![Spring Security](https://img.shields.io/badge/Spring%20Security-Enabled-6DB33F?logo=springsecurity&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-Authentication-black?logo=jsonwebtokens)
![Python](https://img.shields.io/badge/Python-3-3776AB?logo=python&logoColor=white)
![API REST](https://img.shields.io/badge/API-REST-2496ED)
![H2 Database](https://img.shields.io/badge/Database-H2-blue)

</p>

---

**Sistema desenvolvido com Java, Spring Boot, Spring Security e Python.**

Sistema desenvolvido para facilitar o gerenciamento de equipamentos, colaboradores, empréstimos e devoluções, oferecendo mais organização, segurança e controle.

---

## 📌 1. Sobre o projeto

### Qual problema queremos resolver?

Atualmente, o controle de equipamentos pode ser realizado por meio de planilhas, o que pode gerar problemas como:

- Dificuldade para identificar quais equipamentos estão disponíveis.
- Possibilidade de emprestar o mesmo equipamento para duas pessoas.
- Falta de controle sobre devoluções atrasadas.
- Dificuldade para consultar o histórico de empréstimos.

Nosso projeto busca solucionar esses problemas por meio de um sistema que organiza o cadastro de equipamentos e colaboradores, controla os empréstimos e facilita o acompanhamento das devoluções.

## 🚀 2. Funcionalidades do sistema

- Cadastro de equipamentos com número de patrimônio único.
- Cadastro de colaboradores com preenchimento de endereço via ViaCEP.
- Registro de empréstimos com prazo padrão de 14 dias.
- Bloqueio de empréstimos de equipamentos que já estejam emprestados.
- Registro de devoluções e liberação automática dos equipamentos.
- Consulta de empréstimos e identificação de atrasos.
- Autenticação com JWT.
- Controle de acesso por perfis ADMIN e COLABORADOR.
- Geração de relatórios em Excel com:
  - Empréstimos atrasados.
  - Quantidade de equipamentos emprestados por tipo.

## 🛠️ 3. Tecnologias utilizadas

| Tecnologia | Finalidade |
| --- | --- |
| Java 25 | Linguagem principal do sistema |
| Spring Boot 4.1.2 | Estrutura e inicialização da aplicação |
| Maven | Gerenciamento de dependências e compilação |
| Spring Web | Criação das rotas HTTP da API |
| Spring Data JPA | Comunicação com o banco de dados |
| Hibernate | Mapeamento objeto-relacional |
| H2 Database | Banco de dados local |
| Spring Security | Segurança e controle de permissões |
| JWT | Autenticação por token |
| JJWT 0.12.6 | Criação e validação de tokens JWT |
| BCrypt | Criptografia das senhas |
| Bean Validation | Validação dos dados recebidos |
| RestClient | Comunicação com APIs externas |
| ViaCEP | Consulta de endereços pelo CEP |
| Springdoc OpenAPI / Swagger UI | Documentação e testes da API |
| JUnit 5 | Testes automatizados |
| Spring Boot Test | Testes de integração |
| Spring Security Test | Testes de segurança |
| HTML | Estrutura da interface web |
| JavaScript | Comunicação entre tela e API |
| Python 3 | Geração de relatórios |
| Requests | Requisições HTTP em Python |
| Pandas | Organização e análise dos dados |
| OpenPyXL | Geração de planilhas Excel |
| Postman | Testes das rotas da API |
| VS Code / NetBeans / IntelliJ | Ambientes de desenvolvimento |

## 🏗️ 4. Arquitetura do sistema

O projeto utiliza uma arquitetura em camadas, separando as responsabilidades para facilitar a manutenção e organização do código.

### Fluxo principal

text

```text
        USUÁRIO
           |
           v
       INTERFACE
           |
           v
       CONTROLLER
           |
           v
        SERVICE
           |
           v
       REPOSITORY
           |
           v
       BANCO H2
```

### Integrações externas

text

```text
Sistema ---> ViaCEP ---> Consulta de endereço

Sistema ---> API ---> Python ---> Relatório Excel
```

As regras de negócio ficam concentradas na camada Service, enquanto os Controllers recebem as requisições e os Repositories realizam as operações no banco de dados.

## 💻 5. Como executar o projeto

### Pré-requisitos

- Java JDK 17 ou superior.
- Maven.
- VS Code, NetBeans ou IntelliJ IDEA.
- Postman (opcional, para testar a API).
- Python 3 (para gerar os relatórios).

### Passo a passo

**1\. Clone o repositório**

bash

```bash
git clone URL_DO_REPOSITORIO
```

**2\. Acesse a pasta do projeto**

bash

```bash
cd nome-do-projeto
```

**3\. Abra o projeto na IDE**

Importe o arquivo `pom.xml` como um projeto Maven.

**4\. Execute a aplicação**

No terminal, dentro da pasta onde está o `pom.xml`, execute:

bash

```bash
mvn spring-boot:run
```

### Endereços da aplicação

| Serviço | Endereço |
| --- | --- |
| Sistema Web | [http://localhost:8080](<http://localhost:8080>) |
| Swagger UI | [http://localhost:8080/swagger-ui/index.html](<http://localhost:8080/swagger-ui/index.html>) |
| Console H2 | [http://localhost:8080/h2-console](<http://localhost:8080/h2-console>) |

### Configuração do banco H2

| Configuração | Valor |
| --- | --- |
| JDBC URL | `jdbc:h2:file:./dados/emprestimos` |
| Usuário | `sa` |
| Senha | (vazia) |

## 🔐 6. Autenticação e segurança JWT

O sistema utiliza JWT (JSON Web Token) para autenticar usuários e proteger as rotas da API.

### Perfis de acesso

- **ADMIN:** possui permissões administrativas, como cadastrar equipamentos, colaboradores e registrar empréstimos e devoluções.
- **COLABORADOR:** possui acesso às funcionalidades de consulta permitidas pelo sistema.

### Usuários de demonstração

| Perfil | Usuário | Senha |
| --- | --- | --- |
| ADMIN | `admin` | `admin123` |
| COLABORADOR | `colaborador` | `colab123` |

> ⚠️ Atenção: essas credenciais são destinadas exclusivamente ao ambiente local de demonstração. Não utilize essas senhas em ambientes publicados na internet.

### Como funciona o JWT?

Imagine que a API seja uma escola com salas protegidas:

- **AuthController:** é a recepção, que recebe o usuário e a senha.
- **TokenService:** é a máquina que cria os crachás digitais (tokens JWT).
- **FiltroJwt:** é o segurança que verifica o crachá em cada requisição protegida.
- **SecurityConfig:** define quais áreas são públicas e quais exigem autenticação ou perfil ADMIN.

O JWT funciona como um crachá digital temporário. Ele não é a senha do usuário, mas uma autorização que permite acessar determinadas funcionalidades enquanto estiver válido.

### Como testar o login no Postman?

**1\. Envie uma requisição POST:**

http

```http
POST http://localhost:8080/auth/login
```

**2\. No Body, selecione raw → JSON:**

json

```json
{
  "usuario": "admin",
  "senha": "admin123"
}
```

**3\. Copie o campo****`token`****retornado pela API.**

**4\. Para acessar as rotas protegidas:**

- Entre na aba Authorization.
- Selecione Bearer Token.
- Cole o token recebido no login.

Também é possível testar com o usuário colaborador para verificar as permissões de acesso.

## 📂 7. Estrutura de pastas e camadas

| Pasta | Responsabilidade |
| --- | --- |
| `model` | Entidades do sistema |
| `repository` | Consultas e operações no banco |
| `service` | Regras de negócio |
| `controller` | Rotas e recebimento das requisições |
| `dto` | Objetos de transferência de dados |
| `security` | JWT e controle de permissões |
| `exception` | Tratamento de erros |
| `resources/static` | Interface web |
| `relatorio-python` | Scripts de geração de relatórios |

### Caminho de uma requisição

text

```text
Tela / Postman
      |
      v
  Controller
      |
      v
    Service
      |
      v
  Repository
      |
      v
 Banco de dados
```

## 📖 8. Principais rotas da API

| Método | Rota | Permissão |
| --- | --- | --- |
| POST | `/auth/login` | Público |
| GET | `/equipamentos` | Logado |
| GET | `/equipamentos/{id}` | Logado |
| POST | `/equipamentos` | ADMIN |
| PUT | `/equipamentos/{id}` | ADMIN |
| DELETE | `/equipamentos/{id}` | ADMIN |
| GET | `/colaboradores` | ADMIN |
| GET | `/colaboradores/{id}` | ADMIN |
| POST | `/colaboradores` | ADMIN |
| GET | `/colaboradores/{id}/emprestimos` | Logado |
| POST | `/emprestimos` | ADMIN |
| PUT | `/emprestimos/{id}/devolucao` | ADMIN |
| GET | `/emprestimos` | Logado |
| GET | `/emprestimos?atrasados=true` | Logado |

## 📝 9. Exemplos de requisições JSON

### Cadastro de equipamento

json

```json
{
  "patrimonio": "NB-0001",
  "nome": "Notebook Dell",
  "tipo": "NOTEBOOK"
}
```

### Cadastro de colaborador

json

```json
{
  "nome": "Ana Souza",
  "email": "ana@empresa.com",
  "cep": "01310100",
  "numero": "1000"
}
```

### Registro de empréstimo

json

```json
{
  "equipamentoId": 1,
  "colaboradorId": 1
}
```

## 📊 10. Relatório automático com Python

O projeto possui uma pasta chamada `relatorio-python`, responsável pela geração de relatórios gerenciais em Excel.

### Como executar?

**1\. Acesse a pasta:**

bash

```bash
cd relatorio-python
```

**2\. Instale as dependências:**

bash

```bash
pip install -r requirements.txt
```

**3\. Execute o programa:**

bash

```bash
python gerar_relatorio.py
```

### Resultado

O programa gera o arquivo:

text

```text
relatorio_emprestimos.xlsx
```

A planilha contém duas abas:

- **Atrasados:** apresenta os empréstimos que estão fora do prazo.
- **Emprestados por tipo:** apresenta a quantidade de equipamentos emprestados por categoria.

## 👥 11. Integrantes da equipe


| Integrante | Responsabilidade | GitHub |
|---|---|---|
| Lucas | Tech Lead / Arquitetura | [![GitHub](https://img.shields.io/badge/GitHub-Eluriomilmar-181717?logo=github)](https://github.com/Eluriomilmar) |
| Cleber | Organização / Segurança / Python | [![GitHub](https://img.shields.io/badge/GitHub-bin--ribeiro-181717?logo=github)](https://github.com/bin-ribeiro) |
| Thiago | Segurança JWT / ViaCEP | [![GitHub](https://img.shields.io/badge/GitHub-thiagorosabio-181717?logo=github)](https://github.com/thiagorosabio) |
| Lara | QA | [![GitHub](https://img.shields.io/badge/GitHub-Lara--Karolinne-181717?logo=github)](https://github.com/Lara-Karolinne) |
| Filipe | README / Apresentação | [![GitHub](https://img.shields.io/badge/GitHub-FilipeSantos34-181717?logo=github)](https://github.com/FilipeSantos34) |
## 🎯 12. Objetivo do projeto

Desenvolver uma solução que facilite o gerenciamento de equipamentos, garantindo maior controle sobre empréstimos, devoluções e disponibilidade dos recursos.

Além disso, o projeto proporciona a aplicação prática de conhecimentos em desenvolvimento Java, APIs REST, banco de dados, segurança, integração com serviços externos, testes automatizados e geração de relatórios.

---


