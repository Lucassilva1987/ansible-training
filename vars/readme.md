# Ansible Group Vars - Configuração por Região

## Objetivo

Este laboratório tem como objetivo praticar a utilização de Group Vars no Ansible, permitindo definir variáveis específicas para diferentes grupos de servidores.

A proposta é utilizar um único playbook para consultar informações de servidores localizados em regiões diferentes, mantendo suas configurações organizadas em arquivos separados.

## Estrutura do projeto

```text
group-vars/
├── inventory
├── timezone.yml
│
└── group_vars/
    ├── brasil/
    │   └── vars.yml
    │
    └── europa/
        └── vars.yml
```

## Configuração do inventário

O arquivo `inventory` define dois grupos de servidores:

- `brasil`: contém o servidor `app01`.
- `europa`: contém o servidor `db01`.

## Configuração das variáveis

As variáveis são armazenadas em arquivos `vars.yml` dentro dos respectivos diretórios de `group_vars`.

### Brasil

```yaml
---
# Fuso horário utilizado pelos servidores do grupo brasil.
fuso_horario: America/Sao_Paulo
```

### Europa

```yaml
---
# Fuso horário utilizado pelos servidores do grupo europa.
fuso_horario: Europe/London
```

O Ansible identifica automaticamente o grupo de cada servidor e carrega as variáveis correspondentes.

## Sobre o playbook

O arquivo `timezone.yml` utiliza o módulo `ansible.builtin.debug` para exibir o fuso horário associado a cada servidor.

A variável `{{ fuso_horario }}` recebe automaticamente o valor definido no arquivo correspondente dentro de `group_vars`.

### Resultado esperado

- `app01`: America/Sao_Paulo
- `db01`: Europe/London

**Observação:** o playbook apenas consulta e exibe a variável. Ele não modifica o fuso horário real dos servidores.

## Conceitos praticados

- Organização de variáveis com `group_vars`.
- Associação de variáveis aos grupos do inventário.
- Utilização de variáveis em playbooks.
- Interpolação de variáveis com Jinja2 (`{{ }}`).
- Utilização do módulo `ansible.builtin.debug`.
- Reutilização de um único playbook para diferentes grupos de servidores.

## Validação

Para visualizar os grupos e servidores do inventário:

```bash
ansible-inventory -i inventory --graph
```

Para verificar a sintaxe do playbook:

```bash
ansible-playbook -i inventory timezone.yml --syntax-check
```

Para executar o playbook:

```bash
ansible-playbook -i inventory timezone.yml
```

A execução depende da conectividade com os servidores definidos no inventário.

## Aprendizado

Este laboratório demonstra como utilizar Group Vars para centralizar e organizar configurações específicas por grupo, tornando os playbooks mais reutilizáveis e fáceis de manter.

Faz parte dos meus estudos práticos de Ansible e automação de infraestrutura.