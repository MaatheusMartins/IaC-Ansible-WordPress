# IaC Ansible WordPress

Infraestrutura como Código (IaC) para provisionamento automatizado de WordPress com MySQL usando Ansible. Projeto de aprendizado.

## 📋 Descrição

Este projeto utiliza Ansible para automatizar a instalação e configuração de uma infraestrutura completa de WordPress distribuída em múltiplos servidores:
- **Servidor WordPress**: Apache + WordPress
- **Servidor MySQL**: MySQL Database Server

A solução permite gerenciar e replicar a infraestrutura de forma consistente e repetível.

## 🏗️ Arquitetura

```
┌─────────────────────────────────────────────────────────────┐
│                     Ansible Control Node                    │
└─────────────────────────────────────────────────────────────┘
                              │
                 ┌────────────┼────────────┐
                 │                         │
        ┌────────▼──────┐           ┌──▼────────────┐
        │ WordPress     │           │ MySQL Server  │
        │ (192.168.1.17)│           │(192.168.1.22) │
        │               │           │               │
        │ • Apache2     │           │ • MySQL Server│
        │ • WordPress   │           │ • Database    │
        └───────────────┘           └───────────────┘
```

## ⚙️ Configuração

### 1. Inventário (hosts)

O arquivo `hosts` define os servidores alvo:

```
[wordpress]
192.168.1.17 ansible_user=servidor1 ansible_ssh_private_key_file='/home/ansible/ansible/servidor1'

[mysql]
192.168.1.22 ansible_user=servidor2 ansible_ssh_private_key_file='/home/ansible/ansible/servidor2'
```

### 2. Variáveis de Configuração

#### `group_vars/all.yml` (Variáveis Globais)

```yaml
wp_db_name: "wordpress_db"        # Nome do banco de dados
wp_db_user: "wordpress_user"      # Usuário do banco
wp_db_password: "12345"           # Senha (Criada para testes)
```

#### `group_vars/mysql.yml` (Variáveis MySQL)

```yaml
wp_ip: "192.168.1.17"             # IP do servidor WordPress
```

#### `group_vars/wordpress.yml` (Variáveis WordPress)

```yaml
db_ip: "192.168.1.22"             # IP do servidor MySQL
wp_dir: "/srv/www/wordpress"      # Diretório de instalação do WordPress
```

## 🚀 Como Executar

```bash
ansible-playbook -i hosts playbook.yml -K
```

## 📑 Descrição das Roles

### Apache

**Localização**: `roles/apache/`

Instala o Apache2 e todas as dependências PHP necessárias para rodar WordPress:
- Apache2
- PHP 7.4+
- Extensões PHP: bcmath, curl, imagick, intl, json, mbstring, xml, zip
- Ghostscript (para processamento de imagens)

### MySQL

**Localização**: `roles/mysql/`

Configura o servidor MySQL:
- Instala MySQL Server e python3-pymysql
- Cria o banco de dados WordPress
- Cria usuário e configura permissões
- Habilita conexões remotas do servidor WordPress
- Manipuladores: Reinicia o serviço MySQL quando necessário

### WordPress

**Localização**: `roles/wordpress/`

Configura o WordPress:
- Cria diretório (`/srv/www`)
- Baixa e descompacta WordPress
- Configura arquivo `wp-config.php` com credenciais do banco
- Define chaves de segurança do WordPress
- Configura VirtualHost do Apache
- Manipuladores: Reinicia o Apache quando necessário