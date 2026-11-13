Filipe. O conteúdo está bom, mas a numeração ficou repetida, há alguns trechos fora de ordem e apareceram códigos como bashCopy e jsonCopy. Organizei abaixo em uma sequência mais profissional, mantendo a linguagem simples para o README.

📘 README - Sistema de Empréstimo de Equipamentos
1. Nome do projeto

Sistema de Empréstimo de Equipamentos

2. Qual problema queremos resolver?

Hoje, o controle de equipamentos pode ser feito por planilhas, o que pode causar problemas como:

Não saber exatamente quais equipamentos estão disponíveis;
Emprestar o mesmo equipamento para duas pessoas;
Não perceber rapidamente uma devolução atrasada.

Nosso projeto cria um sistema para organizar melhor os equipamentos e tornar o controle de empréstimos e devoluções mais simples e seguro.

3. O que o sistema faz?

O sistema permite:

Cadastrar equipamentos com patrimônio único;
Cadastrar colaboradores e preencher o endereço pelo ViaCEP;
Registrar empréstimos com prazo padrão de 14 dias;
Impedir que um equipamento já emprestado seja emprestado novamente;
Registrar a devolução e liberar o equipamento;
Consultar empréstimos;
Filtrar empréstimos atrasados;
Proteger rotas com JWT e perfis ADMIN e COLABORADOR;
Gerar uma planilha Excel com equipamentos atrasados e quantidade emprestada por tipo.
4. Tecnologias utilizadas
Tecnologia	Para que serve	Explicação simplesJava 17	Linguagem principal	O idioma usado para escrever as instruções do programa.
Spring Boot 3.5.6	Organiza e inicia a aplicação	Uma caixa de ferramentas para montar o sistema Java.
Maven	Dependências e compilação	Baixa as bibliotecas necessárias e prepara o projeto.
Spring Web	Rotas HTTP da API	Permite que a tela e o Postman conversem com o Java.
Spring Data JPA	Acesso ao banco	Ajuda a guardar, procurar e atualizar informações.
JPA / Hibernate	Mapeamento objeto-relacional	Liga as classes Java às tabelas do banco.
H2 Database	Banco de dados local	O armário onde ficam equipamentos, pessoas e empréstimos.
Spring Security	Segurança e permissões	O porteiro que confere quem pode entrar em cada lugar.
JWT	Autenticação por token	Um crachá digital temporário usado nas rotas protegidas.
JJWT 0.12.6	Criar e validar JWT	Biblioteca que cria e confere os tokens.
BCrypt	Proteção de senhas	Transforma a senha em uma representação protegida.
Bean Validation	Validação de dados	Confere campos obrigatórios, e-mail e formato do CEP.
RestClient	Chamadas HTTP externas	O telefone usado para consultar o ViaCEP.
ViaCEP	Buscar endereço	Retorna rua, bairro, cidade e estado pelo CEP.
Springdoc OpenAPI / Swagger UI	Documentar e testar a API	Manual interativo para experimentar as rotas.
JUnit 5	Testes automatizados	Confere se as regras estão funcionando corretamente.
Spring Boot Test	Teste integrado	Ajuda a verificar se o sistema consegue iniciar.
Spring Security Test	Testes de segurança	Ajuda a testar usuários e permissões.
HTML	Estrutura da página	O esqueleto da tela: títulos, campos e botões.
JavaScript	Comunicação da tela com a API	Envia pedidos para a API ao clicar nos botões.
Python 3	Relatório automático	Cria a planilha de gestão.
Requests	Requisições HTTP em Python	Permite ao relatório solicitar informações da API.
Pandas	Organização dos dados	Separa atrasados e conta equipamentos por tipo.
OpenPyXL	Criação do Excel	Escreve os resultados no arquivo .xlsx.
Postman	Testar rotas	Controle remoto para enviar pedidos para a API.
VS Code / NetBeans / IntelliJ	Editores	Programas usados para abrir, editar e executar o projeto.
5. Como o sistema funciona por dentro?

De forma simples:

Usuário
   ↓
Tela
   ↓
Controller
   ↓
Service
   ↓
Repository
   ↓
Banco H2


Também existem integrações externas:

Sistema → ViaCEP → Endereço

Sistema → API → Python → Relatório Excel


