# DesafioConstrua-um-Projeto-L-gico-de-Banco-de-Dados-do-Zero
Descrição do Projeto – Banco de Dados Oficina Este projeto tem como objetivo a modelagem e implementação de um banco de dados relacional para um cenário realista de oficina mecânica, desde o esquema lógico até a realização de queries avançadas em SQL.

DROP DATABASE IF EXISTS oficina;
CREATE DATABASE oficina;
USE oficina;

CREATE TABLE clientes (
  idCliente INT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(100),
  cpf CHAR(11) UNIQUE NOT NULL,
  telefone VARCHAR(15)
);
CREATE TABLE veiculos (
  idVeiculo INT AUTO_INCREMENT PRIMARY KEY,
  placa VARCHAR(10) UNIQUE NOT NULL,
  modelo VARCHAR(50),
  ano INT,
  idCliente INT,
  FOREIGN KEY (idCliente) REFERENCES clientes(idCliente)
);
CREATE TABLE funcionarios (
  idFuncionario INT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(100),
  funcao VARCHAR(50)
);
CREATE TABLE servicos (
  idServico INT AUTO_INCREMENT PRIMARY KEY,
  descricao VARCHAR(100),
  preco DECIMAL(10,2)
);
CREATE TABLE pecas (
  idPeca INT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(100),
  custo DECIMAL(10,2),
  fornecedor VARCHAR(100)
);
CREATE TABLE ordens_servico (
  idOrdem INT AUTO_INCREMENT PRIMARY KEY,
  idCliente INT,
  idVeiculo INT,
  data DATE,
  status ENUM('Aberta', 'Em Andamento', 'Finalizada') DEFAULT 'Aberta',
  FOREIGN KEY (idCliente) REFERENCES clientes(idCliente),
  FOREIGN KEY (idVeiculo) REFERENCES veiculos(idVeiculo)
);
CREATE TABLE servicos_realizados (
  idOrdem INT,
  idServico INT,
  idFuncionario INT,
  tempo_gasto INT,
  PRIMARY KEY (idOrdem, idServico),
  FOREIGN KEY (idOrdem) REFERENCES ordens_servico(idOrdem),
  FOREIGN KEY (idServico) REFERENCES servicos(idServico),
  FOREIGN KEY (idFuncionario) REFERENCES funcionarios(idFuncionario)
);
CREATE TABLE pecas_usadas (
  idOrdem INT,
  idPeca INT,
  quantidade INT,
  PRIMARY KEY (idOrdem, idPeca),
  FOREIGN KEY (idOrdem) REFERENCES ordens_servico(idOrdem),
  FOREIGN KEY (idPeca) REFERENCES pecas(idPeca)
);


INSERT INTO clientes (nome, cpf, telefone) VALUES
('Lucas Silva', '12345678901', '21999998888'),
('Ana Costa', '23456789012', '21988887777');

INSERT INTO veiculos (placa, modelo, ano, idCliente) VALUES
('ABC1234', 'Civic', 2018, 1),
('XYZ5678', 'Onix', 2020, 2);

INSERT INTO funcionarios (nome, funcao) VALUES
('Carlos Souza', 'Mecânico'),
('João Pedro', 'Eletricista');

INSERT INTO servicos (descricao, preco) VALUES
('Troca de óleo', 150.00),
('Alinhamento', 120.00);

INSERT INTO pecas (nome, custo, fornecedor) VALUES
('Filtro de óleo', 40.00, 'AutoParts RJ'),
('Pneu Aro 15', 300.00, 'Pneus Brasil');

INSERT INTO ordens_servico (idCliente, idVeiculo, data, status) VALUES
(1, 1, '2025-04-05', 'Finalizada'),
(2, 2, '2025-04-04', 'Aberta');

INSERT INTO servicos_realizados (idOrdem, idServico, idFuncionario, tempo_gasto) VALUES
(1, 1, 1, 30),
(1, 2, 2, 60);

INSERT INTO pecas_usadas (idOrdem, idPeca, quantidade) VALUES
(1, 1, 1),
(1, 2, 2);


SELECT nome, telefone FROM clientes;

SELECT * FROM ordens_servico WHERE status = 'Finalizada';

SELECT 
  pu.idOrdem, 
  pu.idPeca, 
  pu.quantidade, 
  pu.quantidade * p.custo AS custo_total
FROM pecas_usadas pu
JOIN pecas p ON pu.idPeca = p.idPeca
LIMIT 0, 1000;


select * from veiculos 
order by ano desc;

select idcliente, count(*) as total_ordens
from ordens_servico
group by idcliente
having total_ordens>1;

SELECT c.nome AS Cliente, v.modelo AS Veiculo, os.data, s.descricao AS Servico
FROM ordens_servico os
JOIN clientes c ON os.idCliente = c.idCliente
JOIN veiculos v ON os.idVeiculo = v.idVeiculo
JOIN servicos_realizados sr ON os.idOrdem = sr.idOrdem
JOIN servicos s ON sr.idServico = s.idServico;



