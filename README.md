📘 Sistema de Empréstimo de Equipamentos

<p align="center">

!Java !Spring Boot !Maven !Spring Security !JWT !Python !API REST !H2 Database

</p>

Sistema desenvolvido com Java, Spring Boot, Spring Security e Python.

Sistema desenvolvido para facilitar o gerenciamento de equipamentos, colaboradores, empréstimos e devoluções, oferecendo mais organização, segurança e controle.

📌 1. Sobre o projeto
Qual problema queremos resolver?

Atualmente, o controle de equipamentos pode ser realizado por meio de planilhas, o que pode gerar problemas como:

Dificuldade para identificar quais equipamentos estão disponíveis.
Possibilidade de emprestar o mesmo equipamento para duas pessoas.
Falta de controle sobre devoluções atrasadas.
Dificuldade para consultar o histórico de empréstimos.

Nosso projeto busca solucionar esses problemas por meio de um sistema que organiza o cadastro de equipamentos e colaboradores, controla os empréstimos e facilita o acompanhamento das devoluções.

🚀 2. Funcionalidades do sistema
Cadastro de equipamentos com número de patrimônio único.
Cadastro de colaboradores com preenchimento de endereço via ViaCEP.
Registro de empréstimos com prazo padrão de 14 dias.
Bloqueio de empréstimos de equipamentos que já estejam emprestados.
Registro de devoluções e liberação automática dos equipamentos.
Consulta de empréstimos e identificação de atrasos.
Autenticação com JWT.
Controle de acesso por perfis ADMIN e COLABORADOR.
Geração de relatórios em Excel com:
Empréstimos atrasados.
Quantidade de equipamentos emprestados por tipo.
🛠️ 3. Tecnologias utilizadas
Tecnologia	Finalidade
Java 25	Linguagem principal do sistema
Spring Boot 4.1.2	Estrutura e inicialização da aplicação
Maven	Gerenciamento de dependências e compilação
Spring Web	Criação das rotas HTTP da API
Spring Data JPA	Comunicação com o banco de dados
Hibernate	Mapeamento objeto-relacional
H2 Database	Banco de dados local
Spring Security	Segurança e controle de permissões
JWT	Autenticação por token
JJWT 0.12.6	Criação e validação de tokens JWT
BCrypt	Criptografia das senhas
Bean Validation	Validação dos dados recebidos
RestClient	Comunicação com APIs externas
ViaCEP	Consulta de endereços pelo CEP
Springdoc OpenAPI / Swagger UI	Documentação e testes da API
JUnit 5	Testes automatizados
Spring Boot Test	Testes de integração
Spring Security Test	Testes de segurança
HTML	Estrutura da interface web
JavaScript	Comunicação entre tela e API
Python 3	Geração de relatórios
Requests	Requisições HTTP em Python
Pandas	Organização e análise dos dados
OpenPyXL	Geração de planilhas Excel
Postman	Testes das rotas da API
VS Code / NetBeans / IntelliJ	Ambientes de desenvolvimento
🏗️ 4. Arquitetura do sistema

O projeto utiliza uma arquitetura em camadas, separando as responsabilidades para facilitar a manutenção e organização do código.

4.1. Fluxo principal
text
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

4.2. Integrações externas
text
Sistema ---> ViaCEP ---> Consulta de endereço

Sistema ---> API ---> Python ---> Relatório Excel


As regras de negócio ficam concentradas na camada Service, enquanto os Controllers recebem as requisições e os Repositories realizam as operações no banco de dados.

4.3. Fluxo geral das requisições

O fluxo representa a cadeia de filtros do Spring Security, validação do JWT, autorização, controllers, serviços, repositórios e respostas. Também destaca o login e o cadastro de colaboradores com consulta ao ViaCEP.

