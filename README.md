# Sales & Production — SQL Server Database

Este projeto de teste para CodeGroup que contém o script de criação de um banco de dados relacional baseado em um modelo de vendas e produção, projetado para SQL Server. Inclui tabelas para controle de clientes, funcionários, pedidos, produtos, estoque, marcas e categorias, além de consultas úteis para análise de dados.

---


### Estrutura do Banco de Dados

O banco de dados é dividido em dois domínios:

### Sales
- `customers` — Clientes.

- `staffs` — Funcionários.

- `stores` — Lojas.

- `orders` — Pedidos.

- `order_items` — Itens de pedidos.

### Production

- `products` — Produtos.

- `categories` — Categorias de produtos.

- `brands` — Marcas.

- `stocks` — Estoque de produtos.

---

### Script de Criação das Tabelas

```sql
CREATE DATABASE SalesProductionDB;
GO

USE SalesProductionDB;
GO

-- Tabelas da área de Sales
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    first_name NVARCHAR(50),
    last_name NVARCHAR(50),
    phone NVARCHAR(20),
    email NVARCHAR(100),
    street NVARCHAR(100),
    city NVARCHAR(50),
    state NVARCHAR(50),
    zip_code NVARCHAR(10)
);

CREATE TABLE staffs (
    staff_id INT PRIMARY KEY,
    first_name NVARCHAR(50),
    last_name NVARCHAR(50),
    email NVARCHAR(100),
    phone NVARCHAR(20),
    active BIT,
    store_id INT,
    manager_id INT,
    FOREIGN KEY (store_id) REFERENCES stores(store_id),
    FOREIGN KEY (manager_id) REFERENCES staffs(staff_id)
);

CREATE TABLE stores (
    store_id INT PRIMARY KEY,
    store_name NVARCHAR(100),
    phone NVARCHAR(20),
    email NVARCHAR(100),
    street NVARCHAR(100),
    city NVARCHAR(50),
    state NVARCHAR(50),
    zip_code NVARCHAR(10)
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_status NVARCHAR(20),
    order_date DATE,
    required_date DATE,
    shipped_date DATE,
    store_id INT,
    staff_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    FOREIGN KEY (store_id) REFERENCES stores(store_id),
    FOREIGN KEY (staff_id) REFERENCES staffs(staff_id)
);

CREATE TABLE order_items (
    order_id INT,
    item_id INT,
    product_id INT,
    quantity INT,
    list_price DECIMAL(10, 2),
    discount DECIMAL(5, 2),
    PRIMARY KEY (order_id, item_id),
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

-- Tabelas da área de Production
CREATE TABLE categories (
    category_id INT PRIMARY KEY,
    category_name NVARCHAR(100)
);

CREATE TABLE brands (
    brand_id INT PRIMARY KEY,
    brand_name NVARCHAR(100)
);

CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name NVARCHAR(100),
    brand_id INT,
    category_id INT,
    model_year INT,
    list_price DECIMAL(10, 2),
    FOREIGN KEY (brand_id) REFERENCES brands(brand_id),
    FOREIGN KEY (category_id) REFERENCES categories(category_id)
);

CREATE TABLE stocks (
    store_id INT,
    product_id INT,
    quantity INT,
    PRIMARY KEY (store_id, product_id),
    FOREIGN KEY (store_id) REFERENCES stores(store_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

  
<!-- Espaço adicionado para organização e clareza -->
## Consultas SQL

### 1) Listar todos os Clientes que não tenham realizado uma compra:
```sql
SELECT c.customer_id, c.first_name, c.last_name
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;
```



### 2) Listar os Produtos que não tenham sido comprados:

```sql
SELECT p.product_id, p.product_name
FROM products p
LEFT JOIN order_items oi ON p.product_id = oi.product_id
WHERE oi.product_id IS NULL;
```

### 3) Listar os Produtos sem Estoque:

```sql
SELECT p.product_id, p.product_name
FROM products p
LEFT JOIN stocks s ON p.product_id = s.product_id
WHERE s.product_id IS NULL;
```

### 4) Agrupar a quantidade de vendas que uma determinada Marca por Loja:

```sql
SELECT s.store_name, b.brand_name, COUNT(oi.product_id) AS total_vendas
FROM order_items oi
INNER JOIN products p ON oi.product_id = p.product_id
INNER JOIN brands b ON p.brand_id = b.brand_id
INNER JOIN orders o ON oi.order_id = o.order_id
INNER JOIN stores s ON o.store_id = s.store_id
WHERE b.brand_name = 'NomeDaMarca'
GROUP BY s.store_name, b.brand_name
ORDER BY s.store_name, b.brand_name;
```

### 5) Listar os Funcionários que não estejam relacionados a um Pedido:

```sql
SELECT s.staff_id, s.first_name, s.last_name
FROM staffs s
LEFT JOIN orders o ON s.staff_id = o.staff_id
WHERE o.order_id IS NULL;
```


## Observações
- Banco de dados projetado para estudos de modelagem, análise de dados e práticas de SQL.

- Compatível com SQL Server 2017 ou superior.

- Você pode popular as tabelas com dados fictícios para treinar consultas.
