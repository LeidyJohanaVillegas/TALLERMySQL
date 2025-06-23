# TALLERMySQL

# Descripción del taller.

Este taller está diseñado para profundizar en el manejo y optimización de bases de datos MySQL. A través de ejercicios prácticos, se explorarán temas avanzados para reforzar el conocimiento en normalización, joins, consultas complejas, subconsultas, procedimientos almacenados, funciones definidas por el usuario y triggers. Requisitos previos: Conocimiento básico de SQL y MySQL MySQL instalado y configurado en tu máquina Objetivos: Al finalizar este taller, el participante será capaz de: 1. Diseñar bases de datos optimizadas mediante técnicas de normalización. 2. Realizar consultas avanzadas en múltiples tablas. 3. Utilizar subconsultas para consultas complejas. 4. Crear y ejecutar procedimientos almacenados y funciones definidas por el usuario. 5. Implementar triggers para automatizar operaciones en la base de datos.


## Base de datos
```
CREATE DATABASE vtaszfs;
USE vtaszfs;

CREATE TABLE UbicacionClientes (
  id INT PRIMARY KEY AUTO_INCREMENT,
  cliente_id INT,
  direccion VARCHAR(255),
  ciudad VARCHAR(100),
  estado VARCHAR(50),
  codigo_postal VARCHAR(10),
  pais VARCHAR(50)
); 

CREATE TABLE Clientes (
  id INT PRIMARY KEY AUTO_INCREMENT, 
  nombre VARCHAR(100),
  apellido VARCHAR(50),
  email VARCHAR(100) UNIQUE,
  direccion_id INT,
  FOREIGN KEY (direccion_id) REFERENCES UbicacionClientes(id)
);

CREATE TABLE Pedidos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  cliente_id INT, 
  fecha DATE,
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);

CREATE TABLE HistorialPedidos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  pedido_id INT,
  cliente_id INT,
  fecha_anterior DATE,
  total_anterior DECIMAL(10,2),
  estado_anterior VARCHAR(50),
  fecha_modificacion DATE,
  usuario_modificacion VARCHAR(100),
  FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);

CREATE TABLE Puestos (
  id INT PRIMARY KEY AUTO_INCREMENT, 
  nombre_puesto VARCHAR(50) UNIQUE,
  salario DECIMAL(10,2)
);

CREATE TABLE DatosEmpleados (
  id INT PRIMARY KEY,
  nombre VARCHAR(50),
  apellido VARCHAR(50),
  fecha_contratacion DATE,
  puesto_id INT,
  FOREIGN KEY (puesto_id) REFERENCES Puestos(id)
);

CREATE TABLE Proveedores (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(100),
  dirección VARCHAR(255)
);

CREATE TABLE ContactoProveedor (
  id INT PRIMARY KEY AUTO_INCREMENT,
  proveedor_id INT,
  nombreContacto VARCHAR(100),
  telefono VARCHAR(20),
  FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id)
);

CREATE TABLE TipoTelefonos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  tipo_tel VARCHAR(20)
);

CREATE TABLE Telefonos ( 
  id INT PRIMARY KEY AUTO_INCREMENT,
  cliente_id INT,
  num_tel VARCHAR(10),
  tipo_id INT,
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id),
  FOREIGN KEY (tipo_id) REFERENCES TipoTelefonos(id)
);

CREATE TABLE TiposProductos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  tipo_nombre VARCHAR(100) NOT NULL, 
  descripcion TEXT,
  padre_id INT, 
  FOREIGN KEY (padre_id) REFERENCES TiposProductos(id)
); 

CREATE TABLE Productos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(100),
  precio DECIMAL(10,2),
  proveedor_id INT, 
  tipo_id INT,
  FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id),
  FOREIGN KEY (tipo_id) REFERENCES TiposProductos(id)
);

CREATE TABLE DetallesPedido (
  id INT PRIMARY KEY AUTO_INCREMENT,
  pedido_id INT, 
  producto_id INT,
  cantidad INT, 
  precio DECIMAL(10,2),
  FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
  FOREIGN KEY (producto_id) REFERENCES Productos(id)
);

CREATE TABLE Empleados (
  id INT PRIMARY KEY AUTO_INCREMENT, 
  nombre VARCHAR(100), 
  puesto VARCHAR(50),
  salario DECIMAL(10,2),
  fecha_contratacion DATE
);

CREATE TABLE EmpleadoProveedor (
  empleado_id INT,
  proveedor_id INT,
  PRIMARY KEY (empleado_id, proveedor_id), 
  FOREIGN KEY (empleado_id) REFERENCES Empleados(id),
  FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id)
);

CREATE TABLE Ubicaciones( 
  id INT PRIMARY KEY AUTO_INCREMENT,
  entidad_tipo VARCHAR(50),
  entidad_id INT,
  dirección VARCHAR(255), 
  ciudad VARCHAR(100), 
  estado VARCHAR(50), 
  código_postal VARCHAR(10),
  país VARCHAR(50)
);
```