mermaid
flowchart TD
    A["Navegador / app.js<br/>Requisição HTTP"] --> B["Spring Boot"]
    B --> C["SecurityFilterChain<br/>SecurityConfig"]
    C --> D["FiltroJwt"]
    D --> E{"Cabeçalho Authorization<br/>Bearer presente?"}
    E -- "Sim" --> F["TokenService.ler(token)<br/>Validar assinatura e expiração"]
    F --> G{"JWT válido?"}
    G -- "Não" --> H["HTTP 401<br/>Token inválido ou expirado"]
    G -- "Sim" --> I["SecurityContextHolder<br/>Usuário + ROLE"]
    E -- "Não" --> J["Segue sem autenticação"]
    I --> K["Regras de autorização<br/>permitAll / authenticated / hasRole"]
    J --> K
    K --> L{"Acesso permitido?"}
    L -- "Não autenticado" --> M["HTTP 401<br/>Autenticação necessária"]
    L -- "Sem permissão" --> N["HTTP 403<br/>Acesso negado"]
    L -- "Sim" --> O{"Rota solicitada"}

    O -- "POST /auth/login" --> P["AuthController.login"]
    P --> Q["AuthenticationManager"]
    Q --> R["UserDetailsService<br/>Usuários em memória"]
    R --> S["BCryptPasswordEncoder<br/>Confere senha"]
    S --> T{"Credenciais válidas?"}
    T -- "Não" --> U["TratadorDeErros<br/>HTTP 401 + JSON"]
    T -- "Sim" --> V["TokenService.gerar(usuario, perfil)"]
    V --> W["TokenResposta<br/>token, perfil, usuario"]
    W --> X["HTTP 200 + JSON JWT"]

    O -- "POST /colaboradores" --> Y["ColaboradorController.cadastrar"]
    Y --> Z["ColaboradorService.cadastrar"]
    Z --> AA["ColaboradorRepository<br/>Verifica e-mail duplicado"]
    AA --> AB["ViaCepService.buscar(CEP)"]
    AB --> AC["Serviço externo ViaCEP"]
    AC --> AD["ViaCepResposta<br/>logradouro, bairro, localidade, UF"]
    AD --> AE["Colaborador montado<br/>com endereço + número"]
    AE --> AF["ColaboradorRepository.save"]
    AF --> AG[("Banco de dados")]
    AG --> AH["Resposta HTTP 201 + JSON Colaborador"]

    O -- "Rotas de equipamentos" --> AI["EquipamentoController"]
    AI --> AJ["EquipamentoService"]
    AJ --> AK["EquipamentoRepository"]
    AK --> AG
    AG --> AL["HTTP + JSON Equipamento(s)"]

    O -- "Rotas de empréstimos" --> AM["EmprestimoController"]
    AM --> AN["EmprestimoService"]
    AN --> AO["EmprestimoRepository / EquipamentoRepository / ColaboradorService"]
    AO --> AG
    AG --> AP["HTTP + JSON Empréstimo(s)"]

    Z -. "Exceções" .-> AQ["TratadorDeErros"]
    AJ -. "Exceções" .-> AQ
    AN -. "Exceções" .-> AQ
    AQ --> AR["Resposta de erro JSON<br/>400 / 404 / 409 / 502"]

    X --> AS["app.js processa resposta<br/>Guarda JWT no sessionStorage"]
    AH --> AT["app.js atualiza interface"]
    AL --> AT
    AP --> AT

💻 5. Como executar o projeto
Pré-requisitos
Java JDK compatível com a versão configurada no projeto.
Maven.
VS Code, NetBeans ou IntelliJ IDEA.
Postman (opcional, para testar a API).
Python 3 (para gerar os relatórios).
Passo a passo

1. Clone o repositório

bash
git clone URL_DO_REPOSITORIO


2. Acesse a pasta do projeto

bash
cd nome-do-projeto


3. Abra o projeto na IDE

Importe o arquivo pom.xml como um projeto Maven.

4. Execute a aplicação

No terminal, dentro da pasta onde está o pom.xml, execute:

bash
mvn spring-boot:run

