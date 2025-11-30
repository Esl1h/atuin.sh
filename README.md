# Atuin Self-Hosted Server

Implementação básica de um servidor Atuin auto-hospedado usando Docker para sincronização de histórico de shell entre múltiplos clientes e VMs.

## Sobre o Atuin

Atuin substitui o histórico padrão do shell com um banco de dados SQLite (PostgreSQL no meu caso), registrando contexto adicional para seus comandos. Além disso, oferece busca opcional e totalmente criptografada e sincronização entre máquinas através de um servidor Atuin.

Com Atuin você pode:
- Buscar seu histórico de comandos com contexto completo (diretório, status de saída, duração)
- Sincronizar histórico entre múltiplas máquinas de forma segura e criptografada
- Fazer backup do seu histórico em seu próprio servidor
- Visualizar estatísticas sobre os comandos mais utilizados
- Filtrar histórico por sessão, host, diretório ou workspace

## meu caso de uso
Self-hosted em homeserver (Docker) e alguns clients, sendo 2 Archs (server, laptop), 1 Fedora (workstation), 1 Debian (raspbian/raspberry) e algumas VMs do home lab. Maioria com zsh, fzf e oh-my-zsh.
Com Atuin, consigo separar por contextos, workspaces, historico global etc...


## Estrutura do Projeto

```
.
├── config/
│   ├── config.toml          # Configuração do cliente
│   └── server.toml          # Configuração do servidor
├── database/                # Dados do PostgreSQL (gerado automaticamente)
├── docker-compose.yml       # Orquestração dos serviços
├── .env                     # Variáveis de ambiente (credenciais)
├── example.env              # Exemplo de configuração
├── .gitignore              # Arquivos ignorados pelo git
└── atuin.service           # Serviço systemd (opcional)
```

## Configuração

### 1. Preparar o ambiente

```bash
# Copiar o arquivo de exemplo
cp example.env .env

# Editar com suas credenciais
vim .env
```

### 2. Iniciar os serviços

```bash
# Subir o servidor e banco de dados
docker-compose up -d

# Verificar logs
docker-compose logs -f
```

O servidor estará disponível em `http://localhost:8888` (no meu caso já está configurado no cliente como 172.22.2.108)

### 3. Configurar o cliente

No cliente, instale o Atuin e configure para usar seu servidor:

```bash
# Instalar Atuin
bash <(curl https://raw.githubusercontent.com/atuinsh/atuin/main/install.sh)

# Registrar no servidor
atuin register -u  -e  -p 

# Configurar o endereço do servidor
atuin login -u  -p 

# Sincronizar
atuin sync
```

## Configurações Personalizadas

O arquivo `config/config.toml` contém personalizações do cliente incluindo:
- Modo de busca: fuzzy
- Filtro por dotfiles habilitado
- Comandos ignorados nas estatísticas (cd, ls, vim)
- Formato de histórico personalizado
- Tema e estilo da interface

## Uso

Configuração atual:
- ✅ Sincronização automática
- ✅ Registro aberto (open_registration)
- ✅ Métricas habilitadas (porta 9001)

## Links Oficiais

- **Documentação do Cliente**: https://docs.atuin.sh
- **Documentação do Servidor**: https://docs.atuin.sh/self-hosting/server-setup/
- **Repositório GitHub**: https://github.com/atuinsh/atuin
- **Site Oficial**: https://atuin.sh

## Segurança

⚠️ **Importante**: Este setup usa `open_registration = true` para facilitar o registro inicial. Para ambientes de produção, considere:
- Desabilitar registro aberto após criar as contas necessárias
- Usar HTTPS com certificados TLS válidos
- Configurar firewall para limitar acesso ao servidor
- Fazer backup regular do banco de dados PostgreSQL

## Licença

Este projeto segue a licença do Atuin original. Consulte o repositório oficial para mais detalhes.
