# 🚀 Projeto To-Do List - SQL Server & Integração

Este repositório contém a estrutura de banco de dados e a lógica de autenticação para o meu gerenciador de tarefas. Abaixo, detalho as evoluções técnicas que implementei no sistema.

---

## 🛠️ Script de Banco de Dados (SQL)

```sql
USE todo_db;
GO

-- 1. Tabela de Usuários (Ajustada para o Login do JS)
IF NOT EXISTS (SELECT * FROM sys.tables WHERE name = 'users')
BEGIN
    CREATE TABLE users (
        id INT PRIMARY KEY IDENTITY(1,1),
        name VARCHAR(100) UNIQUE NOT NULL, -- UNIQUE para o 'identifier' do JS
        email VARCHAR(150) UNIQUE NOT NULL,
        password VARCHAR(MAX) NOT NULL,    -- Suporte para Criptografia/Hash
        created_at DATETIME DEFAULT GETDATE()
    );
END
GO

-- 2. Tabela de Status
IF NOT EXISTS (SELECT * FROM sys.tables WHERE name = 'task_status')
BEGIN
    CREATE TABLE task_status (
        id INT PRIMARY KEY,
        name VARCHAR(50) NOT NULL
    );
END
GO

-- 3. Tabela de Tarefas com Constraints Nomeadas
IF NOT EXISTS (SELECT * FROM sys.tables WHERE name = 'tasks')
BEGIN
    CREATE TABLE tasks (
        id INT PRIMARY KEY IDENTITY(1,1),
        title VARCHAR(150) NOT NULL,
        description VARCHAR(255),
        user_id INT NOT NULL,
        status_id INT NOT NULL,
        created_at DATETIME DEFAULT GETDATE(),
        CONSTRAINT FK_Tasks_Users FOREIGN KEY (user_id) REFERENCES users(id),
        CONSTRAINT FK_Tasks_Status FOREIGN KEY (status_id) REFERENCES task_status(id)
    );
END
GO

Minhas Atualizações no Backend
Refinei o script para deixar o projeto pronto para a integração com Node.js. As principais melhorias foram:

Inteligência na Criação (IF NOT EXISTS): Implementei travas de segurança para que o código seja idempotente. Posso rodar o script várias vezes sem causar erros de duplicidade.

Nome de Usuário Único (UNIQUE): Como meu sistema permite login por nome ou e-mail, garanti que cada "username" seja exclusivo, evitando conflitos na autenticação.

Preparação para Segurança (VARCHAR(MAX)): Alterei o campo de senha para suportar hashes criptográficos longos, seguindo as boas práticas de segurança da informação.

Constraints Nomeadas: Organizeis as chaves estrangeiras com nomes específicos para facilitar futuras manutenções na arquitetura do banco.

O Papel da IA no meu Desenvolvimento
Como estudante de ADS, utilizei Inteligência Artificial como um Co-piloto Estratégico para:

Refinar a Arquitetura: Validar tipos de dados e relacionamentos entre tabelas.

Documentação: Gerar comentários didáticos e organizar este README de forma profissional.

Prevenção de Bugs: Revisar a sintaxe SQL e garantir a integridade das Constraints.
