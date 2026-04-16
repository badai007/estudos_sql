IF NOT EXISTS (SELECT * FROM sys.tables WHERE name = 'users')
BEGIN
    CREATE TABLE users (
        id INT PRIMARY KEY IDENTITY(1,1),
        name VARCHAR(100) NOT NULL,
        email VARCHAR(150) UNIQUE NOT NULL,
        password VARCHAR(255) NOT NULL,
        created_at DATETIME DEFAULT GETDATE()
    );
END
IF NOT EXISTS (SELECT * FROM sys.tables WHERE name = 'task_status')
BEGIN
    CREATE TABLE task_status (
        id INT PRIMARY KEY,
        name VARCHAR(50) NOT NULL
    );
END
IF NOT EXISTS (SELECT * FROM task_status)
BEGIN
    INSERT INTO task_status (id, name) VALUES
    (0, 'Não feito'),
    (1, 'Fazendo'),
    (2, 'Concluído');
END
IF NOT EXISTS (SELECT * FROM sys.tables WHERE name = 'tasks')
BEGIN
    CREATE TABLE tasks (
        id INT PRIMARY KEY IDENTITY(1,1),
        title VARCHAR(150) NOT NULL,
        description VARCHAR(255),
        user_id INT NOT NULL,
        status_id INT NOT NULL,
        created_at DATETIME DEFAULT GETDATE(),

        FOREIGN KEY (user_id) REFERENCES users(id),
        FOREIGN KEY (status_id) REFERENCES task_status(id)
    );
END
IF NOT EXISTS (SELECT * FROM users WHERE email = 'cezar@email.com')
BEGIN
    INSERT INTO users (name, email, password)
    VALUES ('Cezar', 'cezar@email.com', '123456');
END
INSERT INTO tasks (title, description, user_id, status_id)
VALUES ('Estudar SQL', 'Aprender banco de dados', 1, 1);
SELECT 
    users.name,
    tasks.title,
    task_status.name AS status
FROM tasks
JOIN users ON tasks.user_id = users.id
JOIN task_status ON tasks.status_id = task_status.id;



ATUALIZAÇÃO de sql
-- B
USE todo_db;
GO

-- 1. Tabela de Usuários (Ajustada para o Login do JS)
IF NOT EXISTS (SELECT * FROM sys.tables WHERE name = 'users')
BEGIN
    CREATE TABLE users (
        id INT PRIMARY KEY IDENTITY(1,1),
        name VARCHAR(100) UNIQUE NOT NULL, -- UNIQUE para o 'identifier' do JS não dar conflito
        email VARCHAR(150) UNIQUE NOT NULL,
        password VARCHAR(MAX) NOT NULL,    -- VARCHAR(MAX) para suportar Criptografia/Hash futuramente
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

-- 3. Tabela de Tarefas
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

-- 4. Inserindo dados de teste
IF NOT EXISTS (SELECT * FROM users WHERE email = 'cesar@email.com')
BEGIN
    INSERT INTO users (name, email, password) 
    VALUES ('Cezar', 'cesar@email.com', 'senha123'); -- Em produção, essa senha seria um HASH
END
GO

Minhas Atualizações no Banco de Dados
 Atualizei o script do meu banco de dados (todo_db) para deixar o projeto mais profissional e pronto para a integração com o backend em Node.js. Aqui estão as principais mudanças que eu implementei:

1. Inteligência na Criação (IF NOT EXISTS)
Eu adicionei travas de segurança em todas as tabelas. Agora, o script verifica se a tabela já existe antes de tentar criar.

Por que eu fiz isso? Para o código ser "reutilizável". Eu posso rodar esse script quantas vezes precisar para atualizar o banco sem gerar erros de "tabela duplicada". É o que chamamos de código idempotente.

2. Nome de Usuário Único (UNIQUE)
Fiz uma alteração importante na tabela de usuários: agora o campo name é único, assim como o e-mail.

Por que eu fiz isso? No meu sistema de login, o usuário pode usar tanto o nome quanto o e-mail para entrar. Para evitar que o banco se confunda entre dois usuários com o mesmo nome, eu garanti que cada "apelido" seja exclusivo.

3. Preparação para Criptografia (VARCHAR(MAX))
Mudei o tamanho do campo de senha para suportar grandes volumes de texto.

Por que eu fiz isso? Pensando em segurança, não vou salvar senhas puras (texto real). Vou usar hashes criptografados, que são códigos bem longos. Com o VARCHAR(MAX), eu garanto que qualquer padrão de segurança que eu adotar no futuro caiba perfeitamente.

4. Organização das Chaves Estrangeiras (CONSTRAINT)
Em vez de apenas criar as ligações entre as tabelas de forma solta, eu dei nomes oficiais para as restrições (Constraints).

Por que eu fiz isso? Isso facilita muito a manutenção. Se eu precisar alterar a regra de como uma tarefa se liga a um usuário, eu sei exatamente qual "ponte" estou mexendo.

O Papel da IA no meu Desenvolvimento
Além das mudanças técnicas, eu utilizei Inteligência Artificial como uma ferramenta de co-piloto durante esse processo. Mas não foi apenas "copiar e colar"; eu utilizei a IA para:

Refinamento de Arquitetura: Usei a IA para validar se as minhas tabelas suportariam um fluxo real de login (usando e-mail ou nome) e para ajustar os tipos de dados (como o VARCHAR(MAX)) pensando em segurança futura.

Documentação Didática: A IA me ajudou a traduzir conceitos complexos de SQL em comentários claros dentro do código, facilitando a leitura para outros desenvolvedores que acessarem meu repositório.

Prevenção de Erros: Usei a IA para revisar a sintaxe das minhas Constraints e garantir que os comandos IF NOT EXISTS estivessem impecáveis, evitando falhas de execução no ambiente de produção.
