# Nexus Portal — Automação com Ansible Roles

Este projeto automatiza o provisionamento completo de servidores para o **Nexus Portal**, utilizando a arquitetura modular de **Roles do Ansible**, separação de ambientes (`dev` e `prod`) via `group_vars`, templates Jinja2 dinâmicos, handlers orientados a eventos (`listen`) e validações de pré e pós-deploy.

---

## Estrutura do Projeto

```text
.
├── group_vars/
│   ├── all.yml                # Variáveis globais (diretórios, RAM mínima, usuários)
│   ├── web_dev.yml            # Variáveis do ambiente de Desenvolvimento (porta 8080, debug)
│   └── web_prd.yml            # Variáveis do ambiente de Produção (porta 80, sem debug)
├── inventory/
│   └── hosts.yml              # Inventário hierárquico (web_servers -> web_dev / web_prd)
├── roles/
│   ├── nexus_base/            # Role de configuração de base do sistema operacional
│   │   ├── defaults/
│   │   │   └── main.yml       # Valores padrão da role base
│   │   └── tasks/
│   │       └── main.yml       # Validação de RAM, criação de diretórios e gestão de usuários
│   └── nexus_web/             # Role do servidor web Apache (httpd)
│       ├── handlers/
│       │   └── main.yml       # Handlers de restart do Apache e log de auditoria
│       ├── tasks/
│       │   └── main.yml       # Instalação do Apache, página HTML, template VHost e serviço
│       └── templates/
│           └── vhost.conf.j2  # Template Jinja2 do VirtualHost do Apache
└── site_orchestrator.yml      # Playbook principal de orquestração
```

---

## Ambientes e Inventário

O inventário (`inventory/hosts.yml`) agrupa os servidores sob o grupo pai `web_servers`, subdividido em dois ambientes:

| Grupo | Host | IP | Ambiente (`environment_stage`) | Porta HTTP (`http_port`) | Debug Banner (`enable_debug_banner`) |
| :--- | :--- | :--- | :---: | :---: | :---: |
| `web_dev` | `node1` | `192.168.55.201` | `dev` | `8080` | `true` |
| `web_prd` | `node2` | `192.168.55.202` | `prod` | `80` | `false` |

---

## Detalhamento das Roles

### 1. Role `nexus_base` (`Step 01`)
Responsável por preparar a infraestrutura base do servidor antes de subir a aplicação:
- **Validação de Hardware (`assert`)**: Verifica se o servidor possui no mínimo `512 MB` de RAM (`min_ram_required_mb`) consultando `ansible_facts['memtotal_mb']`.
- **Estrutura de Diretórios**: Cria os diretórios `/opt/nexus/bin`, `/opt/nexus/logs` e `/opt/nexus/data` usando `loop_control` com índice formatado.
- **Ciclo de Vida de Usuários**:
  - Provisiona contas ativas (`active: true`): `nexus_deployer` (UID 2501) e `nexus_auditor` (UID 2502).
  - Remove automaticamente contas revogadas/inativas (`active: false`): `nexus_legacy` (UID 2503).

### 2. Role `nexus_web` (`Step 02`)
Responsável pela camada de servidor web Apache (`httpd`):
- **Instalação e Diretório do VHost**: Instala o pacote `httpd` via `dnf` e cria `/var/www/vhosts/{{ ansible_hostname }}`.
- **Página Dinâmica (`index.html`)**: Gera uma página HTML exibindo nome da aplicação, ambiente, hostname, IP, sistema operacional e porta, alterando a cor de fundo conforme o ambiente (`#e6ffe6` em dev e `#ffe6e6` em prod).
- **Configuração de VirtualHost (`vhost.conf.j2`)**:
  - Configura a diretiva `Listen` automaticamente caso a porta não seja a `80`.
  - Direciona `ErrorLog` e `CustomLog` para `/opt/nexus/logs/`.
  - Injeta o cabeçalho `X-Environment` (`Development` ou `Production`) de acordo com a variável `enable_debug_banner`.
- **Handlers e Auditoria (`refresh_nexus_web`)**:
  - Sempre que o arquivo de VirtualHost sofre alterações, dispara um tópico (`listen: "refresh_nexus_web"`) que exibe um alerta no terminal, reinicia o serviço `httpd` e registra o evento com timestamp ISO8601 em `/opt/nexus/logs/deploy_audit.log`.
  - Utiliza `meta: flush_handlers` para garantir que o restart ocorra imediatamente antes da validação final do serviço.

---

## Fluxo do Orquestrador (`site_orchestrator.yml`)

O playbook principal executa o provisionamento em três fases:
1. **`pre_tasks`**: Exibe mensagem inicial validando conectividade, hostname e ambiente alvo.
2. **`tasks`**: Invoca modularmente as roles `nexus_base` e `nexus_web` com `include_role`, permitindo execução completa ou parcial através da variável `target_step`.
3. **`post_tasks`**: Realiza um *health check* local via `curl` na porta configurada (`http://localhost:{{ http_port }}`) e valida se o código de retorno HTTP é `200`.

---

## Como Executar

### 1. Executar o pipeline completo (Base + Web)
```bash
ansible-playbook -i inventory/hosts.yml site_orchestrator.yml
```

### 2. Executar apenas uma etapa específica (`target_step`)
O playbook aceita a variável `target_step` via linha de comando (`-e`) para rodar etapas isoladas:

- **Apenas a Role Base (`step01` ou `base`):**
  ```bash
  ansible-playbook -i inventory/hosts.yml site_orchestrator.yml -e "target_step=step01"
  ```

- **Apenas a Role Web (`step02` ou `web`):**
  ```bash
  ansible-playbook -i inventory/hosts.yml site_orchestrator.yml -e "target_step=step02"
  ```

### 3. Executar apenas em um ambiente específico (`--limit`)
- **Somente Desenvolvimento (`web_dev`):**
  ```bash
  ansible-playbook -i inventory/hosts.yml site_orchestrator.yml --limit web_dev
  ```

- **Somente Produção (`web_prd`):**
  ```bash
  ansible-playbook -i inventory/hosts.yml site_orchestrator.yml --limit web_prd
  ```

