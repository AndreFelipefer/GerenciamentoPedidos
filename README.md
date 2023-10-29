<h1 align="center"> Gerenciamento de Pedidos </h1>

## Etapa 1: Criação de Tabelas e Inserção de Dados
Crie as tabelas "Clientes" e "Pedidos" com campos apropriados. Insira dados de exemplo nas tabelas para simular clientes e pedidos. Certifique-se de incluir uma chave primária em cada tabela.

![image](https://github.com/AndreFelipefer/GerenciamentoPedidos/assets/129207232/505c6e6b-b7a7-4282-aecc-3dbf22184226)

### Insert: 
Tabela Pedidos: 

![image](https://github.com/AndreFelipefer/GerenciamentoPedidos/assets/129207232/4e080205-a769-4577-bb08-8fdd7c917e7e)

Tabela Clientes: 

![image](https://github.com/AndreFelipefer/GerenciamentoPedidos/assets/129207232/274faf19-0515-472f-a641-612a4d028d25)

<hr> 

### Etapa 2: Criação de Stored Procedure

Crie uma stored procedure chamada "InserirPedido" que permite inserir um novo pedido na tabela "Pedidos" com as informações apropriadas. A stored procedure deve receber parâmetros como o ID do cliente e os detalhes do pedido. Ao término teste o funcionamento da stored procedure criada inserindo um pedido.
```SQL
DELIMITER $$

CREATE PROCEDURE InserirPedido(
  IN cliente_nome VARCHAR(255),
  IN descricao VARCHAR(255),
  IN dataPedido DATE,
  IN valor DECIMAL(10, 2)
)
BEGIN
  DECLARE cliente_id INT;

  -- Obtenha o ID do cliente com base no nome fornecido
  SELECT idClientes INTO cliente_id FROM clientes WHERE nome = cliente_nome;

  -- Insira o novo pedido na tabela Pedidos
  INSERT INTO pedidos (cliente, descricao, dataPedido, valor, Clientes_idClientes)
  VALUES (cliente_nome, descricao, dataPedido, valor, cliente_id);
END $$

DELIMITER ;
```

<hr>

### Etapa 3: Trigger

Crie uma trigger que seja acionada APÓS a inserção de um novo pedido na tabela "Pedidos". A trigger deve calcular o valor total dos pedidos para o cliente correspondente e atualizar um campo "TotalPedidos" na tabela "Clientes" com o valor total. Teste a Trigger inserindo um novo pedido na tabela "Pedidos“.
```SQL
DELIMITER $$

CREATE TRIGGER AfterInsertPedido
AFTER INSERT ON pedidos
FOR EACH ROW
BEGIN
  UPDATE clientes
  SET TotalPedidos = TotalPedidos + NEW.valor
  WHERE idClientes = NEW.Clientes_idClientes;
END $$

DELIMITER ;

```
<hr>

###  Etapa 4: View

Crie uma view chamada "PedidosClientes" que combina informações das tabelas "Clientes" e "Pedidos" usando JOIN para mostrar os detalhes dos pedidos e os nomes dos clientes.

```SQL
CREATE VIEW PedidosClientes AS
SELECT
  p.Clientes_idClientes AS ClienteID,
  c.nome AS ClienteNome,
  p.descricao AS DescricaoPedido,
  p.dataPedido AS DataPedido,
  p.valor AS ValorPedido
FROM pedidos p
INNER JOIN clientes c ON p.Clientes_idClientes = c.idClientes;

```
<hr>
### Etapa 5: Consulta com JOIN

Escreva uma consulta SQL que utiliza JOIN para listar os detalhes dos pedidos de cada cliente, incluindo o nome do cliente e o valor total de seus pedidos. Utilize a view "PedidosClientes" para essa consulta.
```SQL
ALTER TABLE clientes
ADD COLUMN TotalPedidos DECIMAL(10, 2) DEFAULT 0.00;
````
```SQL
SELECT
  pc.ClienteNome,
  pc.DescricaoPedido,
  pc.DataPedido,
  pc.ValorPedido,
  c.TotalPedidos AS ValorTotalPedidos
FROM PedidosClientes pc
INNER JOIN clientes c ON pc.ClienteID = c.idClientes;
```
