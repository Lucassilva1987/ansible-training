# Ansible Loops - Criação de Diretórios

## Objetivo

Este laboratório tem como objetivo praticar a utilização de loops no Ansible para automatizar a criação de múltiplos diretórios em servidores Linux.

A proposta é utilizar uma única tarefa para criar diferentes diretórios, evitando a repetição de código.

## Estrutura do projeto

```text
meu-projeto-loop/
├── onboarding.yml
└── README.md
```

## Sobre o playbook

O arquivo `onboarding.yml` contém uma tarefa responsável por padronizar a estrutura de diretórios do servidor.

Durante a execução, o Ansible percorre uma lista de caminhos e cria cada diretório utilizando o módulo `ansible.builtin.file`.

### Diretórios criados

- `/opt/scripts`
- `/opt/logs`
- `/opt/backups`

## Conceitos praticados

- `loop`: permite executar uma mesma tarefa para diferentes elementos de uma lista.
- `item`: variável especial utilizada para acessar o elemento atual do loop.
- `ansible.builtin.file`: módulo utilizado para gerenciar arquivos e diretórios.
- `state: directory`: garante que o diretório exista.
- `mode: "0755"`: define as permissões de acesso dos diretórios.
- `become: true`: permite executar tarefas com elevação de privilégios.

## Validação

Para verificar se o playbook possui erros de sintaxe:

```bash
ansible-playbook onboarding.yml --syntax-check
```

Para executar o playbook:

```bash
ansible-playbook onboarding.yml
```

A execução requer um inventário configurado e acesso aos servidores de destino.

## Aprendizado

Este exercício demonstra como utilizar loops para reduzir a repetição de tarefas e tornar os playbooks mais organizados, reutilizáveis e fáceis de manter.

Faz parte dos meus estudos práticos de Ansible e automação de infraestrutura.