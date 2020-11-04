[Início](/readme.md) &raquo; Autenticação &raquo; [Versão 2.0 - Integração de Autenticação](/auth/cognito/readme.md) &raquo; [Federação](/auth/cognito/federation.md) 

# Federação

Quando o cliente tem sua própria base de usuários e quer utiliza-la para acessar os catalogos da LTM, é necessário desenvolver uma integração com o SSO da LTM, essa integração é chamada de federação.

## Tipos de Federação
Há 3 métodos disponiveís para a integração com o catálogo, que são protocolo Open Id Connect, protocolo SAML e integração por Middleware. Para saber qual a melhor opção, é necessário entender o cenário do cliente e juntamento com o time técnico da LTM definir o melhor modelo.

Documentação de referência para a integração Open Id Connect e SAML:

https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-identity-federation.html

## Open Id Connect

Neste cenário utilizamos o mecanismo de federação do Amazon Cognito para conectar a instância do user pool do catálogo com o identity provider do parceiro. Este cenário considera que o parceiro tenha um identity server que suporte o protocolo Open Id Connect. 

Neste caso, as credenciais do usuario não existem no Cognito, apenas uma referência para o usuário externo é criada e o processo de autenticação envolve o redirecionamento do usuário para o identity server do parceiro.

Documentação de referência para a integração:

https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-oidc-idp.html

## SAML
Neste cenário o processo de federação é muito parecido com o anterior alterando apenas o protocolo para o SAML. Essa integração geralmente é utilizada em aplicações enterprise como Ad ou SalesForce.

Documentação de referência para a integração:

https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-saml-idp.html

## Middleware
Modelo de integração criado para os casos em que o parceiro não expõe uma interface que se comunique com nenhum dos dois protocolos anteriores. 

Neste cenário o parceiro cria um token JWT contendo as informações do usuário, o assina utilizando uma chave privada compartilhada e em seguida redireciona o usuário para um middleware que valida o token JWT. 

Após validar a autenticidade do token, o middleware gera uma sessão autenticada para o usuario e inicia o fluxo de federação com o Cognito utilizando o protocolo Open Id Connect.

![Federation](/images/federation-middleware-diagram.png)

## Fluxo Middleware
Uma chave privada deve ser compartilhada entre a aplicação cliente e o Middleware, pois ambos devem ser capazes de validar o token JWT gerado pela aplicação cliente.

Durante a autenticação os dados do participante são obtidos da api CloudLoyalty para criar a sessão no Middleware. Para isso é necessário a geração de um token [client credentials](/auth/cognito/client_credentials.md). Com o token em mãos o Middleware chama a api e obtêm os dados necessários.

![Federation](/images/federation-get-participant-info-diagram.png)

## Importante
O Middleware internamente nos ambientes da LTM também é conhecido como SSO ou  Middleware SSO, porém não devemos confundir o **conceito** SSO (Single Sign-On) com essa **aplicação** comumente conhecida como SSO.
