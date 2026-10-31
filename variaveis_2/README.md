# Laboratório: Precedência de Variáveis e Templates Dinâmicos no Ansible

Este laboratório demonstra o funcionamento da hierarquia e precedência de variáveis no Ansible utilizando a convenção de diretórios (`group_vars` e `host_vars`), além do provisionamento automatizado de um servidor web Apache (`httpd`) com templates dinâmicos Jinja2 baseados no ambiente e nos *facts* do servidor.

---

## 📁 Estrutura do Projeto

```text
variaveis_2/
├── ansible.cfg                   # Configuração padrão do Ansible (inventário e SSH)
├── inventory                     # Inventário de hosts (grupo [web] -> node1)
├── group_vars/
│   └── web.yml                   # Variáveis padrão do grupo 'web' (stage: dev)
├── host_vars/
│   └── node1.yml                 # Variáveis exclusivas do host 'node1' (stage: prod)
├── templates/
│   └── index_avancado.html.j2    # Template HTML dinâmico em Jinja2
├── teste_variaveis.yml           # Playbook para testar a precedência de variáveis
└── deploy_web.yml                # Playbook de instalação do Apache e deploy do template
```

---

## 1. Arquitetura e Precedência de Variáveis

O Ansible utiliza o princípio de **Convenção sobre Configuração**, buscando automaticamente arquivos de variáveis nas pastas `group_vars/` e `host_vars/` de acordo com os nomes dos grupos e hosts definidos no arquivo `inventory`.

### Hierarquia aplicada neste projeto:
1. **`group_vars/web.yml` (Escopo de Grupo - Menor Precedência):**
   Define a variável padrão para todos os servidores que pertencem ao grupo `[web]`:
   ```yaml
   stage: dev
   ```
2. **`host_vars/node1.yml` (Escopo de Host - Maior Precedência):**
   Define uma variável específica para o servidor `node1`, sobrescrevendo o valor do grupo:
   ```yaml
   stage: prod
   ```

> **Regra de Ouro:** Quanto mais específica a definição da variável, maior a sua prioridade. Portanto, `host_vars` sempre vence `group_vars`.

---

## 2. Playbooks e Templates

### 🔹 `teste_variaveis.yml`
Playbook simples utilizando o módulo `ansible.builtin.debug` para validar qual valor da variável `{{ stage }}` o Ansible aplica ao `node1`.
- **Resultado esperado:** Imprime `prod` (pois `host_vars/node1.yml` sobrescreve `group_vars/web.yml`).

### 🔹 `deploy_web.yml`
Playbook completo que executa como `root` (`become: true`) para configurar o servidor web:
1. **Instalação (`ansible.builtin.dnf`):** Instala o pacote `httpd` (Apache).
2. **Serviço (`ansible.builtin.service`):** Inicia e habilita o serviço `httpd` no boot.
3. **Firewall (`ansible.posix.firewalld`):** Libera o tráfego HTTP permanentemente.
4. **Template (`ansible.builtin.template`):** Processa o arquivo `templates/index_avancado.html.j2` e envia o HTML final gerado para `/var/www/html/index.html`.

### 🔹 `templates/index_avancado.html.j2`
Utiliza **Jinja2** para gerar uma página HTML personalizada dinamicamente:
- **Filtros Jinja2:** `{{ stage | upper }}` transforma `prod` em `PROD`.
- **Condicionais (`if/else`):** Altera a cor de fundo da página dependendo do ambiente (`#ffe6e6` avermelhado para `prod`, `#e6ffe6` esverdeado para `dev`).
- **Ansible Facts (`ansible_facts`):** Coleta automaticamente informações reais da máquina alvo (Hostname, Sistema Operacional/Versão e Endereço IPv4).

---

## 3. Troubleshooting (Problemas Resolvidos no Lab)

Durante a execução do laboratório, dois detalhes importantes de convenção do Ansible foram identificados e corrigidos:

1. **Nome do diretório `host_vars`:**
   - **Sintoma:** O playbook imprimiu `dev` em vez de `prod`.
   - **Causa:** O diretório estava nomeado como `host.vars` (com ponto) em vez de `host_vars` (com underscore). O Ansible ignorou a pasta e usou o valor padrão do `group_vars/web.yml`.
   - **Solução:** Renomeado com `mv host.vars host_vars`.

2. **Localização do arquivo de Template (`.j2`):**
   - **Sintoma:** Erro ao executar a tarefa `ansible.builtin.template` no `deploy_web.yml`.
   - **Causa:** O playbook apontava para `src: templates/index_avancado.html.j2`, mas o arquivo estava na raiz do projeto.
   - **Solução:** Criada a pasta `templates/` e movido o arquivo para `templates/index_avancado.html.j2`.

---

## 4. Como Executar

Certifique-se de estar com o ambiente virtual ativado (se aplicável) e dentro da pasta `variaveis_2`:

**1. Testar a precedência de variáveis:**
```bash
ansible-playbook teste_variaveis.yml
```

**2. Fazer o deploy do servidor Web com o template dinâmico:**
```bash
ansible-playbook deploy_web.yml
```

**3. Validar o resultado:**
Acesse no navegador ou via `curl` o IP do nó configurado no inventário:
```bash
curl http://192.168.0.20
```