Endereços da aplicação
Serviço	Endereço
Sistema Web	http://localhost:8080
Swagger UI	http://localhost:8080/swagger-ui/index.html
Console H2	http://localhost:8080/h2-console
Configuração do banco H2
Configuração	Valor
JDBC URL	jdbc:h2:file:./dados/emprestimos
Usuário	sa
Senha	(vazia)
🔐 6. Autenticação e segurança JWT

O sistema utiliza JWT (JSON Web Token) para autenticar usuários e proteger as rotas da API.

6.1. Perfis de acesso
ADMIN: possui permissões administrativas, como cadastrar equipamentos, colaboradores e registrar empréstimos e devoluções.
COLABORADOR: possui acesso às funcionalidades de consulta permitidas pelo sistema.
6.2. Usuários de demonstração
Perfil	Usuário	Senha
ADMIN	admin	admin123
COLABORADOR	colaborador	colab123
⚠️ Atenção: essas credenciais são destinadas exclusivamente ao ambiente local de demonstração. Não utilize essas senhas em ambientes publicados na internet.
6.3. Como funciona o JWT?

Imagine que a API seja uma escola com salas protegidas:

AuthController: é a recepção, que recebe o usuário e a senha.
TokenService: é a máquina que cria os crachás digitais (tokens JWT).
FiltroJwt: é o segurança que verifica o crachá em cada requisição protegida.
SecurityConfig: define quais áreas são públicas e quais exigem autenticação ou perfil ADMIN.

O JWT funciona como um crachá digital temporário. Ele não é a senha do usuário, mas uma autorização que permite acessar determinadas funcionalidades enquanto estiver válido.

6.4. Como testar o login no Postman?

1. Envie uma requisição POST:

http
POST http://localhost:8080/auth/login


2. No Body, selecione raw → JSON:

json
{
  "usuario": "admin",
  "senha": "admin123"
}


3. Copie o campo token retornado pela API.

4. Para acessar as rotas protegidas:

Entre na aba Authorization.
Selecione Bearer Token.
Cole o token recebido no login.

Também é possível testar com o usuário colaborador para verificar as permissões de acesso.

📂 7. Estrutura de pastas e camadas
Pasta	Responsabilidade
model	Entidades do sistema
repository	Consultas e operações no banco
service	Regras de negócio
controller	Rotas e recebimento das requisições
dto	Objetos de transferência de dados
security	JWT e controle de permissões
exception	Tratamento de erros
resources/static	Interface web
relatorio-python	Scripts de geração de relatórios
Caminho de uma requisição
text
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

📖 8. Principais rotas da API
Método	Rota	Permissão
POST	/auth/login	Público
GET	/equipamentos	Logado
GET	/equipamentos/{id}	Logado
POST	/equipamentos	ADMIN
PUT	/equipamentos/{id}	ADMIN
DELETE	/equipamentos/{id}	ADMIN
GET	/colaboradores	ADMIN
GET	/colaboradores/{id}	ADMIN
POST	/colaboradores	ADMIN
GET	/colaboradores/{id}/emprestimos	Logado
POST	/emprestimos	ADMIN
PUT	/emprestimos/{id}/devolucao	ADMIN
GET	/emprestimos	Logado
GET	/emprestimos?atrasados=true	Logado
📝 9. Exemplos de requisições JSON
Cadastro de equipamento
json
{
  "patrimonio": "NB-0001",
  "nome": "Notebook Dell",
  "tipo": "NOTEBOOK"
}

Cadastro de colaborador
json
{
  "nome": "Ana Souza",
  "email": "ana@empresa.com",
  "cep": "01310100",
  "numero": "1000"
}

Registro de empréstimo
json
{
  "equipamentoId": 1,
  "colaboradorId": 1
}

🔄 10. Fluxo detalhado: registrar empréstimo (POST /emprestimos)

Esta seção apresenta o caminho completo da operação, desde o clique do usuário na interface até a resposta exibida no navegador.

O fluxo considera autenticação, validação JWT, autorização por perfil, validação dos dados, regras de negócio, persistência no banco e tratamento de erros.

