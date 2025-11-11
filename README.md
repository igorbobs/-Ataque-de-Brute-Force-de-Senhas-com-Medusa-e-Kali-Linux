# Projeto de Simulação de Ataques de Força Bruta

## Descrição

Este projeto demonstra a execução de ataques de força bruta em um ambiente de laboratório controlado, utilizando a distribuição Kali Linux e a ferramenta Medusa. Os alvos são os ambientes vulneráveis Metasploitable 2 e Damn Vulnerable Web Application (DVWA).

O objetivo principal é educacional: entender como esses ataques funcionam para, então, aprender e aplicar as melhores práticas de mitigação e defesa.

**Aviso:** As técnicas e ferramentas descritas neste projeto devem ser utilizadas apenas em ambientes de laboratório autorizados e para fins de estudo. A execução de ataques em sistemas sem permissão é ilegal.

## Estrutura do Repositório

- **/documentacao:** Contém a documentação detalhada do projeto, incluindo os passos de configuração do ambiente, os resultados dos testes e as recomendações de mitigação.
- **/wordlists:** Contém as listas de palavras (wordlists) utilizadas nos ataques de força bruta.
- **/scripts:** Contém scripts ou comandos utilizados para automatizar os ataques.

## Cenários de Ataque Simulados

1.  **Ataque de Força Bruta a um Serviço FTP:** Utilizando Medusa para descobrir credenciais de acesso no serviço FTP do Metasploitable 2.
2.  **Ataque de Força Bruta a um Formulário Web:** Automatizando tentativas de login no DVWA.
3.  **Password Spraying em um Serviço SMB:** Realizando um ataque de *password spraying* contra o serviço SMB do Metasploitable 2 após uma enumeração de usuários.
