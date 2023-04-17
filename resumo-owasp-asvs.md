# Resumo OWASP ASVS

Com objetivo de resumir o <a href="https://owasp.org/www-project-application-security-verification-standard/">OWASP ASVS</a> segue a lista:

## **Arquitetura**

1. **Durante o planejamento há uma forte preocupação com a definição de requisitos de segurança.**
2. **Implementa controles de segurança centralizados, de forma a evitar a ausência, duplicidade ou insegurança.**
3. **Possui uma lista de verificação de codificação segura, diretriz ou política para todos os desenvolvedores e testadores.**
    1. Boas práticas de código seguro visando melhores formas de evitar os ataques mais conhecidos (OWASP TOP 10)
    2. Os testes são efetivos e abrangem os requisitos de segurança.
4. **Utiliza contas de sistema operacional de baixo privilégio para todos os componentes de aplicativos, serviços e servidores.**
5. **A comunicação entre componentes (API's, middlewares, etc...) são autenticados e criptografados.**
6. **É notificado sobre componentes (bibliotecas, frameworks, etc…) desatualizados ou inseguros através de uma Integração Contínua.**
7. **Não utiliza tecnologias não suportadas, inseguras ou obsoletas do lado do cliente (Ex.: plug-ins NSAPI, Flash, Shockwave, ActiveX, Silverlight, NACL ou applets Java do lado do cliente).**
8. **Verifique se os processos de criação e implantação do aplicativo são executados de maneira segura e repetível, como automação de CI/CD, gerenciamento de configuração automatizada e scripts de implantação automatizada.**
9. **O aplicativo pode detectar e alertar sobre números anormais de solicitações, como por IP, usuário, total por hora ou dia ou o que fizer sentido para o aplicativo.**
10. **O aplicativo possui controles antiautomação suficientes para detectar e proteger contra exfiltração de dados, solicitações excessivas de lógica de negócios, uploads excessivos de arquivos ou ataques de negação de serviço.**
11. **O aplicativo possui alertas configuráveis quando ataques automatizados ou atividades incomuns são detectados.**

## **Autenticação**

1. **As senhas escolhidas pelos usuários possuem pelo menos menos 8 caracteres.**
2. **A funcionalidade de alteração de senha exige a senha atual.**
3. **Possui controles anti-automação para evitar testes automatizados na busca de credenciais válidas (força bruta, bloqueio de contas).**
    1. Tela de login garante que requisições enviadas em um período de tempo não humano, são bloqueadas. (Ex.: 2 solicitações em menos de 1 segundo)
    2. Cuidado com mitigações que bloqueiam usuários, podem tornar a aplicação indisponível (DoS).
4. **Autenticação utilizando SMS ou email são feitos apenas para um segundo fator de autenticação. Pois são autenticadores fracos e não podem ser utilizados como um único fator de autenticação. Cenários não seguros:**
    1. Autenticação feita utilizando um link enviado para o email da vítima.
    2. Autenticação feita através de um código enviado via SMS.
5. **Notificações de alerta são enviadas aos usuários após atualizações de autenticação.**
    1. Alteração de senha.
    2. Alteração de email.
    3. Logins de locais desconhecidos.
6. **Utiliza multiplo fator de autenticação.**
7. **As senhas estão armazenadas em um formato resistente a ataques offline. (Salt, Bcrypt, Script, Argon2, etc...)**
8. **Não utiliza dicas de senha (Perguntas secretas).**
9. **A recuperação de senha não revela a senha atual.**
10. **Links e códigos de recuperação podem ser utilizados apenas uma vez e possuem uma data de expiração.**
11. **Utiliza um único mecanismo de autenticação, que pode ser estendido para incluir autenticação forte (2FA, MFA, etc) e tem registro e monitoramento suficiente para detectar abusos ou violações de conta. (Tal como o item 2)**

## **Sessão**

1. **Cookie**
    1. Possuem os atributos necessários:
        - HttpOnly: Os cookies são acessados e modificados apenas pelo Navegador.
        - Secure: Os cookies são trafegados epenas em domínios HTTPS.
        - SameSite: Os cookies são trafegados apenas para o mesmo domínio.
    2. Ao efetuar logout a sessão é completamente destruída, de forma que o botão voltar do Navegador não retome uma sessão válida e autenticada.
    3. Todas as sessões ativas são destruídas na recuperação ou troca de senha bem sucedida.
    4. Impõe um forte mecanismo de prevenção de Cross-Site Request Forgery (CSRF).
        - [https://www.treinaweb.com.br/blog/cross-site-request-forgery-csrf-e-abordagens-para-mitiga-lo](https://www.treinaweb.com.br/blog/cross-site-request-forgery-csrf-e-abordagens-para-mitiga-lo)
2. **Token Transparente (Ex.: JWT).**
    1. Não são revelados em URL’s, mensagens de erro e nem logados.
    2. Não armazena informações sensíveis (CPF, etc…).
    3. Token expira em um tempo adequado.
    4. Utiliza algoritmo forte como HS256 (Simétrico para o caso de um servidor) ou RSA (Assimétrico para casos de mais de um servidor) e algoritmo “None” está desabilitado.
    5. Evitar o uso demasiado de claims, evitando que o token fique muito grande.
    6. Somente algoritmos Validar se o algoritmo “None” não está habilitado.
3. **Token Opaco.**
    1. Não são revelados em URL’s, mensagens de erro e nem logados.
    2. Um novo token é gerado durante uma troca ou recuperação de senha e os demais tokens são invalidados.
    3. Logout ou expiração invalidam o token de sessão.
    4. Tokens não são reutilizados durante uma nova autenticação.

## **Controle de Acesso**

1. **Controle de acesso não é implementado somente no lado do cliente.**
2. **Princípios do menor privilégio e negar por padrão são seguidos.**
    - Os usuários só devem ser capazes de acessar funções, arquivos de dados, URLs, controladores, serviços e outros recursos, para os quais possuem autorização específica e não estando apenas autenticados.
    - Novos usuários ou funções começam com o mínimo ou nenhuma permissão até que o acesso seja explicitamente atribuído.
3. **Verifique se os dados confidenciais e APIs estão protegidos contra ataques diretos a objetos (IDOR).** 
    1. Ex: Usuário 1 não deve conseguir ver, criar, alterar ou excluir dados do usuário 2.
    2. [https://www.varonis.com/pt-br/blog/o-que-e-idor-insecure-direct-object-reference](https://www.varonis.com/pt-br/blog/o-que-e-idor-insecure-direct-object-reference)
4. **Verifique se a listagem de diretórios (Página “Index of /”, S3 Bucke, etc…) está desativada, a menos que seja deliberadamente desejada.**

## **Validação**

1. **Dados de entrada são sanitizados para impor medidas de segurança, como caracteres e comprimento permitidos.**
    1. Verifique se os inputs da aplicação (Forms, endpoints, etc) possuem algum schema e/ou mecanismo que faça a validação dos tipos de cada campo, bem como os caracteres permitidos, tamanho, se seguem algum padrão (Para dados como número de documentos, número de cartão de crédito, CEP, etc) e em casos de campos livres de texto, escaping de HTML;
2. **Para inputs que tem a intenção de receber entradas de HTML (Ao usar editores de texto WYSIWYG por exemplo), deve-se usar um recurso para filtrar as tags HTML de maneira a permitir que apenas os elementos esperados sejam renderizados.** **************************************************************************************************************************************************************O mesmo se aplica para aplicações que permitem inputs com Markdown.**************************************************************************************************************************************************************
    1. Ex: Um input produzido por um editor WYSIWYG pode conter tags como `<b>` ou `<i>` para formatação de texto. Estas tags devem ser renderizadas como elementos HTML. Outras tags como `<script>` devem ser escapadas usando `&lt;script&gt;`.
3. **O aplicativo evita o uso de eval() ou outros recursos dinâmicos de execução de código.**
4. **Entradas são sanatizadas para evitar ataques de Injeção (CSS, XML, JavaScript, SVG).** 
    1. https://www.youtube.com/watch?v=Loomvo-3LPk
5. **Consultas em bancos de dados são feitas sem concatenação de strings, utilizando Prepared Statement, ORMs utilizando binding, ou recursos como os repositories do Spring Data.**
6. **Ao fazer uso de ORMs ou ferramentas semelhantes,** i**nterfaces de entrada, como controllers de endpoints REST, devem possuir validações para que não sejam alterados campos** 
    1. A classe *Funcionario* que representa o body de um endpoint REST não é a mesma classe *Funcionario* que o framework ORM usa para mapear as entradas do banco de dados.
7. **Parâmetros enviados para comandos de sistema operacional são sanatizados.**
8. **Tem proteção contra ataques de inclusão de arquivo local (LFI) ou inclusão de arquivo remoto (RFI).**
    1. Referência, LFI: [https://knowledge-base.secureflag.com/vulnerabilities/inadequate_input_validation/file_inclusion_vulnerability.html](https://knowledge-base.secureflag.com/vulnerabilities/inadequate_input_validation/file_inclusion_vulnerability.html)
    2. Referência, RFI: [https://www.hackingarticles.in/comprehensive-guide-on-remote-file-inclusion-rfi/](https://www.hackingarticles.in/comprehensive-guide-on-remote-file-inclusion-rfi/)
9. **Verifique se o aplicativo restringe corretamente os analisadores XML para usar apenas a configuração mais restritiva possível para evitar XXE.**
    1. Referência: [https://portswigger.net/web-security/xxe](https://portswigger.net/web-security/xxe)
10. **Se a aplicação recebe inputs de URLs externas do usuário ou faz requests baseada em informações de inputs de usuário, verifique se a aplicação implementa alguma estratégia de proteção contra ataques de SSRF, como validação de URLs, sanitização de nomes de arquivos, timeout para requests, etc.**

## **Criptografia**

1. **Dados privados regulamentados armazenados são criptografados enquanto estão em transito e em repouso, como informações de identificação pessoal (PII), informações pessoais confidenciais ou dados avaliados que provavelmente estão sujeitos ao GDPR da UE.**
    1. [https://www.ibm.com/br-pt/topics/pii](https://www.ibm.com/br-pt/topics/pii)
2. **Todos os números, nomes de arquivos, GUIDs e strings aleatórias são gerados de forma que não podem ser adivinhados por um invasor.**
3. **Verifique se uma solução de gerenciamento de segredos, como um cofre de chaves, é usada para criar, armazenar, controlar o acesso e destruir segredos com segurança.**
4. **TLS atualizado (v1.2+) é usado para toda a conectividade do cliente e não recorre a protocolos inseguros ou não criptografados.**

## **Erros**

1. **Aplicativo não registra credenciais ou informações sensíveis. Os tokens de sessão devem ser armazenados apenas em logs em um formato de hash irreversível.**
2. **O aplicativo registra eventos relevantes de segurança, incluindo eventos de autenticação bem-sucedidos e com falha, falhas de controle de acesso, falhas de desserialização e falhas de validação de entrada.**
3. **Todos os eventos estão protegidos contra injeção quando visualizados no software de visualização de log.**
4. **Verifique se os logs de segurança estão protegidos contra acesso e modificação não autorizados.**
5. **Uma mensagem genérica é exibida quando ocorre um erro inesperado ou sensível à segurança, possivelmente com um ID que a equipe de suporte pode usar para investigar.**
6. **Verifique se um manipulador de erro de "último recurso" está definido para capturar todas as exceções não tratadas.**

## **Dados**

1. **O aplicativo define cabeçalhos anti-cache suficientes para que dados confidenciais não sejam armazenados em cache nem em navegadores (no-cache, no-store, etc…).**
2. **Dados armazenados do lado do cliente (como LocalStorage, SessionStorage, IndexedDB, Cookies etc...) não contêm dados confidenciais ou PII.**
3. **Os usuários são informados de forma clara sobre a coleta e o uso das informações pessoais fornecidas e os usuários devem fornecer consentimento para o uso desses dados antes de serem usados de qualquer forma.**

## **Arquivos**

1. A **aplicação não aceita arquivos grandes que podem encher o armazenamento ou causar um ataque de negação de serviço (DoS).**
    1. Restringir o tamanho máximo de arquivos permitidos.
2. **Verifique se os arquivos compactados são verificados quanto a "zip bombs" - pequenos arquivos de entrada que serão descompactados em arquivos enormes, esgotando assim os limites de armazenamento de arquivos.**
3. **Verifique se os arquivos obtidos de fontes não confiáveis são validados como sendo do tipo esperado com base no conteúdo do arquivo.**