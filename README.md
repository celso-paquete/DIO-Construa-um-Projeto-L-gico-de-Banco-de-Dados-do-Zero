# DIO-Construa-um-Projeto-L-gico-de-Banco-de-Dados-do-Zero
Criação de um banco de dados para consultas médicas

# Modelo Conceitual da Base de Dados
 - Entidades e Atributos Principais
     - Paciente: Nome, Nº de Bilhete de Identidade BI (chave primária), Data de Nascimento, Sexo, Residencia, Telefone, Email.
     - Médico: Nº da Ordem dos Médicos OMA (chave primária), Nome, Especialidade e Telefone.
     - Consulta: ID da Consulta, Paciente, Data, Hora, Motivo da marcação (Queixa Principal), Diagnóstico, Médico.
     - Exame Laboratorial: ID do Exame Lab, Paciente, Médico que solucitou, data da solicitação, Data do exame, Tipo de exame, Resultado.
     - Exame de Imagem: ID do Exame Imagem, Paciente, Médico que solucitou, data da solicitação, Data do exame, Tipo de exame.
 - Chaves primárias
      - Paciente - 	BI
      - Médico	OMA
      - Consulta - ID_Consulta
      - Exame Lab	 - ID_Exame_Lab
      - Exame Imagem - ID_Exame_Img
 - Os Relacionamentos (Esquema Lógico)
      - Paciente (1) ↔ Consulta (N): Um paciente (identificado pelo BI) pode ter várias consultas. O BI do paciente aparece na tabela Consulta como Chave Estrangeira (FK).
      - Médico (1) ↔ Consulta (N): Um médico (identificado pela OMA) realiza várias consultas. O OMA do médico aparece na tabela Consulta como FK.
      - Consulta (1) ↔ Exames (N): Como os exames (Lab e Imagem) possuem "Data da Solicitação", eles nascem de uma Consulta. O ID_Consulta deve estar presente nas tabelas de exames para saber em qual momento clínico aquele exame foi pedido.