10.1. Diagrama de sequência
mermaid
sequenceDiagram
    autonumber
    actor U as Usuário
    participant JS as app.js (navegador)
    participant FJ as FiltroJwt
    participant TS as TokenService
    participant AZ as AuthorizationFilter
    participant DS as DispatcherServlet
    participant EC as EmprestimoController
    participant ES as EmprestimoService
    participant QS as EquipamentoService
    participant CS as ColaboradorService
    participant RP as Repositories
    participant DB as H2 (dados/emprestimos)
    participant TE as TratadorDeErros

    U->>JS: Escolhe equipamento e colaborador e clica em Registrar
    JS->>JS: registrarEmprestimo() monta equipamentoId e colaboradorId
    JS->>JS: chamarApi() lê JWT do sessionStorage
    JS->>FJ: POST /emprestimos com Bearer Token

    alt Sem cabeçalho Bearer
        FJ->>AZ: filterChain.doFilter() sem autenticação
        AZ-->>JS: 401 Autenticação necessária
    else Com cabeçalho Bearer
        FJ->>TS: ler(token)
        alt Assinatura inválida, token malformado ou expirado
            TS-->>FJ: JwtException
            FJ-->>JS: 401 Token inválido ou expirado
        else Token válido
            TS-->>FJ: Claims (subject = usuário, perfil)
            FJ->>FJ: SecurityContextHolder recebe ROLE + perfil
            FJ->>AZ: filterChain.doFilter()
            alt Perfil COLABORADOR
                AZ-->>JS: 403 Você não tem permissão para esta operação
            else Perfil ADMIN
                AZ->>DS: Requisição autorizada
                DS->>DS: Jackson converte JSON em EmprestimoRequest
                DS->>DS: @Valid verifica campos obrigatórios

                alt JSON inválido ou ID nulo
                    DS->>TE: HttpMessageNotReadableException ou MethodArgumentNotValidException
                    TE-->>JS: 400 Erro de requisição
                else Dados válidos
                    DS->>EC: emprestar(dados)
                    EC->>ES: emprestar(dados)
                    Note over ES,DB: @Transactional abre a transação

                    ES->>QS: buscar(equipamentoId)
                    QS->>RP: equipamentoRepository.findById(id)
                    RP->>DB: SELECT equipamento WHERE id = ?

                    alt Equipamento não existe
                        QS->>TE: NaoEncontradoException
                        TE-->>JS: 404 Equipamento não encontrado
                    else Equipamento encontrado
                        QS-->>ES: Equipamento

                        ES->>CS: buscar(colaboradorId)
                        CS->>RP: colaboradorRepository.findById(id)
                        RP->>DB: SELECT colaborador WHERE id = ?

                        alt Colaborador não existe
                            CS->>TE: NaoEncontradoException
                            TE-->>JS: 404 Colaborador não encontrado
                        else Colaborador encontrado
                            CS-->>ES: Colaborador

                            alt Equipamento indisponível
                                ES->>TE: ConflitoException
                                TE-->>JS: 409 Equipamento indisponível
                            else Equipamento disponível
                                ES->>ES: setDisponivel(false)
                                ES->>ES: dataEmprestimo = hoje
                                ES->>ES: dataPrevistaDevolucao = hoje + 14 dias
                                ES->>RP: emprestimoRepository.save(emprestimo)
                                RP->>DB: INSERT INTO emprestimo
                                DB-->>RP: ID gerado
                                RP-->>ES: Emprestimo salvo
                                ES->>DB: Commit e atualização do equipamento
                                ES-->>EC: Emprestimo
                                EC-->>DS: Emprestimo (HTTP 201 Created)
                                DS-->>JS: JSON do empréstimo
                            end
                        end
                    end
                end
            end
        end
    end

    Note over JS: chamarApi() trata a resposta

    alt Status 401
        JS->>JS: encerrarSessao() limpa sessionStorage
        JS-->>U: Sua sessão expirou ou não é válida. Entre novamente.
    else Status 400, 403, 404 ou 409
        JS->>JS: Lê dados.message e lança Error
        JS-->>U: mostrarMensagem(message, error)
    else Status 201
        JS->>JS: formulario.reset()
        JS-->>U: Empréstimo registrado com sucesso
        JS->>JS: carregarDadosEmprestimos() e carregarPainel()
    end

