# TRANSA-ES-
Objetivo:    Neste desafio você irá lidar com transações para executar modificações na base de dados. Sendo assim, vamos relembrar como executar uma transação. Primeiramente, é preciso desabilitar o autocommit do MySQL. Caso contrário, cada instrução SQL executada será confirmada. 

Parte 1: Transações Simples
Transações Sem Procedures
Desabilitar o autocommit:

sql
Copiar código
SET AUTOCOMMIT = 0;
Iniciar a Transação:

sql
Copiar código
START TRANSACTION;
Executar Modificações: Exemplo de atualização de um produto no banco de dados:

sql
Copiar código
UPDATE produtos SET preco = preco * 1.1 WHERE categoria = 'Eletrônicos';
Confirmar (Commit) ou Desfazer (Rollback):

Se tudo estiver correto:
sql
Copiar código
COMMIT;
Se houver algum erro ou condição inesperada:
sql
Copiar código
ROLLBACK;
Exemplo Completo:
sql
Copiar código
SET AUTOCOMMIT = 0;

START TRANSACTION;

UPDATE produtos SET preco = preco * 1.1 WHERE categoria = 'Eletrônicos';

COMMIT;
Parte 2: Transação com Procedure
Aqui você irá criar uma procedure que inclui transações e checkpoints com SAVEPOINTS para recuperação parcial.

Exemplo de Procedure com Transação e Savepoint:
sql
Copiar código
DELIMITER //

CREATE PROCEDURE atualizar_salarios(IN departamento_id INT, IN aumento DECIMAL(5,2))
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        -- Em caso de erro, fazer o rollback
        ROLLBACK;
    END;

    START TRANSACTION;
    
    -- Savepoint para recuperação parcial
    SAVEPOINT antes_da_atualizacao;

    -- Atualiza os salários do departamento
    UPDATE empregados SET salario = salario * aumento WHERE departamento_id = departamento_id;
    
    -- Verificação de condição
    IF aumento > 1.5 THEN
        -- Caso o aumento seja muito alto, desfazer parcialmente
        ROLLBACK TO antes_da_atualizacao;
    ELSE
        -- Confirmar as mudanças
        COMMIT;
    END IF;
END //

DELIMITER ;
Chamada da Procedure:
sql
Copiar código
CALL atualizar_salarios(1, 1.1);
Parte 3: Backup e Recovery
Para o backup e recovery de um banco de dados MySQL, você pode usar o mysqldump.

Backup:
bash
Copiar código
mysqldump -u root -p e_commerce > backup_e_commerce.sql
e_commerce: Nome do banco de dados.
backup_e_commerce.sql: Arquivo onde o backup será salvo.
Recovery:
bash
Copiar código
mysql -u root -p e_commerce < backup_e_commerce.sql
Backup Incluindo Procedures, Eventos, etc.:
bash
Copiar código
mysqldump -u root -p --routines --events e_commerce > backup_completo_e_commerce.sql
