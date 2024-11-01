# Filtro2_MySQL-OrtizZully

Filtro Campus pizza

## Descripción 

Este repositorio consta una un proyecto llamado campus pizza donde su funcion  es  diseñar una base de datos que permita gestionar eficientemente los productos, combos, pedidos y clientes de una pizzería. El sistema debe almacenar información sobre pizzas, panzarottis, otros productos no elaborados (bebidas, postres, etc.), adiciones y el menú disponible. Además, se deberán registrar los pedidos de los clientes, que pueden ser para consumir en el lugar o para recoger.

## Lenguajes de Uso 

- MySQL

## Estructura de proyecto 

Consta de dos partes Modelo logico y modelo fisico.

- Modelo logico lo diseñe en DrawSQL donde cree las llaves foraneas y primaria
  ![image](https://github.com/user-attachments/assets/6864b784-af02-48d3-830c-e840ed34a4ed)

- Modelo Fisico lo diseñe en MySQL workbench
Consta de codigo donde primero cree codigo de las creaciones de tablas donde a cada tabla le inserte datos

```sql
CREATE DATABASE Pizza2_OrtizZully;

use Pizza2_OrtizZully;


CREATE TABLE Producto (
	id_producto INT PRIMARY KEY,
    nombre VARCHAR(50),
    tipo VARCHAR(70),
    precio DECIMAL(10,2),
    cantidad INT(255)
);

INSERT INTO Producto (id_producto, nombre, tipo, precio, cantidad) VALUES
(1, 'Pizza Margarita', 'Pizza', 28000.00, 4),
(2, 'Pizza Pepperoni', 'Pizza', 28000.00, 3),
(3, 'Panzarotti Clásico', 'Panzarotti',20000.00,5),
(4, 'Coca-Cola', 'Bebida', 5000.00, 1),
(5, 'Tiramisu', 'Postre', 10000.00, 4);


CREATE TABLE Ingredientes (
	id_ingredientes INT PRIMARY KEY,
    nombre VARCHAR(90)
);

INSERT INTO Ingredientes (id_ingredientes, nombre) VALUES
(1, 'Queso'),
(2, 'Tomate'),
(3, 'Pepperoni'),
(4, 'Masa'),
(5, 'Cacao');

CREATE TABLE producto_ingrediente (
	id_ingredientes INT,
    id_producto INT,
    FOREIGN KEY (id_ingredientes) REFERENCES Ingredientes (id_ingredientes),
    FOREIGN KEY (id_producto) REFERENCES Producto (id_producto)
);

INSERT INTO producto_ingrediente (id_ingredientes, id_producto) VALUES
(1, 1),  -- Queso en Pizza Margarita
(2, 1),  -- Tomate en Pizza Margarita
(1, 2),  -- Queso en Pizza Pepperoni
(2, 2),  -- Tomate en Pizza Pepperoni
(3, 2),  -- Pepperoni en Pizza Pepperoni
(4, 1),  -- Masa en Pizza Margarita
(4, 2),  -- Masa en Pizza Pepperoni
(1, 3),  -- Queso en Panzarotti
(4, 3);  -- Masa en Panzarotti

CREATE TABLE Combo (
	id_combo INT PRIMARY KEY,
    nombre VARCHAR(100),
    precio DECIMAL(10,2),
    cantidad INT(255)
);

INSERT INTO Combo (id_combo, nombre, precio, cantidad) VALUES
(1, 'Combo Familiar', 40000.00, 1),
(2, 'Combo Pareja', 35000.00, 2),
(3, 'Combo Pizza y Bebida', 25000.00, 4),
(4, 'Combo Postre y Café', 15000.00, 3),
(5, 'Combo Fiesta', 60000.00, 1);

CREATE TABLE combo_producto (
	id_combo INT,
    id_producto INT,
    FOREIGN KEY (id_producto) REFERENCES Producto (id_producto),
    FOREIGN KEY (id_combo) REFERENCES Combo (id_combo)
);
INSERT INTO combo_producto (id_combo, id_producto) VALUES
(1, 1),  -- Combo Familiar incluye Pizza Margarita
(1, 4),  -- Combo Familiar incluye Coca-Cola
(2, 2),  -- Combo Duo incluye Pizza Pepperoni
(2, 5);  -- Combo Duo incluye Tiramisu

CREATE TABLE Adicional (
	id_adicional INT PRIMARY KEY,
    nombre VARCHAR(100),
    precio DECIMAL(10,2),
    cantidad INT(255)
);

INSERT INTO Adicional (id_adicional, nombre, precio, cantidad) VALUES
(1, 'Extra Queso', 5000.00, 2),
(2, 'Champiñones Extras', 7000.00, 4),
(3, 'Salsa Picante', 2500.00,1),
(4, 'Bebida Grande', 12000.00,1);

CREATE TABLE Cliente (
	id_cliente INT PRIMARY KEY,
    nombre VARCHAR(100),
    telefono INT(12),
    email VARCHAR(100)
);

INSERT INTO Cliente (id_cliente, nombre, telefono, email) VALUES
(1, 'Juan Pérez', 1234567, 'juan.perez@example.com'),
(2, 'María López', 234567, 'maria.lopez@example.com'),
(3, 'Carlos Ruiz', 345678, 'carlos.ruiz@example.com');

CREATE TABLE pedido (
	id_pedido INT PRIMARY KEY,
    id_producto INT,
    id_cliente INT,
    fecha DATE,
	cantidad INT(100),
    id_adicional INT,
    estado_pedido VARCHAR(100),
    FOREIGN KEY (id_cliente) REFERENCES Cliente (id_cliente),
    FOREIGN KEY (id_producto) REFERENCES Producto (id_producto),
    FOREIGN KEY (id_adicional) REFERENCES Adicional (id_adicional)
);

INSERT INTO pedido (id_pedido, id_producto, id_cliente, fecha, cantidad, id_adicional, estado_pedido) VALUES
(3, 3, 1, '2024-10-31', 1, NULL, 'Recoger'),  -- id_pedido = 3: Panzarotti por Juan Pérez
(4, 4, 2, '2024-10-31', 2, NULL, 'Consumir'),  -- id_pedido = 4: 2 Coca-Colas por María López
(5, 5, 1, '2024-10-31', 1, NULL, 'Recoger'),  -- id_pedido = 5: Tiramisu por Juan Pérez
(6, 1, 2, '2024-11-01', 1, 1, 'Consumir'),  -- id_pedido = 6: Pizza Margarita con Extra Queso por María López
(7, 2, 1, '2024-11-01', 1, 2, 'Recoger');  -- id_pedido = 7: Pizza Pepperoni con Salsa Picante por Juan Pérez

CREATE TABLE Inventario (
	id_pedido INT PRIMARY KEY,
    id_producto INT,
	cantidad INT(100),
    id_adicional INT,
    id_combo INT,
    precio  DECIMAL(10,2),
    FOREIGN KEY (id_producto) REFERENCES Producto (id_producto),
    FOREIGN KEY (id_adicional) REFERENCES Adicional (id_adicional),
    FOREIGN KEY (id_pedido) REFERENCES pedido (id_pedido),
    FOREIGN KEY (id_combo) REFERENCES Combo (id_combo)
);


INSERT INTO Inventario (id_pedido, id_producto, cantidad, id_adicional, id_combo, precio) VALUES
(3, 3, 5, NULL, NULL, 90000.00),  
(4, 4, 3, NULL, NULL, 60000.00),  
(5, 5, 14, NULL, NULL, 90000.00), 
(6, 1, 9, 1, NULL, 20000.00), 
(7, 2, 2, 2, NULL, 50000.00); 

```
- con su modelo fisico en sql
  ![image](https://github.com/user-attachments/assets/fff9e56e-a03c-4d43-a541-abde7f20c92c)

## CONSULTAS 
1. Productos más vendidos (pizza, panzarottis, bebidas, etc.
   ![image](https://github.com/user-attachments/assets/4f821bbc-73b2-4268-8ccf-b10848107ee1)
   ![image](https://github.com/user-attachments/assets/0ae928f6-c9c7-4860-8499-557124d46858)
   * suma la cantidad del id_producto donde se va a mostrar en el cuadro como total_venta y lo va a ordenar de mayor a menor
     
2. Total de ingresos generados por cada combo.
  ![image](https://github.com/user-attachments/assets/4d842308-bfe1-49d0-a65b-7a58ac57152f)
  ![image](https://github.com/user-attachments/assets/5f79cc8c-49cf-4f45-b3a3-ed7461c357fe)
  * En la tabla del cuadro combo donde mostrara todos los precio con el titulo de total_ingreso

3. Pedidos realizados para recoger vs. comer en la pizzería.
   ![image](https://github.com/user-attachments/assets/00a9d8c1-e67d-4353-8846-3a3d15470994)
   ![image](https://github.com/user-attachments/assets/ce059227-5e09-4e71-b6fa-587601278049)
   * Mostrara el total de estado de pedido que tenga ya sea consumir o recoger de la tabla pedido

4. Adiciones más solicitadas en pedidos personalizados.
   ![image](https://github.com/user-attachments/assets/455f67e4-1951-4c3c-9ee9-28a280e4b194)
   ![image](https://github.com/user-attachments/assets/803f58a3-76c5-49e1-aae8-f22defd09c36)

5. Cantidad total de productos vendidos por categoría.
   ![image](https://github.com/user-attachments/assets/08a4350f-92ef-4250-8f37-395bfd5be9ac)
   ![image](https://github.com/user-attachments/assets/02be9e99-eccd-4407-982e-b43f7322a42d)




   


## Credito 

Este trabajo fue elaborado por Zully Fernada Ortiz Avendaño, me disculpo por la entrega minima de consultas, no supe controlar mi estres y frustracion y ya cuando faltaba poco tiempo para terminar empece a aclarar todo, pero la parte logica y fisica de codigo esta terminada otra vez me disculpo con ustedes. 