10.2. Regras de negócio do empréstimo
O usuário precisa estar autenticado.
Apenas ADMIN pode registrar empréstimos.
O equipamento informado precisa existir.
O colaborador informado precisa existir.
O equipamento precisa estar disponível.
A data do empréstimo é definida automaticamente.
O prazo padrão de devolução é de 14 dias.
Após o registro, o equipamento passa a ficar indisponível.
A operação é executada dentro de uma transação.
10.3. Possíveis respostas da API
Código HTTP	Situação
201	Empréstimo registrado com sucesso
400	JSON inválido ou dados obrigatórios ausentes
401	Usuário não autenticado ou JWT inválido
403	Usuário sem permissão
404	Equipamento ou colaborador não encontrado
409	Equipamento indisponível
🧩 11. Diagrama de classes (Mermaid UML)

O diagrama mostra controllers, services, repositories, entidades, DTOs, segurança e tratamento de erros.

As setas indicam dependências entre classes. As associações Emprestimo–Colaborador e Emprestimo–Equipamento representam os relacionamentos persistidos no modelo.

mermaid
classDiagram
direction LR

class EmprestimosApplication {
  +main(String[] args)
}

class AuthController {
  -AuthenticationManager authenticationManager
  -TokenService tokenService
  +login(LoginRequest) TokenResposta
}

class ColaboradorController {
  -ColaboradorService colaboradorService
  -EmprestimoService emprestimoService
  +listar() List~Colaborador~
  +listarEmprestimos(Long) List~Emprestimo~
  +buscar(Long) Colaborador
  +cadastrar(ColaboradorRequest) Colaborador
}

class EquipamentoController {
  -EquipamentoService equipamentoService
  +listar(TipoEquipamento, Boolean) List~Equipamento~
  +buscar(Long) Equipamento
  +cadastrar(EquipamentoRequest) Equipamento
  +atualizar(Long, AtualizarEquipamentoRequest) Equipamento
  +remover(Long) ResponseEntity
}

class EmprestimoController {
  -EmprestimoService emprestimoService
  +listar(Boolean) List~Emprestimo~
  +emprestar(EmprestimoRequest) Emprestimo
  +devolver(Long) Emprestimo
}

class ColaboradorService {
  -ColaboradorRepository colaboradorRepository
  -ViaCepService viaCepService
  +listar() List~Colaborador~
  +buscar(Long) Colaborador
  +cadastrar(ColaboradorRequest) Colaborador
}

class EquipamentoService {
  -EquipamentoRepository equipamentoRepository
  -EmprestimoRepository emprestimoRepository
  +listar(TipoEquipamento, Boolean) List~Equipamento~
  +buscar(Long) Equipamento
  +cadastrar(EquipamentoRequest) Equipamento
  +atualizar(Long, AtualizarEquipamentoRequest) Equipamento
  +remover(Long) void
}

class EmprestimoService {
  -EmprestimoRepository emprestimoRepository
  -EquipamentoRepository equipamentoRepository
  -ColaboradorService colaboradorService
  +listar(Boolean) List~Emprestimo~
  +buscar(Long) Emprestimo
  +listarDoColaborador(Long) List~Emprestimo~
  +emprestar(EmprestimoRequest) Emprestimo
  +devolver(Long) Emprestimo
}

class ViaCepService {
  -RestClient restClient
  +buscar(String) ViaCepResposta
}

class ColaboradorRepository {
  <<interface>>
  +existsByEmail(String) boolean
}