## Datos ingresados
```
INSERT INTO UbicacionClientes (direccion, ciudad, estado, codigo_postal, pais) VALUES
('Calle 123', 'Bogotá', 'Cundinamarca', '110111', 'Colombia'),
('Av. Central 456', 'Medellín', 'Antioquia', '050021', 'Colombia');

INSERT INTO Clientes (nombre, apellido, email, direccion_id) VALUES
('Juan', 'Pérez', 'juan@example.com', 1),
('María', 'Gómez', 'maria@example.com', 2);

INSERT INTO Pedidos (cliente_id, fecha) VALUES
(1, '2025-06-01'),
(2, '2025-06-15');

INSERT INTO HistorialPedidos (pedido_id, cliente_id, fecha_anterior, total_anterior, estado_anterior, fecha_modificacion, usuario_modificacion) VALUES
(1, 1, '2025-05-30', 200000, 'Pendiente', '2025-06-01', 'admin'),
(2, 2, '2025-06-10', 350000, 'Entregado', '2025-06-15', 'admin');

INSERT INTO Puestos (nombre_puesto, salario) VALUES
('Vendedor', 1800000),
('Repartidor', 1500000);

INSERT INTO DatosEmpleados (id, nombre, apellido, fecha_contratacion, puesto_id) VALUES
(1, 'Carlos', 'Ramírez', '2023-02-01', 1),
(2, 'Ana', 'López', '2024-05-15', 2);

INSERT INTO Proveedores (nombre, dirección) VALUES
('Proveedora Andina', 'Carrera 10 #12-34'),
('Distribuciones Norte', 'Av. Norte #45-67');

INSERT INTO TipoTelefonos (tipo_tel) VALUES
('Móvil'), ('Fijo');

INSERT INTO Telefonos (cliente_id, num_tel, tipo_id) VALUES
(1, '3205551234', 1),
(2, '6041234567', 2);

INSERT INTO TiposProductos (tipo_nombre, descripcion, padre_id) VALUES
('Electrodomésticos', 'Productos eléctricos para el hogar', NULL),
('Lavadoras', 'Lavadoras automáticas', 1);

INSERT INTO Productos (nombre, precio, proveedor_id, tipo_id) VALUES
('Lavadora LG 20kg', 1800000, 1, 2),
('Lavadora Samsung 18kg', 1650000, 2, 2);
-- Insertamos un nuevo producto que no ha sido pedido
INSERT INTO Productos (nombre, precio) VALUES ('Producto sin pedido', 99.99);
INSERT INTO Productos (nombre, precio, proveedor_id, tipo_id) VALUES
('Repuesto Bomba de agua', 25000, 1, 3),
('Lavadora Whirlpool 22kg', 1900000, 2, 2),
('Panel Digital LG', 120000, 1, 4),
('Lavadora Haceb 16kg', 1500000, 3, 2),
('Tubería de desagüe universal', 18000, 2, 3);

INSERT INTO DetallesPedido (pedido_id, producto_id, cantidad, precio) VALUES
(1, 1, 1, 1800000),
(2, 2, 2, 1650000);

INSERT INTO Empleados (nombre, puesto, salario, fecha_contratacion) VALUES
('Carlos Ramírez', 'Vendedor', 1800000, '2023-02-01'),
('Ana López', 'Repartidor', 1500000, '2024-05-15');

INSERT INTO EmpleadoProveedor (empleado_id, proveedor_id) VALUES
(1, 1),
(2, 2);

INSERT INTO Ubicaciones (entidad_tipo, entidad_id, dirección, ciudad, estado, código_postal, país) VALUES
('Cliente', 1, 'Calle 123', 'Bogotá', 'Cundinamarca', '110111', 'Colombia'),
('Cliente', 2, 'Av. Central 456', 'Medellín', 'Antioquia', '050021', 'Colombia'),
('Proveedor', 1, 'Carrera 10 #12-34', 'Bogotá', 'Cundinamarca', '110111', 'Colombia'),
('Proveedor', 2, 'Av. Norte #45-67', 'Medellín', 'Antioquia', '050021', 'Colombia');
```


## EJERCICIOS 

