USE MASTER
    ALTER DATABASE Universidade SET SINGLE_USER WITH ROLLBACK IMMEDIATE
    GO
    DROP DATABASE Universidade;
    GO
    USE master;
    CREATE DATABASE Universidade;
    GO
    USE Universidade;
    GO
    CREATE TABLE ALUNOS (MATRICULA INT NOT NULL IDENTITY CONSTRAINT PK_ALUNO PRIMARY KEY, NOME VARCHAR(50) NOT NULL);
    GO
    CREATE TABLE CURSOS (CURSO CHAR(3) NOT NULL CONSTRAINT PK_CURSO PRIMARY KEY, NOME VARCHAR(50) NOT NULL);
    GO
    CREATE TABLE PROFESSOR (PROFESSOR INT IDENTITY NOT NULL CONSTRAINT PK_PROFESSOR PRIMARY KEY, NOME VARCHAR(50) NOT NULL);
    GO
    CREATE TABLE MATERIAS (SIGLA CHAR(3) NOT NULL, NOME VARCHAR(50) NOT NULL, CARGAHORARIA INT NOT NULL,
    CURSO CHAR(3) NOT NULL, PROFESSOR INT
    CONSTRAINT PK_MATERIA PRIMARY KEY (SIGLA,CURSO,PROFESSOR)
    CONSTRAINT FK_CURSO FOREIGN KEY (CURSO) REFERENCES CURSOS(CURSO),
    CONSTRAINT FK_PROFESSOR FOREIGN KEY (PROFESSOR) REFERENCES PROFESSOR (PROFESSOR)
    );
    GO
    INSERT ALUNOS (NOME) VALUES ('Pedro')
    GO
    INSERT CURSOS (CURSO, NOME) VALUES ('SIS','SISTEMAS'),('ENG','ENGENHARIA')
    GO
    INSERT PROFESSOR (NOME ) VALUES ('DORNEL'),('WALTER')
    GO
    INSERT MATERIAS (SIGLA, NOME, CARGAHORARIA, CURSO,PROFESSOR)
    VALUES ('BDA','BANCO DE DADOS',144,'SIS',1), ('PRG','PROGRAMAÇÃO',144,'SIS',2)
    GO
    INSERT MATERIAS (SIGLA, NOME, CARGAHORARIA, CURSO,PROFESSOR)
    VALUES ('BDA','BANCO DE DADOS',144,'ENG',1), ('PRG','PROGRAMAÇÃO',144,'ENG',2)
    GO
    CREATE TABLE MATRICULA (MATRICULA INT, CURSO CHAR(3), MATERIA CHAR(3), PROFESSOR INT, PERLETIVO INT,
    N1 FLOAT, N2 FLOAT, N3 FLOAT, N4 FLOAT, TOTALPONTOS FLOAT, MEDIA FLOAT,
    F1 INT, F2 INT, F3 INT, F4 INT, TOTALFALTAS INT, PERCFREQ FLOAT, RESULTADO VARCHAR(20)

    CONSTRAINT PK_MATRICULA PRIMARY KEY (MATRICULA,CURSO,MATERIA,PROFESSOR,PERLETIVO),
    CONSTRAINT FK_ALUNOS_MATRICULA FOREIGN KEY (MATRICULA) REFERENCES ALUNOS (MATRICULA),
    CONSTRAINT FK_CURSOS_MATRICULA FOREIGN KEY (CURSO) REFERENCES CURSOS (CURSO),
    --CONSTRAINT FK_MATERIAS FOREIGN KEY (MATERIA) REFERENCES MATERIAS (SIGLA),
    CONSTRAINT FK_PROFESSOR_MATRICULA FOREIGN KEY (PROFESSOR) REFERENCES PROFESSOR(PROFESSOR)
    )


    CREATE PROCEDURE sp_MatriculaAluno
    (
    @NOMEALUNO VARCHAR(50),
    @CURSOALUNO VARCHAR(50)
    )
    AS
    BEGIN

    DECLARE @MATRICULAALUNO INT, @CODIGOCURSO VARCHAR(3)

    SET @MATRICULAALUNO = (SELECT MATRICULA FROM ALUNOS WHERE NOME = @NOMEALUNO)

    SET @CODIGOCURSO = (SELECT CURSO FROM CURSOS WHERE NOME = @CURSOALUNO)

    INSERT MATRICULA
            (
                            MATRICULA,
                            CURSO,
                            MATERIA,
                            PROFESSOR,
                            PERLETIVO

            )
            SELECT @MATRICULAALUNO AS MATRICULA, CURSO, SIGLA,PROFESSOR, YEAR(GETDATE()) AS PERLETIVO FROM MATERIAS WHERE CURSO ='ENG'

    END

    --Calculo do percentual de Frequencia (144-NrFaltas*100)/144


    EXEC sp_MatriculaAluno @NOMEALUNO = 'Guilherme', -- varchar(50)
                                               @CURSOALUNO = 'Sistemas' -- varchar(50)