# Definição do script SQL para criação do esquema da Base de dados
 
    -- 1. Criar a Tabela de Médicos
    CREATE TABLE MEDICO (
        OMA VARCHAR(20) PRIMARY KEY,
        Nome VARCHAR(100) NOT NULL,
        Especialidade VARCHAR(50) NOT NULL,
        Telefone VARCHAR(20)
    );
    
    -- 2. Criar a Tabela de Pacientes
    CREATE TABLE PACIENTE (
        BI VARCHAR(15) PRIMARY KEY,
        Nome VARCHAR(100) NOT NULL,
        Data_Nascimento DATE NOT NULL,
        Sexo CHAR(1) CHECK (Sexo IN ('M', 'F')),
        Residencia VARCHAR(255),
        Telefone VARCHAR(20),
        Email VARCHAR(100)
    );
    
    -- 3. Criar a Tabela de Consultas (Entidade Central)
    CREATE TABLE CONSULTA (
        ID_Consulta INT AUTO_INCREMENT PRIMARY KEY,
        Data_Consulta DATE NOT NULL,
        Hora_Consulta TIME NOT NULL,
        Motivo_Marcacao TEXT,
        Diagnostico TEXT,
        FK_BI_Paciente VARCHAR(15),
        FK_OMA_Medico VARCHAR(20),
        FOREIGN KEY (FK_BI_Paciente) REFERENCES PACIENTE(BI),
        FOREIGN KEY (FK_OMA_Medico) REFERENCES MEDICO(OMA)
    );
    
    -- 4. Criar a Tabela de Exames Laboratoriais
    CREATE TABLE EXAME_LABORATORIAL (
        ID_Exame_Lab INT AUTO_INCREMENT PRIMARY KEY,
        Tipo_Exame VARCHAR(100) NOT NULL,
        Data_Solicitacao DATE NOT NULL,
        Data_Exame DATE,
        Resultado TEXT,
        FK_ID_Consulta INT,
        FK_BI_Paciente VARCHAR(15),
        FK_OMA_Solicitante VARCHAR(20),
        FOREIGN KEY (FK_ID_Consulta) REFERENCES CONSULTA(ID_Consulta) ON DELETE CASCADE,
        FOREIGN KEY (FK_BI_Paciente) REFERENCES PACIENTE(BI),
        FOREIGN KEY (FK_OMA_Solicitante) REFERENCES MEDICO(OMA)
    );
    
    -- 5. Criar a Tabela de Exames de Imagem
    CREATE TABLE EXAME_IMAGEM (
        ID_Exame_Img INT AUTO_INCREMENT PRIMARY KEY,
        Tipo_Exame VARCHAR(100) NOT NULL,
        Data_Solicitacao DATE NOT NULL,
        Data_Exame DATE,
        Laudo_Imagem TEXT,
        FK_ID_Consulta INT,
        FK_BI_Paciente VARCHAR(15),
        FK_OMA_Solicitante VARCHAR(20),
        FOREIGN KEY (FK_ID_Consulta) REFERENCES CONSULTA(ID_Consulta) ON DELETE CASCADE,
        FOREIGN KEY (FK_BI_Paciente) REFERENCES PACIENTE(BI),
        FOREIGN KEY (FK_OMA_Solicitante) REFERENCES MEDICO(OMA)
    );

 # Persistência de dados para testes

     -- 1. Inserindo Médicos (OMA fictícias)
    INSERT INTO MEDICO (OMA, Nome, Especialidade, Telefone) VALUES
    ('OMA12345', 'Dr. António Agostinho', 'Clínica Geral', '923000111'),
    ('OMA67890', 'Dra. Maria Isabel', 'Pediatria', '931000222'),
    ('OMA11223', 'Dr. Carlos Manuel', 'Cardiologia', '944000333');
    
    -- 2. Inserindo Pacientes (BI fictícios)
    INSERT INTO PACIENTE (BI, Nome, Data_Nascimento, Sexo, Residencia, Telefone, Email) VALUES
    ('001234567LA041', 'João Paulo Silva', '1985-05-20', 'M', 'Maianga, Luanda', '912000001', 'joao.silva@email.com'),
    ('002345678BE042', 'Ana Paula Santos', '1992-11-10', 'F', 'Talatona, Luanda', '912000002', 'ana.santos@email.com'),
    ('003456789HU043', 'Beatriz Cardoso', '2015-03-15', 'F', 'Viana, Luanda', '912000003', 'beatriz.c@email.com'),
    ('004567890LA044', 'Ricardo Jorge', '1970-01-30', 'M', 'Benfica, Luanda', '912000004', 'ricardo.j@email.com');
    
    -- 3. Inserindo Consultas
    -- Note que as Fks (BI e OMA) devem existir nas tabelas acima
    INSERT INTO CONSULTA (Data_Consulta, Hora_Consulta, Motivo_Marcacao, Diagnostico, FK_BI_Paciente, FK_OMA_Medico) VALUES
    ('2024-05-10', '09:00:00', 'Febre e dores no corpo', 'Suspeita de Malária', '001234567LA041', 'OMA12345'),
    ('2024-05-10', '10:30:00', 'Check-up de rotina', 'Paciente saudável', '002345678BE042', 'OMA12345'),
    ('2024-05-11', '08:00:00', 'Tosse persistente', 'Infecção respiratória', '003456789HU043', 'OMA67890'),
    ('2024-05-11', '14:00:00', 'Dor no peito', 'Hipertensão a controlar', '004567890LA044', 'OMA11223');
    
    -- 4. Inserindo Exames Laboratoriais (Vinculados às Consultas)
    INSERT INTO EXAME_LABORATORIAL (Tipo_Exame, Data_Solicitacao, Data_Exame, Resultado, FK_ID_Consulta, FK_BI_Paciente, FK_OMA_Solicitante) VALUES
    ('Gota Espessa', '2024-05-10', '2024-05-10', 'Positivo para P. falciparum (+)', 1, '001234567LA041', 'OMA12345'),
    ('Hemograma Completo', '2024-05-10', '2024-05-11', 'Normal', 2, '002345678BE042', 'OMA12345'),
    ('Glicemia em Jejum', '2024-05-10', '2024-05-11', '95 mg/dL', 2, '002345678BE042', 'OMA12345'),
    ('Teste de Sensibilidade Antibiótica', '2024-05-11', NULL, 'Pendente', 3, '003456789HU043', 'OMA67890');
    
    -- 5. Inserindo Exames de Imagem
    INSERT INTO EXAME_IMAGEM (Tipo_Exame, Data_Solicitacao, Data_Exame, Laudo_Imagem, FK_ID_Consulta, FK_BI_Paciente, FK_OMA_Solicitante) VALUES
    ('Raio-X Tórax', '2024-05-11', '2024-05-12', 'Infiltrado leve em base direita', 3, '003456789HU043', 'OMA67890'),
    ('Ecocardiograma', '2024-05-11', '2024-05-13', 'Hipertrofia ventricular leve', 4, '004567890LA044', 'OMA11223');