#1. NORMALIZACION:
```
1.1. Crear una tabla HistorialPedidos que almacene cambios en los pedidos.
RTA:
--Tabla HistorialPedidos
CREATE TABLE HistorialPedidos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  pedido_id INT,
  cliente_id INT,
  fecha_anterior DATE,
  total_anterior DECIMAL(10,2),
  estado_anterior VARCHAR(
  fecha_modificacion DATE,
  usuario_modificacion VARCHAR(100),
  FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);
Explicación:
pedido_id: Relaciona el historial con el pedido original.
cliente_id: Para saber a qué cliente pertenecía el pedido modificado.
fecha_anterior y total_anterior: Valores antes de que fueran modificados.
fecha_modificacion: Cuándo se hizo el cambio.
usuario_modificacion: Quién hizo el cambio.

1.2. Evaluar la tabla Clientes para eliminar datos redundantes y normalizar hasta 3NF.
RTA: 
--Tabla original
CREATE TABLE Clientes (
  id INT PRIMARY KEY AUTO_INCREMENT, 
  nombre VARCHAR(100),
  email VARCHAR(100) UNIQUE
);
Análisis:
1NF = los campos contienen solo un valor, cada fila es unica, sin grupos repetitivos.
2NF = Requiere estar en 1NF y que cada campo dependa de toda la clave primaria, como id en una clave simple (no compuesta).
3NF = Requiere estar en 2NF y que no haya dependencias transitivas, se podría separar la estructura de nombre y dejar nombre y apellido.
--Tabla Clientes normalizada 3NF
CREATE TABLE Clientes (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(50),
  apellido VARCHAR(50), 
  email VARCHAR(100) UNIQUE
);
Explicación:
Se eliminan ambigüedades, se mejora la búsqueda y se respeta la integridad de los datos al llegar a 3NF.

1.3. Separar la tabla Empleados en una tabla de DatosEmpleados y otra para Puestos 
RTA:
--Tabla Empleados ORIGINAL
CREATE TABLE Empleados (
  id INT PRIMARY KEY AUTO_INCREMENT, 
  nombre VARCHAR(100), 
  puesto VARCHAR(50),
  salario DECIMAL(10,2),
  fecha_contratacion DATE
);
TABLAS NORMALIZADAS
--Tabla DatosEmpleados
CREATE TABLE DatosEmpleados (
  id INT PRIMARY KEY,
  nombre VARCHAR(50),
  apellido VARCHAR(50),
  fecha_contratacion DATE,
  puesto_id INT,
  FOREIGN KEY (puesto_id) REFERENCES Puestos(id)
);
--Tabla Puestos 
CREATE TABLE Puestos (
  id INT PRIMARY KEY AUTO_INCREMENT, 
  nombre_puesto VARCHAR(50) UNIQUE,
  salario DECIMAL(10,2)
);
Explicación:
nombre_puesto: Evitamos repetir el nombre del puesto muchas veces.
salario: El salario se centraliza: si un puesto sube de sueldo, lo cambias una sola vez en la tabla Puestos. Si un mismo puesto lo ocupan varios empleados, no repetimos la información.

1.4. Revisar la relación Clientes y UbicacionCliente para evitar duplicación de datos
RTA:
--TABLAS ORIGINALES
--Tabla Clientes
CREATE TABLE Clientes (
  id INT PRIMARY KEY AUTO_INCREMENT, 
  nombre VARCHAR(100),
  email VARCHAR(100) UNIQUE
);
--Tabla UbicacionClientes
CREATE TABLE UbicacionClientes (
  id INT PRIMARY KEY AUTO_INCREMENT,
  cliente_id INT,
  direccion VARCHAR(255),
  ciudad VARCHAR(100),
  estado VARCHAR (50),
  codigo_postal VARCHAR(10),
  pais VARCHAR(50),
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);
Análisis: Cada cliente tiene una única ubicación (o varias), pero todos los campos de dirección están siendo repetidos incluso si son iguales para varios clientes. 
--Tablas Normalizadas
--Tabla UbicacionClientes 
CREATE TABLE UbicacionClientes (
  id INT PRIMARY KEY AUTO_INCREMENT,
  direccion VARCHAR(255),
  ciudad VARCHAR(100),
  estado VARCHAR(50),
  codigo_postal VARCHAR(10),
  pais VARCHAR(50)
);
--Tabla Clientes 
CREATE TABLE Clientes (
  id INT PRIMARY KEY AUTO_INCREMENT, 
  nombre VARCHAR(100),
  email VARCHAR(100) UNIQUE,
  direccion_id INT,
  FOREIGN KEY (direccion_id) REFERENCES UbicaciónCliente(id)
);
Explicación:
Menos redundancia: una dirección se guarda solo una vez, aunque la usen varios clientes.
Si necesita actualizar la ciudad o estado, lo haces en una sola fila.
Mejora la integridad de los datos y simplifica mantenimiento.

1.5. Normalizar Proveedores para tener ContactoProveedores en otra tabla.
RTA: 
--Tabla Proveedores ORIGINAL
CREATE TABLE Proveedores (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(100),
  contacto VARCHAR(100),
  telefono VARCHAR(20),
  direccion VARCHAR(255)
);
Análisis:
1NF: Cumple, ya que todos los atributos son atómicos (un solo valor por campo) y cada fila es única.
2NF: Está en 1NF y como la clave es simple (id), todos los campos dependen completamente de la clave.
3NF: Hay dependencia transitiva, porque contacto, telefono y direccion están relacionados más con el contacto del proveedor que con el proveedor en sí.
--Tablas Normalizadas
--Tabla Proveedores
CREATE TABLE Proveedores (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(100),
  dirección VARCHAR(255)
);
--Tabla ContactoProveedores
CREATE TABLE ContactoProveedor (
  id INT PRIMARY KEY AUTO_INCREMENT,
  proveedor_id INT,
  nombreContacto VARCHAR(100),
  telefono VARCHAR(20),
  FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id)
);
Explicacion:
Con esto se logra una estructura más limpia, evitando duplicación de datos cuando varios proveedores tienen más de un contacto o cambian de contacto.

1.6. Crear una tabla de Telefonos para almacenar múltiples números por cliente.
RTA:
--Tabla TipoTelefonos
CREATE TABLE TipoTelefonos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  tipo_tel VARCHAR(20)
);
--Tabla Telefonos
CREATE TABLE Telefonos ( 
  id INT PRIMARY KEY AUTO_INCREMENT,
  cliente_id INT,
  num_tel VARCHAR(10)
  tipo_id INT,
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id),
  FOREIGN KEY (tipo_id) REFERENCES TipoTelefonos(id)
);
Explicacion:
TipoTelefonos: Para clasificar los tipos de números: Casa, Móvil, Trabajo, etc.
Telefonos: Permite almacenar múltiples números por clientte, cada uno con su tipo de telefono.
cliente_id: Como clave foreanea para relacionar con clientes. 
tipo_id: Referencia la tabla TipoTelefonos para indicar el tipo de número.

1.7. Transformar TiposProductos en una relación categórica jerárquica.
RTA:
--Tabla TiposProducto ORIGINAL
CREATE TABLE TiposProductos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  tipo_nombre VARCHAR(100), 
  descripción TEXT
);
-- Tabla Version jerárquica de TiposProducto
CREATE TABLE TiposProductos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  tipo_nombre VARCHAR(100) NOT NULL, 
  descripcion TEXT,
  padre_id INT, 
  FOREIGN KEY (padre_id) REFERENCES TiposProductos(id)
); 
Explicación:
Permite ordenar y clasificar los productos por niveles, ideal si tienes categorías generales y especificas.

1.8. Normalizar Pedidos y DetallesPedido para evitar inconsistencias de precios
RTA:
--Tablas originales
--Tabla Pedidos
CREATE TABLE Pedidos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  cliente_id INT, 
  fecha DATE,
  total DECIMAL(10,2), //Se elimina 
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);

--Tabla DetallesPedido
CREATE TABLE DetallesPedido ( 
  id INT PRIMARY KEY AUTO_INCREMENT,
  pedido_id INT, 
  producto_id INT,
  cantidad INT,
  precio DECIMAL(10,2),
  FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
  FOREIGN KEY (producto_id) REFERENCES Productos(id)
);

--Tablas normalizadas
--Tabla pedidos
CREATE TABLE Pedidos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  cliente_id INT, 
  fecha DATE,
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);
--Tabla DetallesPedido
CREATE TABLE DetallesPedido (
  id INT PRIMARY KEY AUTO_INCREMENT,
  pedido_id INT, 
  producto_id INT,
  cantidad INT, 
  precio DECIMAL(10,2),
  FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
  FOREIGN KEY (producto_id) REFERENCES Productos(id)
);
Explicación:
No se repite la información, los precios se guardan por pedidos, el total del pedido se puede calcular automáticamente.

1.9. Usar una relación de muchos a muchos para Empleados y Proveedores.
RTA:
--Tabla Empleados
CREATE TABLE Empleados (
  id INT PRIMARY KEY AUTO_INCREMENT, 
  nombre VARCHAR(100), 
  puesto VARCHAR(50),
  salario DECIMAL(10,2),
  fecha_contratacion DATE
);
--Tabla Proveedores
CREATE TABLE Proveedores (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(100),
  contacto VARCHAR(100),
  telefono VARCHAR(20),
  direccion VARCHAR(255)
);
Análisis: Para ello se necesita una tabla intermedia para la relación de muchos a muchos
--Tabla EmpleadoProveedor
CREATE TABLE EmpleadoProveedor (
  empleado_id INT,
  proveedor_id INT,
  PRIMARY KEY (empleado_id, proveedor_id), 
  FOREIGN KEY (empleado_id) REFERENCES Empleados(id),
  FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id)
); 
Explicación:
Ya que no es posible representar directamente una relación de muchos a muchos entre dos tablas por ende se crea la tabla intermedio EmpleadoProveedor.

1.10. Convertir la tabla UbicacionCliente en una relación genérica de Ubicaciones 
RTA:
--Tabla UbicacionCliente ORIGINAL
CREATE TABLE UbicacionCliente (
  id INT PRIMARY KEY AUTO_INCREMENT,
  cliente_id INT,
  direccion VARCHAR(255),
  ciudad VARCHAR(100),
  estado VARCHAR (50),
  codigo_postal VARCHAR(10),
  pais VARCHAR(50),
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);
--Tabla Ubicaciones generica
CREATE TABLE Ubicaciones( 
  id INT PRIMARY KEY AUTO_INCREMENT,
  entidad_tipo VARCHAR(50),
  entidad_id INT,
  dirección VARCHAR(255), 
  ciudad VARCHAR(100), 
  estado VARCHAR(50), 
  código_postal VARCHAR(10),
  país VARCHAR(50)
);
Explicación:
Evita duplicar tablas con la misma estructura, facilita las consultas generales de ubicaciones y mejora la escalabilidad del modelo.
```