A arquitetura utiliza uma organização em camadas. As principais regras do sistema ficam na camada Service.

6. Como executar o projeto?
Extraia o arquivo ZIP do projeto.
Abra a pasta no VS Code ou importe o pom.xml no NetBeans/IntelliJ como projeto Maven.
Configure o JDK 17 ou superior.
Abra o terminal na pasta onde está o pom.xml.
Execute:
mvn spring-boot:run


Depois que o sistema iniciar:

Aplicação: http://localhost:8080
Swagger: http://localhost:8080/swagger-ui/index.html
H2 Console: http://localhost:8080/h2-console
Configuração do banco H2
JDBC URL: jdbc:h2:file:./dados/emprestimos
Usuário: sa
Senha: vazia

7. Usuários de demonstração
Perfil	Usuário	SenhaADMIN	admin	admin123
COLABORADOR	colaborador	colab123

⚠️ Esses usuários são apenas para demonstração. Essas senhas não devem ser utilizadas em um sistema publicado na internet.

8. Como funciona o JWT?
🎫 JWT explicado de forma simples

Imagine que a API é uma escola com salas protegidas:

AuthController - Recepção: recebe o usuário e a senha;
TokenService - Máquina de crachás: cria um crachá digital JWT depois do login e verifica se ele ainda é válido;
FiltroJwt - Segurança da porta: procura o crachá no campo Authorization;
SecurityConfig - Regras da escola: define quais portas são públicas e quais precisam de login ou perfil ADMIN.

O JWT não é a senha. Ele funciona como uma autorização temporária.

Se o token for alterado ou estiver vencido, a API não deve aceitá-lo.

Testando o JWT no Postman

Faça a requisição:

POST http://localhost:8080/auth/login


Endpoint de login

No Postman, escolha:

Body → raw → JSON

{
  "usuario": "admin",
  "senha": "admin123"
}


Depois:

Copie o campo token;
Abra outra requisição;
Vá em Authorization;
Escolha Bearer Token;
Cole o token.

Também é possível testar com:

Usuário: colaborador
Senha: colab123


O colaborador pode consultar informações, mas não cadastrar equipamentos.

9. Pastas e camadas
Pasta	Função	Comparação simplesmodel	Entidades	Moldes das fichas guardadas.
repository	Consultas ao banco	Ajudante que procura e guarda fichas.
service	Regras de negócio	Cérebro que decide se uma operação pode acontecer.
controller	Rotas da API	Atendente que recebe os pedidos.
dto	Dados de entrada e saída	Fichas usadas para transportar informações.
security	JWT e permissões	Porteiro e crachás.
exception	Tratamento de erros	Explica o que deu errado.
resources/static	Tela web	Página exibida no navegador.
relatorio-python	Relatório	Prepara a planilha Excel.
Caminho normal de uma solicitação
Tela/Postman → Controller → Service → Repository → Banco de dados

10. Rotas principais 📖
Método	Rota	PermissãoPOST	/auth/login	Público
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
11. Exemplos JSON
Equipamento
{
  "patrimonio": "NB-0001",
  "nome": "Notebook Dell",
  "tipo": "NOTEBOOK"
}

Colaborador
{
  "nome": "Ana Souza",
  "email": "ana@empresa.com",
  "cep": "01310100",
  "numero": "1000"
}

Empréstimo
{
  "equipamentoId": 1,
  "colaboradorId": 1
}

12. Relatório Python 📊

Na pasta relatorio-python, execute:

pip install -r requirements.txt


Depois:

python gerar_relatorio.py


O programa gera o arquivo:

relatorio_emprestimos.xlsx


A planilha possui duas abas:

Atrasados
Emprestados por tipo
13. Integrantes 👥
Integrante	ResponsabilidadeLucas	Tech Lead / Arquitetura
Cleber	Organização / Segurança / Python
Thiago	Segurança: login e JWT / Integração com ViaCEP
Lara	QA
Filipe	README e roteiro da apresentação

O que ajustei: corrigi a numeração, retirei repetições e códigos estranhos como bashCopy, organizei as tabelas e coloquei o conteúdo em uma ordem mais natural para alguém que abrir o README pela primeira vez. Mantive as explicações simples, porque combina bem com a proposta de documentação do seu grupo.