class EquipamentoRepository {
  <<interface>>
  +findByTipo(TipoEquipamento) List~Equipamento~
  +findByDisponivel(Boolean) List~Equipamento~
  +findByTipoAndDisponivel(TipoEquipamento, Boolean) List~Equipamento~
  +findByIdForUpdate(Long) Optional~Equipamento~
}

class EmprestimoRepository {
  <<interface>>
  +existsByEquipamentoId(Long) boolean
  +findByColaboradorId(Long) List~Emprestimo~
  +findByDataDevolucaoIsNullAndDataPrevistaDevolucaoBefore(LocalDate) List~Emprestimo~
}

class Colaborador {
  -Long id
  -String nome
  -String email
  -String cep
  -String numero
  -String logradouro
  -String bairro
  -String cidade
  -String uf
}

class Equipamento {
  -Long id
  -String patrimonio
  -String nome
  -TipoEquipamento tipo
  -boolean disponivel
}

class Emprestimo {
  -Long id
  -LocalDate dataEmprestimo
  -LocalDate dataPrevistaDevolucao
  -LocalDate dataDevolucao
  +isAtrasado() boolean
}

class TipoEquipamento {
  <<enumeration>>
  NOTEBOOK
  MONITOR
  HEADSET
  TECLADO
  MOUSE
  OUTRO
}

class LoginRequest {
  <<record>>
  +String usuario
  +String senha
}

class TokenResposta {
  <<record>>
  +String token
  +String perfil
  +String usuario
}

class ColaboradorRequest {
  <<record>>
  +String nome
  +String email
  +String cep
  +String numero
}

class ViaCepResposta {
  <<record>>
  +String logradouro
  +String bairro
  +String localidade
  +String uf
  +Boolean erro
}

class EquipamentoRequest {
  <<record>>
  +String patrimonio
  +String nome
  +TipoEquipamento tipo
}

class AtualizarEquipamentoRequest {
  <<record>>
  +String nome
  +TipoEquipamento tipo
}

class EmprestimoRequest {
  <<record>>
  +Long equipamentoId
  +Long colaboradorId
}

class SecurityConfig {
  +passwordEncoder() PasswordEncoder
  +userDetailsService() UserDetailsService
  +authenticationManager() AuthenticationManager
  +filterChain() SecurityFilterChain
}

class FiltroJwt {
  -TokenService tokenService
  +doFilterInternal(request, response, chain) void
}

class TokenService {
  -SecretKey chave
  -long validadeMs
  +gerar(String, String) String
  +ler(String) Claims
}

class SwaggerConfig {
  +documentacaoApi() OpenAPI
}

class TratadorDeErros {
  +naoEncontrado() ErroResposta
  +conflito() ErroResposta
  +validacao() ErroResposta
  +cepNaoEncontrado() ErroResposta
  +loginInvalido() ErroResposta
  +integracao() ErroResposta
  +tipoInvalido() ErroResposta
  +jsonInvalido() ErroResposta
  +integridade() ErroResposta
}

class ErroResposta {
  <<record>>
  +String message
}

class CepNaoEncontradoException
class ConflitoException
class IntegracaoException
class NaoEncontradoException

EmprestimosApplication ..> SecurityConfig : inicia contexto

AuthController --> TokenService : gera JWT
AuthController ..> LoginRequest : recebe
AuthController ..> TokenResposta : devolve

SecurityConfig --> FiltroJwt : registra filtro
SecurityConfig --> TokenService : injeta
FiltroJwt --> TokenService : valida JWT

ColaboradorController --> ColaboradorService
ColaboradorController --> EmprestimoService
EquipamentoController --> EquipamentoService
EmprestimoController --> EmprestimoService

ColaboradorService --> ColaboradorRepository
ColaboradorService --> ViaCepService
ColaboradorService ..> ColaboradorRequest
ColaboradorService ..> ViaCepResposta

ViaCepService ..> ViaCepResposta : converte resposta externa

EquipamentoService --> EquipamentoRepository
EquipamentoService --> EmprestimoRepository
EmprestimoService --> EmprestimoRepository
EmprestimoService --> EquipamentoRepository
EmprestimoService --> ColaboradorService