# Perguntas que podem ser respondidas pelas consultas
  
    -- 1. Recuperações simples com SELECT Statement
    -- Pergunta: Quais são todos os médicos cadastrados e suas especialidades?
    SELECT Nome, Especialidade 
    FROM MEDICO;
  
              /* RESULTADO ESPERADO:
              +-----------------------+----------------+
              | Nome                  | Especialidade  |
              +-----------------------+----------------+
              | Dr. António Agostinho | Clínica Geral  |
              | Dra. Maria Isabel     | Pediatria      |
              | Dr. Carlos Manuel     | Cardiologia    |
              +-----------------------+----------------+
              */
  
  
    -- 2. Filtros com WHERE Statement
    -- Pergunta: Quais pacientes do sexo feminino (F) nasceram após o ano 2000?
    SELECT Nome, Data_Nascimento 
    FROM PACIENTE 
    WHERE Sexo = 'F' AND Data_Nascimento > '2000-12-31';
  
              /* RESULTADO ESPERADO:
              +-----------------+-----------------+
              | Nome            | Data_Nascimento |
              +-----------------+-----------------+
              | Beatriz Cardoso | 2015-03-15      |
              +-----------------+-----------------+
              */
  
  
    -- 3. Atributos Derivados (Cálculos de Idade e Tempo de Espera)
    -- Pergunta: Qual a idade e quantos dias levou para o exame ser realizado?
    SELECT 
        Tipo_Exame, 
        DATEDIFF(Data_Exame, Data_Solicitacao) AS Dias_Espera,
        (YEAR(CURDATE()) - YEAR(Data_Nascimento)) AS Idade_Paciente
    FROM EXAME_LABORATORIAL
    JOIN PACIENTE ON EXAME_LABORATORIAL.FK_BI_Paciente = PACIENTE.BI;
  
              /* RESULTADO ESPERADO (Simulação em 2026):
              +---------------------+-------------+----------------+
              | Tipo_Exame          | Dias_Espera | Idade_Paciente |
              +---------------------+-------------+----------------+
              | Gota Espessa        | 0           | 41             |
              | Hemograma Completo  | 1           | 34             |
              | Glicemia em Jejum   | 1           | 34             |
              | Teste Sensibilid.   | NULL        | 11             |
              +---------------------+-------------+----------------+
              */
  
  
    -- 4. Ordenações com ORDER BY
    -- Pergunta: Listar consultas da mais recente para a mais antiga.
    SELECT Data_Consulta, Motivo_Marcacao, FK_BI_Paciente
    FROM CONSULTA 
    ORDER BY Data_Consulta DESC;
  
              /* RESULTADO ESPERADO:
              +---------------+-----------------------+----------------+
              | Data_Consulta | Motivo_Marcacao       | FK_BI_Paciente |
              +---------------+-----------------------+----------------+
              | 2024-05-11    | Dor no peito          | 004567890LA044 |
              | 2024-05-11    | Tosse persistente     | 003456789HU043 |
              | 2024-05-10    | Check-up de rotina    | 002345678BE042 |
              | 2024-05-10    | Febre/Dores corpo     | 001234567LA041 |
              +---------------+-----------------------+----------------+
              */
  
  
    -- 5. Filtros de Grupo com HAVING Statement
    -- Pergunta: IDs de Médicos com 1 ou mais exames de imagem solicitados.
    SELECT FK_OMA_Solicitante, COUNT(*) AS Total_Imagens
    FROM EXAME_IMAGEM
    GROUP BY FK_OMA_Solicitante
    HAVING COUNT(*) >= 1;
  
              /* RESULTADO ESPERADO:
              +--------------------+---------------+
              | FK_OMA_Solicitante | Total_Imagens |
              +--------------------+---------------+
              | OMA67890           | 1             |
              | OMA11223           | 1             |
              +--------------------+---------------+
              */
  
  
    -- 6. Junções Complexas (JOIN)
    -- Pergunta: Relatório Multidisciplinar (Paciente, Médico, Diagnóstico e Laudo)
    SELECT 
        P.Nome AS Paciente,
        M.Nome AS Medico,
        C.Diagnostico,
        I.Laudo_Imagem
    FROM PACIENTE P
    INNER JOIN CONSULTA C ON P.BI = C.FK_BI_Paciente
    INNER JOIN MEDICO M ON C.FK_OMA_Medico = M.OMA
    LEFT JOIN EXAME_IMAGEM I ON C.ID_Consulta = I.FK_ID_Consulta;
  
              /* RESULTADO ESPERADO:
              +-------------------+-----------------------+-------------------------+---------------------------------+
              | Paciente          | Medico                | Diagnostico             | Laudo_Imagem                    |
              +-------------------+-----------------------+-------------------------+---------------------------------+
              | João Paulo Silva  | Dr. António Agostinho | Suspeita de Malária     | NULL                            |
              | Ana Paula Santos  | Dr. António Agostinho | Paciente saudável       | NULL                            |
              | Beatriz Cardoso   | Dra. Maria Isabel     | Infecção respiratória   | Infiltrado leve em base direita |
              | Ricardo Jorge     | Dr. Carlos Manuel     | Hipertensão a controlar | Hipertrofia ventricular leve    |
              +-------------------+-----------------------+-------------------------+---------------------------------+
              */
                
