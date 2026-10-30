# Ansible Vault - Proteção de Credenciais

## Objetivo

Este laboratório tem como objetivo praticar a utilização do Ansible Vault para proteger informações sensíveis utilizadas em playbooks.

O exercício demonstra como armazenar credenciais em arquivos criptografados e utilizá-las durante a execução de tarefas Ansible.

## Estrutura do projeto

```text
oracle-example/
├── deploy-oracle.yml
├── inventory
├── segredos.yml
└── README.md
```

## Sobre o playbook

O arquivo `deploy-oracle.yml` demonstra como carregar variáveis protegidas utilizando a diretiva `vars_files`.

O exemplo utiliza uma variável chamada `db_password`, armazenada no arquivo criptografado `segredos.yml`.

O módulo `ansible.builtin.debug` é utilizado apenas para demonstrar a execução de uma tarefa que utiliza informações protegidas.

### Proteção dos logs

O parâmetro `no_log: true` impede que os dados da tarefa sejam exibidos nos logs normais de execução do Ansible.

Essa configuração ajuda a evitar a exposição de informações sensíveis durante a execução do playbook.

## Trabalhando com Ansible Vault

### Criar um arquivo criptografado

```bash
ansible-vault create segredos.yml
```

### Editar um arquivo protegido

```bash
ansible-vault edit segredos.yml
```

### Visualizar o conteúdo

```bash
ansible-vault view segredos.yml
```

### Criptografar um arquivo existente

```bash
ansible-vault encrypt segredos.yml
```

### Executar o playbook

```bash
ansible-playbook -i inventory deploy-oracle.yml --ask-vault-pass
```

O Ansible solicitará a senha utilizada para desbloquear o arquivo criptografado.

## Conceitos praticados

- Ansible Vault: criptografia de informações sensíveis.
- `vars_files`: carregamento de variáveis externas.
- `no_log`: proteção contra exposição de informações nos logs.
- Organização de credenciais em arquivos separados.
- Utilização de variáveis protegidas em playbooks.

## Observações de segurança

- Não armazenar senhas reais em arquivos de texto puro.
- Não publicar senhas do Ansible Vault.
- Não versionar arquivos com credenciais descriptografadas.
- Utilizar credenciais fictícias em exercícios e demonstrações.
- O parâmetro `no_log` não substitui a criptografia do Ansible Vault.

## Aprendizado

Este laboratório demonstra como separar informações sensíveis da lógica dos playbooks, utilizando recursos do Ansible para melhorar a segurança e a organização das automações.

Faz parte dos meus estudos práticos de Ansible e automação de infraestrutura.