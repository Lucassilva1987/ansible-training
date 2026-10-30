# Ansible Inventory: Grupos e Children

## Objetivo

Estudar como organizar servidores em grupos no Ansible e como
utilizar o recurso `:children` para criar uma hierarquia de grupos.

## Conceitos estudados

- Organização de hosts em grupos.
- Agrupamento por região e ambiente.
- Utilização de grupos filhos com `:children`.
- Criação de um grupo principal que reúne outros grupos.

## Validando o inventário

Para visualizar a hierarquia dos grupos, execute:

```bash
ansible-inventory -i inventory/inventory.ini --graph

Aprendizado
O uso de :children permite agrupar grupos existentes,
facilitando a organização e a seleção de servidores
em automações com Ansible.


### 4. E como isso vai para o GitHub?

Quando terminar, sua pasta estará organizada e pronta para versionamento.

Só tem um detalhe: **esse inventário é um exemplo de configuração, não uma automação de servidores ainda.** Ele organiza os hosts, mas não executa nenhuma tarefa neles.

Para validar a estrutura, você pode executar o comando de inventário em um ambiente com o Ansible instalado.