#2. JOINS:
```
2.1. Obtener la lista de todos los pedidos con los nombres de clientes usando INNER JOIN 
RTA:
SELECT 
  Pedidos.id AS pedido_id,
  Clientes.nombre,
  Clientes.apellido,
  Pedidos.fecha
FROM Pedidos
INNER JOIN Clientes ON Pedidos.cliente_id = Clientes.id;
+-----------+--------+----------+------------+
| pedido_id | nombre | apellido | fecha      |
+-----------+--------+----------+------------+
|         1 | Juan   | Pérez    | 2025-06-01 |
|         2 | María  | Gómez    | 2025-06-15 |
+-----------+--------+----------+------------+

2.2. Listar los productos y proveedores que los suministran con INNER JOIN.
RTA:
SELECT 
  Productos.nombre AS producto,
  Proveedores.nombre AS proveedor
FROM Productos
INNER JOIN Proveedores ON Productos.proveedor_id = Proveedores.id;
+-----------------------+----------------------+
| producto              | proveedor            |
+-----------------------+----------------------+
| Lavadora LG 20kg      | Proveedora Andina    |
| Lavadora Samsung 18kg | Distribuciones Norte |
+-----------------------+----------------------+

2.3. Mostrar los pedidos y las ubicaciones de los clientes con LEFT JOIN. 
RTA:
SELECT 
  Pedidos.id AS pedido_id,
  Clientes.nombre,
  UbicacionClientes.direccion,
  UbicacionClientes.ciudad,
  UbicacionClientes.estado
FROM Pedidos
LEFT JOIN Clientes ON Pedidos.cliente_id = Clientes.id
LEFT JOIN UbicacionClientes ON Clientes.direccion_id = UbicacionClientes.id;
+-----------+--------+-----------------+-----------+--------------+
| pedido_id | nombre | direccion       | ciudad    | estado       |
+-----------+--------+-----------------+-----------+--------------+
|         1 | Juan   | Calle 123       | Bogotá    | Cundinamarca |
|         2 | María  | Av. Central 456 | Medellín  | Antioquia    |
+-----------+--------+-----------------+-----------+--------------+

2.4. Consultar los empleados que han registrado pedidos, incluyendo empleados sin pedidos ( LEFT JOIN ). 
RTA:
SELECT
  E.id AS empleados_id,
  E.nombre AS empleados_nombre,
  P.id AS pedido_id,
  P.fecha AS pedidos_fecha
FROM Empleados E
LEFT JOIN Pedidos P ON E.id = P.empleado_id;
+--------------+------------------+-----------+---------------+
| empleados_id | empleados_nombre | pedido_id | pedidos_fecha |
+--------------+------------------+-----------+---------------+
|            1 | Carlos Ramírez   |      NULL | NULL          |
|            2 | Ana López        |      NULL | NULL          |
+--------------+------------------+-----------+---------------+

2.5. Obtener el tipo de producto y los productos asociados con INNER JOIN. 
RTA:
SELECT 
    TiposProductos.tipo_nombre AS tipo,
    Productos.nombre AS producto,
    Productos.precio
FROM Productos
INNER JOIN TiposProductos ON Productos.tipo_id = TiposProductos.id;
+-----------+-----------------------+------------+
| tipo      | producto              | precio     |
+-----------+-----------------------+------------+
| Lavadoras | Lavadora Samsung 18kg | 1650000.00 |
| Lavadoras | Lavadora LG 20kg      | 1800000.00 |
+-----------+-----------------------+------------+

2.6. Listar todos los clientes y el número de pedidos realizados con COUNT y GROUP BY. 
RTA:
SELECT 
    Clientes.id,
    CONCAT(Clientes.nombre, ' ', Clientes.apellido) AS cliente,
    COUNT(Pedidos.id) AS total_pedidos
FROM Clientes
LEFT JOIN Pedidos ON Clientes.id = Pedidos.cliente_id
GROUP BY Clientes.id, Clientes.nombre, Clientes.apellido;
+----+---------------+---------------+
| id | cliente       | total_pedidos |
+----+---------------+---------------+
|  1 | Juan Pérez    |             1 |
|  2 | María Gómez   |             1 |
+----+---------------+---------------+

2.7. Combinar Pedidos y Empleados para mostrar qué empleados gestionaron pedidos específicos. 
RTA:
SELECT
    Pedidos.id AS pedido_id,
    Pedidos.fecha,
    Empleados.id AS empleado_id,
    Empleados.nombre AS nombre_empleado,
    Empleados.puesto
FROM Pedidos
LEFT JOIN Empleados ON Pedidos.empleado_id = Empleados.id;
+-----------+------------+-------------+-----------------+--------+
| pedido_id | fecha      | empleado_id | nombre_empleado | puesto |
+-----------+------------+-------------+-----------------+--------+
|         1 | 2025-06-01 |        NULL | NULL            | NULL   |
|         2 | 2025-06-15 |        NULL | NULL            | NULL   |
+-----------+------------+-------------+-----------------+--------+

2.8. Mostrar productos que no han sido pedidos ( RIGHT JOIN ). 
RTA:
SELECT 
    Productos.id,
    Productos.nombre,
    Productos.precio
FROM detallespedido
RIGHT JOIN Productos ON detallespedido.producto_id = Productos.id
WHERE detallespedido.producto_id IS NULL;
+----+---------------------+--------+
| id | nombre              | precio |
+----+---------------------+--------+
|  3 | Producto sin pedido |  99.99 |
+----+---------------------+--------+

2.9. Mostrar el total de pedidos y ubicación de clientes usando múltiples JOIN. 
RTA:
SELECT 
    Clientes.id AS cliente_id,
    Clientes.nombre AS cliente_nombre,
    Ubicaciones.dirección,
    Ubicaciones.ciudad,
    Ubicaciones.estado,
    Ubicaciones.país,
    COUNT(Pedidos.id) AS total_pedidos
FROM Clientes
LEFT JOIN Pedidos ON Pedidos.cliente_id = Clientes.id
LEFT JOIN Ubicaciones ON Ubicaciones.entidad_tipo = 'Cliente' AND Ubicaciones.entidad_id = Clientes.id
GROUP BY 
    Clientes.id, 
    Clientes.nombre,
    Ubicaciones.dirección,
    Ubicaciones.ciudad,
    Ubicaciones.estado,
    Ubicaciones.país;
+------------+----------------+-----------------+-----------+--------------+----------+---------------+
| cliente_id | cliente_nombre | dirección       | ciudad    | estado       | país     | total_pedidos |
+------------+----------------+-----------------+-----------+--------------+----------+---------------+
|          1 | Juan           | Calle 123       | Bogotá    | Cundinamarca | Colombia |             1 |
|          2 | María          | Av. Central 456 | Medellín  | Antioquia    | Colombia |             1 |
+------------+----------------+-----------------+-----------+--------------+----------+---------------+

2.10. Unir Proveedores , Productos , y TiposProductos para un listado completo de inventario.
RTA:SELECT 
    p.id AS producto_id,
    p.nombre AS nombre_producto,
    p.precio,
    pr.nombre AS proveedor,
    pr.dirección AS direccion_proveedor,
    tp.tipo_nombre AS tipo_producto,
    tp.descripcion
FROM Productos p
JOIN Proveedores pr ON p.proveedor_id = pr.id
JOIN TiposProductos tp ON p.tipo_id = tp.id;
+-------------+-----------------------+------------+----------------------+---------------------+---------------+------------------------+
| producto_id | nombre_producto       | precio     | proveedor            | direccion_proveedor | tipo_producto | descripcion            |
+-------------+-----------------------+------------+----------------------+---------------------+---------------+------------------------+
|           2 | Lavadora Samsung 18kg | 1650000.00 | Distribuciones Norte | Av. Norte #45-67    | Lavadoras     | Lavadoras automáticas  |
|           1 | Lavadora LG 20kg      | 1800000.00 | Proveedora Andina    | Carrera 10 #12-34   | Lavadoras     | Lavadoras automáticas  |
+-------------+-----------------------+------------+----------------------+---------------------+---------------+------------------------+
```