EquipamentoRepository ..> Equipamento
ColaboradorRepository ..> Colaborador
EmprestimoRepository ..> Emprestimo

Emprestimo "0..*" --> "1" Colaborador : pertence a
Emprestimo "0..*" --> "1" Equipamento : utiliza
Equipamento --> TipoEquipamento : tipo

EquipamentoService ..> EquipamentoRequest
EquipamentoService ..> AtualizarEquipamentoRequest
EmprestimoService ..> EmprestimoRequest

TratadorDeErros ..> ErroResposta : monta resposta
TratadorDeErros ..> CepNaoEncontradoException
TratadorDeErros ..> ConflitoException
TratadorDeErros ..> IntegracaoException
TratadorDeErros ..> NaoEncontradoException

SwaggerConfig ..> TokenService : documenta esquema Bearer JWT

📊 12. Relatório automático com Python

O projeto possui uma pasta chamada relatorio-python, responsável pela geração de relatórios gerenciais em Excel.

12.1. Tecnologias utilizadas
Python 3.
Requests.
Pandas.
OpenPyXL.
12.2. Como executar?

1. Acesse a pasta:

bash
cd relatorio-python


2. Instale as dependências:

bash
pip install -r requirements.txt


3. Execute o programa:

bash
python gerar_relatorio.py

12.3. Resultado

O programa gera o arquivo:

text
relatorio_emprestimos.xlsx


A planilha contém duas abas:

Atrasados: apresenta os empréstimos que estão fora do prazo.
Emprestados por tipo: apresenta a quantidade de equipamentos emprestados por categoria.
👥 13. Integrantes da equipe
Integrante	Responsabilidade	GitHub
Lucas	Tech Lead / Arquitetura	![GitHub](https://img.shields.io/badge/GitHub-Eluriomilmar-181717?logo=github)
Cleber	Organização / Segurança / Python	![GitHub](https://img.shields.io/badge/GitHub-bin--ribeiro-181717?logo=github)
Thiago	Segurança JWT / ViaCEP	![GitHub](https://img.shields.io/badge/GitHub-thiagorosabio-181717?logo=github)
Lara	QA	![GitHub](https://img.shields.io/badge/GitHub-Lara--Karolinne-181717?logo=github)
Filipe	README / Apresentação	![GitHub](https://img.shields.io/badge/GitHub-FilipeSantos34-181717?logo=github)
🔎 14. Observações importantes sobre o código
SecurityConfig libera a rota /auth/login e recursos estáticos; as demais rotas seguem as regras de autenticação e perfil.
FiltroJwt valida o token Bearer antes de chegar ao controller.
O login utiliza usuários configurados em memória pelo InMemoryUserDetailsManager.
ColaboradorService consulta o ViaCEP e copia logradouro, bairro, localidade e UF para a entidade Colaborador.
Emprestimo possui relacionamento ManyToOne com Colaborador e Equipamento.
Cada empréstimo referencia um colaborador e um equipamento; cada colaborador ou equipamento pode aparecer em vários registros de empréstimo ao longo do tempo.
Os repositórios são interfaces Spring Data JPA; o Spring fornece suas implementações em tempo de execução.
app.js é a camada do navegador e não uma classe Java. Ele chama a API, guarda o token em sessionStorage e atualiza a interface.
Os diagramas representam o código-fonte analisado e não devem ser interpretados como funcionalidades que não estejam implementadas.
🎯 15. Objetivo do projeto

Desenvolver uma solução que facilite o gerenciamento de equipamentos, garantindo maior controle sobre empréstimos, devoluções e disponibilidade dos recursos.

Além disso, o projeto proporciona a aplicação prática de conhecimentos em desenvolvimento Java, APIs REST, banco de dados, segurança, integração com serviços externos, testes automatizados e geração de relatórios.

<p align="center">

Projeto desenvolvido em equipe para aplicação prática de conhecimentos em desenvolvimento de software.

</p>
