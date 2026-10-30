# Desenvolvimento de Playbooks Ansible

## Objetivo

Este laboratório tem como objetivo praticar a criação e execução de playbooks utilizando Ansible, explorando a configuração do ambiente, o gerenciamento de servidores e a automação de tarefas.

## Estrutura do projeto

```text
Desenvolvimento de playbooks/
├── ansible-navigator.yml
├── ansible.cfg
├── inventory
├── site.yml

```

## Arquivos

- `ansible.cfg`: configurações gerais do Ansible, como inventário, usuário remoto e elevação de privilégios.
- `ansible-navigator.yml`: configura o Execution Environment utilizado pelo Ansible Navigator.
- `inventory`: define os servidores que serão gerenciados.
- `site.yml`: playbook responsável pela configuração do servidor web Nginx..

## Conceitos praticados

- Configuração do Ansible e Ansible Navigator.
- Organização de inventários e grupos de servidores.
- Criação e execução de playbooks.
- Instalação de pacotes com `ansible.builtin.dnf`.
- Gerenciamento de arquivos com `ansible.builtin.copy`.
- Gerenciamento de serviços com `ansible.builtin.service`.
- Gerenciamento de firewall com `ansible.posix.firewalld`.
- Elevação de privilégios com `become`.

## Validação

Para verificar a sintaxe do playbook:

```bash
ansible-playbook site.yml --syntax-check
```

Para visualizar os servidores do inventário:

```bash
ansible-inventory --graph
```

## Aprendizado

Este laboratório faz parte dos meus estudos práticos de Ansible e automação de infraestrutura, com foco na compreensão dos módulos, organização dos arquivos e execução de tarefas em servidores remotos.