# 3. CONSULTAS SIMPLES
```
3.1. Seleccionar todos los productos con precio mayor a $50. 
RTA:
SELECT id, nombre, precio
FROM Productos
WHERE precio > 50.00;
+----+-----------------------+------------+
| id | nombre                | precio     |
+----+-----------------------+------------+
|  1 | Lavadora LG 20kg      | 1800000.00 |
|  2 | Lavadora Samsung 18kg | 1650000.00 |
|  3 | Producto sin pedido   |      99.99 |
+----+-----------------------+------------+

3.2. Consultar clientes registrados en una ciudad específica. 
RTA:
SELECT C.id, C.nombre, U.dirección, U.ciudad, U.estado, U.país
FROM Clientes C
JOIN Ubicaciones U
  ON U.entidad_tipo = 'cliente' AND U.entidad_id = C.id
WHERE U.ciudad = 'Medellin';
+----+--------+-----------------+-----------+-----------+----------+
| id | nombre | dirección       | ciudad    | estado    | país     |
+----+--------+-----------------+-----------+-----------+----------+
|  2 | María  | Av. Central 456 | Medellín  | Antioquia | Colombia |
+----+--------+-----------------+-----------+-----------+----------+

3.3. Mostrar empleados contratados en los últimos 2 años. 
RTA:
SELECT id, nombre
FROM Empleados
WHERE fecha_contratacion >= DATE_SUB(CURDATE(), INTERVAL 2 YEAR);
+----+------------+
| id | nombre     |
+----+------------+
|  2 | Ana López  |
+----+------------+

3.4. Seleccionar proveedores que suministran más de 5 productos.
RTA:
SELECT pr.id, pr.nombre, COUNT(p.id) AS total_productos
FROM proveedores pr
LEFT JOIN productos p ON p.proveedor_id = pr.id
GROUP BY pr.id, pr.nombre
ORDER BY total_productos DESC;
+----+----------------------+-----------------+
| id | nombre               | total_productos |
+----+----------------------+-----------------+
|  1 | Proveedora Andina    |               1 |
|  2 | Distribuciones Norte |               1 |
+----+----------------------+-----------------+
 
3.5. Listar clientes que no tienen dirección registrada en UbicacionCliente. 
RTA:
SELECT c.id, c.nombre
FROM clientes c
LEFT JOIN ubicacionclientes u ON u.cliente_id = c.id
WHERE u.cliente_id IS NULL;
SELECT c.id, c.nombre
FROM clientes c
LEFT JOIN ubicacionclientes u ON u.cliente_id = c.id
WHERE u.cliente_id IS NULL;

3.6. Calcular el total de ventas por cada cliente. 
RTA:
SELECT 
    c.id AS cliente_id,
    c.nombre AS cliente_nombre,
    SUM(dp.cantidad * p.precio) AS total_ventas
FROM clientes c
JOIN pedidos pe ON pe.cliente_id = c.id
JOIN detallespedido dp ON dp.pedido_id = pe.id
JOIN productos p ON p.id = dp.producto_id
GROUP BY c.id, c.nombre
ORDER BY total_ventas DESC;
+------------+----------------+--------------+
| cliente_id | cliente_nombre | total_ventas |
+------------+----------------+--------------+
|          2 | María          |   3300000.00 |
|          1 | Juan           |   1800000.00 |
+------------+----------------+--------------+

3.7. Mostrar el salario promedio de los empleados. 
RTA:
SELECT AVG(salario) AS salario_promedio
FROM empleados;
+------------------+
| salario_promedio |
+------------------+
|   1650000.000000 |
+------------------+

3.8. Consultar el tipo de productos disponibles en TiposProductos. 
RTA:
SELECT id, tipo_nombre, descripcion
FROM tiposproductos;
+----+--------------------+-------------------------------------+
| id | tipo_nombre        | descripcion                         |
+----+--------------------+-------------------------------------+
|  1 | Electrodomésticos  | Productos eléctricos para el hogar  |
|  2 | Lavadoras          | Lavadoras automáticas               |
+----+--------------------+-------------------------------------+

3.9. Seleccionar los 3 productos más caros. 
RTA:
SELECT id, nombre, precio
FROM productos
ORDER BY precio DESC
LIMIT 3;
+----+-----------------------+------------+
| id | nombre                | precio     |
+----+-----------------------+------------+
|  1 | Lavadora LG 20kg      | 1800000.00 |
|  2 | Lavadora Samsung 18kg | 1650000.00 |
|  3 | Producto sin pedido   |      99.99 |
+----+-----------------------+------------+

3.10. Consultar el cliente con el mayor número de pedidos.
RTA:
SELECT c.id, c.nombre, COUNT(p.id) AS total_pedidos
FROM clientes c
JOIN pedidos p ON p.cliente_id = c.id
GROUP BY c.id, c.nombre
ORDER BY total_pedidos DESC
LIMIT 1;
+----+--------+---------------+
| id | nombre | total_pedidos |
+----+--------+---------------+
|  1 | Juan   |             1 |
+----+--------+---------------+
```
# 4. Consultas Multitabla:
```
4.1. Listar todos los pedidos y el cliente asociado. 
RTA:
SELECT p.id AS id_pedido, p.fecha, c.id AS id_cliente, c.nombre AS nombre_cliente
FROM pedidos p
JOIN clientes c ON p.cliente_id = c.id
ORDER BY p.fecha DESC;
+-----------+------------+------------+----------------+
| id_pedido | fecha      | id_cliente | nombre_cliente |
+-----------+------------+------------+----------------+
|         2 | 2025-06-15 |          2 | María          |
|         1 | 2025-06-01 |          1 | Juan           |
+-----------+------------+------------+----------------+

4.2. Mostrar la ubicación de cada cliente en sus pedidos. 
RTA:
SELECT 
    p.id AS id_pedido,
    c.nombre AS nombre_cliente,
    u.direccion,
    u.ciudad,
    u.estado,
    u.pais,
    p.fecha
FROM pedidos p
JOIN clientes c ON p.cliente_id = c.id
LEFT JOIN ubicacionclientes u ON u.cliente_id = c.id
ORDER BY p.fecha DESC;
+-----------+----------------+-----------+--------+--------+------+------------+
| id_pedido | nombre_cliente | direccion | ciudad | estado | pais | fecha      |
+-----------+----------------+-----------+--------+--------+------+------------+
|         2 | María          | NULL      | NULL   | NULL   | NULL | 2025-06-15 |
|         1 | Juan           | NULL      | NULL   | NULL   | NULL | 2025-06-01 |
+-----------+----------------+-----------+--------+--------+------+------------+

4.3. Listar productos junto con el proveedor y tipo de producto. 
RTA:
SELECT 
    p.id AS id_producto,
    p.nombre AS nombre_producto,
    pr.nombre AS nombre_proveedor,
    tp.tipo_nombre AS tipo_producto,
    p.precio
FROM productos p
JOIN proveedores pr ON p.proveedor_id = pr.id
JOIN tiposproductos tp ON p.tipo_id = tp.id
ORDER BY p.nombre;
+-------------+-----------------------+----------------------+---------------+------------+
| id_producto | nombre_producto       | nombre_proveedor     | tipo_producto | precio     |
+-------------+-----------------------+----------------------+---------------+------------+
|           1 | Lavadora LG 20kg      | Proveedora Andina    | Lavadoras     | 1800000.00 |
|           2 | Lavadora Samsung 18kg | Distribuciones Norte | Lavadoras     | 1650000.00 |
+-------------+-----------------------+----------------------+---------------+------------+

4.4. Consultar todos los empleados que gestionan pedidos de clientes en una ciudad específica. 
RTA:

4.5. Consultar los 5 productos más vendidos. 
RTA:
SELECT pr.nombre, SUM(dp.cantidad) AS total_vendido
FROM DetallesPedido dp
JOIN Productos pr ON dp.producto_id = pr.id
GROUP BY pr.nombre
ORDER BY total_vendido DESC
LIMIT 5;
+-----------------------+---------------+
| nombre                | total_vendido |
+-----------------------+---------------+
| Lavadora Samsung 18kg |             2 |
| Lavadora LG 20kg      |             1 |
+-----------------------+---------------+

4.6. Obtener la cantidad total de pedidos por cliente y ciudad. 
RTA:
SELECT c.nombre, c.apellido, u.ciudad, COUNT(p.id) AS total_pedidos
FROM Clientes c
JOIN Pedidos p ON c.id = p.cliente_id
JOIN UbicacionClientes u ON c.direccion_id = u.id
GROUP BY c.id, u.ciudad;
+--------+----------+-----------+---------------+
| nombre | apellido | ciudad    | total_pedidos |
+--------+----------+-----------+---------------+
| Juan   | Pérez    | Bogotá    |             1 |
| María  | Gómez    | Medellín  |             1 |
+--------+----------+-----------+---------------+

4.7. Listar clientes y proveedores en la misma ciudad. 
RTA:
SELECT c.nombre AS nombre_cliente, p.nombre AS nombre_proveedor, uc.ciudad
FROM Clientes c
JOIN UbicacionClientes uc ON c.direccion_id = uc.id
JOIN Proveedores p ON uc.ciudad = (
    SELECT ciudad FROM Ubicaciones u 
    WHERE u.entidad_tipo = 'proveedor' AND u.entidad_id = p.id LIMIT 1
);
+----------------+----------------------+-----------+
| nombre_cliente | nombre_proveedor     | ciudad    |
+----------------+----------------------+-----------+
| Juan           | Proveedora Andina    | Bogotá    |
| María          | Distribuciones Norte | Medellín  |
+----------------+----------------------+-----------+

4.8. Mostrar el total de ventas agrupado por tipo de producto. 
RTA:
SELECT tp.tipo_nombre, SUM(dp.cantidad * dp.precio) AS total_ventas
FROM DetallesPedido dp
JOIN Productos pr ON dp.producto_id = pr.id
JOIN TiposProductos tp ON pr.tipo_id = tp.id
GROUP BY tp.tipo_nombre;
+-------------+--------------+
| tipo_nombre | total_ventas |
+-------------+--------------+
| Lavadoras   |   5100000.00 |
+-------------+--------------+

4.9. Listar empleados que gestionan pedidos de productos de un proveedor específico. 
RTA:

4.10. Obtener el ingreso total de cada proveedor a partir de los productos vendidos.
RTA:
SELECT pr.proveedor_id, prov.nombre, SUM(dp.cantidad * dp.precio) AS ingreso_total
FROM DetallesPedido dp
JOIN Productos pr ON dp.producto_id = pr.id
JOIN Proveedores prov ON pr.proveedor_id = prov.id
GROUP BY pr.proveedor_id, prov.nombre;
+--------------+----------------------+---------------+
| proveedor_id | nombre               | ingreso_total |
+--------------+----------------------+---------------+
|            1 | Proveedora Andina    |    1800000.00 |
|            2 | Distribuciones Norte |    3300000.00 |
+--------------+----------------------+---------------+
```

##Estructura base de datos 
```
+-------------------+
| Tables_in_vtaszfs |
+-------------------+
| clientes          |
| contactoproveedor |
| datosempleados    |
| detallespedido    |
| empleadoproveedor |
| empleados         |
| historialpedidos  |
| pedidos           |
| productos         |
| proveedores       |
| puestos           |
| telefonos         |
| tiposproductos    |
| tipotelefonos     |
| ubicacionclientes |
| ubicaciones       |
+-------------------+
```

## Realizado por: Leidy Johana Niño Villegas.

## Actividad mejor organizada en el siguiente link

https://drive.google.com/drive/folders/1YEW_n4iiOVhltrBk-Sf2eX_AhpZy0IOH?usp=drive_link
