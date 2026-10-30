# Provisionamento de Servidor Web com Ansible

## Sobre o Projeto
Este laboratório é um exercício prático de *Infrastructure as Code (IaC)* focado na instalação, configuração e inicialização de um servidor web Apache em uma máquina virtual (Rocky Linux) local.

O projeto foi construído de forma evolutiva: partindo de uma base de provisionamento simples, foi incrementado com padrões profissionais de automação para simular um ambiente corporativo real.

## Padrões e Boas Práticas Aplicadas
* **Separação de Variáveis:** Utilização da estrutura `group_vars/webservers.yml` para isolar dados, mantendo o playbook limpo e facilitando a manutenção.
* **Templates Dinâmicos:** Uso do Jinja2 (`index.html.j2`) para gerar páginas web que absorvem dados (facts) reais do servidor durante a execução.
* **Handlers:** Configuração de gatilhos inteligentes para reiniciar o serviço do Apache apenas quando houver alterações de configuração, garantindo a idempotência.
* **Módulos do Sistema:** Gestão do serviço de firewall local através da coleção `ansible.posix`.

## Estrutura do Repositório
- `ansible.cfg`: Configurações de comportamento do Ansible (inventário padrão, desativação de checagem de chaves SSH).
- `inventory`: Inventário declarativo contendo os IPs e credenciais de acesso do *Managed Node*.
- `apache.yml`: O Playbook principal contendo as *tasks*.
- `index.html.j2`: O template HTML dinâmico.
- `group_vars/webservers.yml`: Arquivo contendo as variáveis injetadas automaticamente no grupo de servidores web.

## Como Executar
Com o ambiente virtual do Python ativo e o Ansible configurado, execute:
```bash
ansible-playbook apache.yml