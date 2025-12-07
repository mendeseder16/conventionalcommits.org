Diagrama EER Simplificado:

- Cliente (1:N) → Pedido
- Pedido (1:N) → Pagamento
- Pedido (1:1) → Entrega
- Fornecedor (1:N) → Produto


CREATE TABLE Cliente (
    ID_cliente INT PRIMARY KEY,
    Nome VARCHAR(100) NOT NULL,
    Tipo ENUM('PF', 'PJ') NOT NULL,
    CNPJ VARCHAR(14),
    CPF VARCHAR(11),
    CHECK ((Tipo = 'PF' AND CNPJ IS NULL) OR (Tipo = 'PJ' AND CPF IS NULL))
);

CREATE TABLE Fornecedor (
    ID_fornecedor INT PRIMARY KEY,
    Nome VARCHAR(100) NOT NULL
);

CREATE TABLE Produto (
    ID_produto INT PRIMARY KEY,
    Nome VARCHAR(100) NOT NULL,
    Descricao TEXT,
    Preco DECIMAL(10, 2) NOT NULL,
    ID_fornecedor INT,
    FOREIGN KEY (ID_fornecedor) REFERENCES Fornecedor(ID_fornecedor)
);

CREATE TABLE Pedido (
    ID_pedido INT PRIMARY KEY,
    ID_cliente INT,
    Data DATE NOT NULL,
    Status ENUM('Pendente', 'Enviado', 'Entregue'),
    FOREIGN KEY (ID_cliente) REFERENCES Cliente(ID_cliente)
);

CREATE TABLE Pagamento (
    ID_pagamento INT PRIMARY KEY,
    ID_pedido INT,
    Tipo_pagamento ENUM('Cartão', 'Boleto', 'Transferência'),
    Valor DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (ID_pedido) REFERENCES Pedido(ID_pedido)
);

CREATE TABLE Entrega (
    ID_entrega INT PRIMARY KEY,
    ID_pedido INT,
    Status ENUM('Aguardando', 'Em Trânsito', 'Entregue'),
    Codigo_rastreamento VARCHAR(50),
    FOREIGN KEY (ID_pedido) REFERENCES Pedido(ID_pedido)
);



INSERT INTO Cliente (ID_cliente, Nome, Tipo, CNPJ, CPF) VALUES (1, 'Empresa ABC', 'PJ', '12345678000195', NULL);
INSERT INTO Cliente (ID_cliente, Nome, Tipo, CNPJ, CPF) VALUES (2, 'Maria Oliveira', 'PF', NULL, '12345678901');

INSERT INTO Fornecedor (ID_fornecedor, Nome) VALUES (1, 'Fornecedor 1');
INSERT INTO Fornecedor (ID_fornecedor, Nome) VALUES (2, 'Fornecedor 2');

INSERT INTO Produto (ID_produto, Nome, Descricao, Preco, ID_fornecedor) VALUES (1, 'Produto A', 'Descrição A', 29.99, 1);
INSERT INTO Produto (ID_produto, Nome, Descricao, Preco, ID_fornecedor) VALUES (2, 'Produto B', 'Descrição B', 49.99, 2);

INSERT INTO Pedido (ID_pedido, ID_cliente, Data, Status) VALUES (1, 1, '2023-10-01', 'Pendente');
INSERT INTO Pagamento (ID_pagamento, ID_pedido, Tipo_pagamento, Valor) VALUES (1, 1, 'Cartão', 29.99);
INSERT INTO Entrega (ID_entrega, ID_pedido, Status, Codigo_rastreamento) VALUES (1, 1, 'Aguardando', 'RASTREIO123');




SELECT c.Nome, COUNT(p.ID_pedido) AS Total_Pedidos
FROM Cliente c
LEFT JOIN Pedido p ON c.ID_cliente = p.ID_cliente
GROUP BY c.ID_cliente;



SELECT v.Nome
FROM Vendedor v
JOIN Fornecedor f ON v.ID_vendedor = f.ID_fornecedor;



SELECT p.Nome AS Produto, f.Nome AS Fornecedor, p.Preco
FROM Produto p
JOIN Fornecedor f ON p.ID_fornecedor = f.ID_fornecedor;



SELECT f.Nome AS Fornecedor, GROUP_CONCAT(p.Nome) AS Produtos
FROM Fornecedor f
JOIN Produto p ON f.ID_fornecedor = p.ID_fornecedor
GROUP BY f.ID_fornecedor;



SELECT p.Nome, p.Preco
FROM Produto p
WHERE p.Preco > 30.00
ORDER BY p.Preco DESC;


SELECT c.Nome, SUM(p.valor) AS Total_Pago
FROM Cliente c
JOIN Pedido pd ON c.ID_cliente = pd.ID_cliente
JOIN Pagamento p ON pd.ID_pedido = p.ID_pedido
GROUP BY c.ID_cliente
HAVING Total_Pago > 50.00;

