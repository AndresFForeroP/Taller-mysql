# Taller mysql2 Andrés Forero

**- - Base de datos inicial**.

```mysql
CREATE DATABASE taller; 
USE taller;

-- Tabla Clientes
CREATE TABLE Clientes (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
email VARCHAR(100) UNIQUE
);

-- Tabla UbicacionCliente
CREATE TABLE UbicacionCliente (
id INT PRIMARY KEY AUTO_INCREMENT,
cliente_id INT,
direccion VARCHAR(255),
ciudad VARCHAR(100),
estado VARCHAR(50),
codigo_postal VARCHAR(10),
pais VARCHAR(50),
FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);

-- Tabla Empleados
CREATE TABLE Empleados (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
puesto VARCHAR(100),
salario DECIMAL(10,2),
fecha_contratacion DATE 
);

-- Tabla Proveedores
CREATE TABLE Proveedores (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
contacto VARCHAR(100),
telefono VARCHAR(20),
direccion VARCHAR(255)
);

-- Tabla TiposProductos
CREATE TABLE TiposProductos (
id INT PRIMARY KEY AUTO_INCREMENT,
tipo_nombre VARCHAR(100),
descripcion TEXT
);

-- Tabla Productos
CREATE TABLE Productos (
id INT PRIMARY KEY AUTO_INCREMENT,
nombre VARCHAR(100),
precio DECIMAL(10,2),
proveedor_id INT,
tipo_id INT,
FOREIGN KEY (preveedor_id) REFERENCES Proveedores(id),
FOREIGN KEY (tipo_id) REFERENCES TiposProductos(id)
);

-- Tabla pedidos
CREATE TABLE Pedidos (
id INT PRIMARY KEY AUTO_INCREMENT,
cliente_id INT,
fecha DATE,
total DECIMAL(10,2),
FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);

-- Tabla DetallesPedido
CREATE TABLE DetallesPedido (
id INT PRIMARY KEY AUTO_INCREMENT,
pedido_id INT,
producto_id INT,
cantidad INT,
precio DECIMAL(10,2),
FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
FOREIGN KEY (producto_id) REFERENCES Productos(id)
);
```



**1. Crear tabla HistorialPedidos que almacene cambios en los pedidos.**

```mysql
CREATE TABLE HistorialPedidos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    pedido_id INT,
    fecha_cambio DATETIME,
    cliente_id INT,
    fecha_pedido DATE,
    total DECIMAL(10,2),
    usuario_modifico VARCHAR(100), 
    FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
    FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);
```

**2.  Evaluar la tabla Clientes para eliminar datos redundantes y normalizar hasta 3NF**.

ya esta normalizada

**3. Separar la tabla Empleados en una tabla de DatosEmpleados y otra para Puestos .**

```mysql
-- Tabla Puestos
CREATE TABLE Puestos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100) UNIQUE,
    descripcion TEXT
);

-- Tabla DatosEmpleados
CREATE TABLE DatosEmpleados (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100),
    puesto_id INT,
    salario DECIMAL(10,2),
    fecha_contratacion DATE,
    FOREIGN KEY (puesto_id) REFERENCES Puestos(id)
);
```

**4. Revisar la relación Clientes y UbicacionCliente para evitar duplicación de datos.**

```mysql
-- Tabla Paises
CREATE TABLE Paises (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100) UNIQUE
);

--Tabla estados

CREATE TABLE Estados (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100),
    pais_id INT,
    FOREIGN KEY (pais_id) REFERENCES Paises(id)
);

--Tabla ciudades

CREATE TABLE Ciudades (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100),
    estado_id INT,
    FOREIGN KEY (estado_id) REFERENCES Estados(id)
);

-- Tabla UbicacionCliente modificada
CREATE TABLE UbicacionCliente (
    id INT PRIMARY KEY AUTO_INCREMENT,
    cliente_id INT,
    direccion VARCHAR(255),
    ciudad_id INT,
    codigo_postal VARCHAR(10),
    FOREIGN KEY (cliente_id) REFERENCES Clientes(id),
    FOREIGN KEY (ciudad_id) REFERENCES Ciudades(id)
);
```

**5. Normalizar Proveedores para tener ContactoProveedores en otra tabla.**

```mysql
-- Tabla Proveedores modificada 
CREATE TABLE Proveedores (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100),
    direccion VARCHAR(255)
);

-- Tabla ContactoProveedores
CREATE TABLE ContactoProveedores (
    id INT PRIMARY KEY AUTO_INCREMENT,
    proveedor_id INT,
    nombre_contacto VARCHAR(100),
    telefono VARCHAR(20),
    email VARCHAR(100),
    cargo VARCHAR(100),
    FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id)
);
```

**6. Crear una tabla de Telefonos para almacenar múltiples números por cliente.**

```mysql
-- Tabla TelefonosClientes
CREATE TABLE TelefonosClientes (
    id INT PRIMARY KEY AUTO_INCREMENT,
    cliente_id INT,
    numero_telefono VARCHAR(20),
    prefijo VARCHAR(20),
    FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);
```

**7.  Transformar TiposProductos en una relación categórica jerárquica.**

```mysql
-- Tabla TiposProductos modificado
CREATE TABLE TiposProductos (
id INT PRIMARY KEY AUTO_INCREMENT,
tipo_nombre VARCHAR(100),
tipo_id INT,
descripcion TEXT,
FOREIGN KEY (tipo_id) REFERENCES TiposProductos(id)
);
```

**8.  Normalizar Pedidos y DetallesPedido para evitar inconsistencias de precios.**

```mysql
-- Tabla PreciosProductos 
CREATE TABLE PreciosProductos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    producto_id INT,
    precio DECIMAL(10,2),
    fecha_inicio DATE,
    fecha_fin DATE,
    FOREIGN KEY (producto_id) REFERENCES Productos(id)
);

-- Tabla DetallesPedido modificada
CREATE TABLE DetallesPedido (
    id INT PRIMARY KEY AUTO_INCREMENT,
    pedido_id INT,
    producto_id INT,
    cantidad INT,
    precio_unitario DECIMAL(10,2),
    FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
    FOREIGN KEY (producto_id) REFERENCES Productos(id)
);
```

**9.  Usar una relación de muchos a muchos para Empleados y Proveedores.**

```mysql
-- Tabla EmpleadosProveedores 
CREATE TABLE EmpleadosProveedores (
    id INT PRIMARY KEY AUTO_INCREMENT,
    empleado_id INT,
    proveedor_id INT,
    rol VARCHAR(100),
    fecha_asignacion DATE,
    FOREIGN KEY (empleado_id) REFERENCES DatosEmpleados(id),
    FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id)
);
```

**10. Convertir la tabla UbicacionCliente en una relación genérica de Ubicaciones**

```mysql
-- Tabla Ubicaciones
CREATE TABLE Ubicaciones (
    id INT PRIMARY KEY AUTO_INCREMENT,
    direccion VARCHAR(255),
    ciudad_id INT,
    codigo_postal VARCHAR(10),
    tipo_entidad ENUM('cliente', 'empleado', 'proveedor'),
    entidad_id INT,
    FOREIGN KEY (ciudad_id) REFERENCES Ciudades(id)
);
```

**Base de datos final**

```mysql
CREATE DATABASE taller;
USE taller;

CREATE TABLE Clientes (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100),
    email VARCHAR(100) UNIQUE
)ENGINE = INNODB;

CREATE TABLE TelefonosClientes (
    id INT PRIMARY KEY AUTO_INCREMENT,
    cliente_id INT,
    numero_telefono VARCHAR(20),
    prefijo VARCHAR(20),
    FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
)ENGINE = INNODB;

CREATE TABLE Paises (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100) UNIQUE
)ENGINE = INNODB;

CREATE TABLE Estados (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100),
    pais_id INT,
    FOREIGN KEY (pais_id) REFERENCES Paises(id)
)ENGINE = INNODB;

CREATE TABLE Ciudades (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100),
    estado_id INT,
    FOREIGN KEY (estado_id) REFERENCES Estados(id)
)ENGINE = INNODB;

CREATE TABLE Ubicaciones (
    id INT PRIMARY KEY AUTO_INCREMENT,
    direccion VARCHAR(255),
    ciudad_id INT,
    codigo_postal VARCHAR(10),
    tipo_entidad ENUM('cliente', 'empleado', 'proveedor'),
    entidad_id INT,
    FOREIGN KEY (ciudad_id) REFERENCES Ciudades(id)
)ENGINE = INNODB;

CREATE TABLE Puestos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100) UNIQUE,
    descripcion TEXT
)ENGINE = INNODB;

CREATE TABLE DatosEmpleados (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100),
    puesto_id INT,
    salario DECIMAL(10,2),
    fecha_contratacion DATE,
    FOREIGN KEY (puesto_id) REFERENCES Puestos(id)
)ENGINE = INNODB;

CREATE TABLE Proveedores (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100),
    direccion VARCHAR(255)
)ENGINE = INNODB;

CREATE TABLE ContactoProveedores (
    id INT PRIMARY KEY AUTO_INCREMENT,
    proveedor_id INT,
    nombre_contacto VARCHAR(100),
    telefono VARCHAR(20),
    email VARCHAR(100),
    cargo VARCHAR(100),
    FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id)
)ENGINE = INNODB;

CREATE TABLE EmpleadosProveedores (
    id INT PRIMARY KEY AUTO_INCREMENT,
    empleado_id INT,
    proveedor_id INT,
    rol VARCHAR(100),
    fecha_asignacion DATE,
    FOREIGN KEY (empleado_id) REFERENCES DatosEmpleados(id),
    FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id)
)ENGINE = INNODB;

CREATE TABLE TiposProductos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    tipo_nombre VARCHAR(100),
    tipo_id INT,
    descripcion TEXT,
    FOREIGN KEY (tipo_id) REFERENCES TiposProductos(id)
)ENGINE = INNODB;

CREATE TABLE Productos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100),
    proveedor_id INT,
    tipo_id INT,
    FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id),
    FOREIGN KEY (tipo_id) REFERENCES TiposProductos(id)
)ENGINE = INNODB;

CREATE TABLE PreciosProductos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    producto_id INT,
    precio DECIMAL(10,2),
    fecha_inicio DATE,
    fecha_fin DATE,
    FOREIGN KEY (producto_id) REFERENCES Productos(id)
)ENGINE = INNODB;

CREATE TABLE Pedidos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    cliente_id INT,
    fecha DATE,
    total DECIMAL(10,2),
    FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
)ENGINE = INNODB;

CREATE TABLE DetallesPedido (
    id INT PRIMARY KEY AUTO_INCREMENT,
    pedido_id INT,
    producto_id INT,
    cantidad INT,
    precio_unitario DECIMAL(10,2),
    FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
    FOREIGN KEY (producto_id) REFERENCES Productos(id)
)ENGINE = INNODB;

CREATE TABLE HistorialPedidos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    pedido_id INT,
    fecha_cambio DATETIME,
    cliente_id INT,
    fecha_pedido DATE,
    total DECIMAL(10,2),
    usuario_modifico VARCHAR(100), 
    FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
    FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
)ENGINE = INNODB;
```

**Inserts base de datos**

```mysql
INSERT INTO Paises (nombre) VALUES ('Colombia'), ('México'), ('Argentina'), ('Perú'), ('Chile');

INSERT INTO Estados (nombre, pais_id) VALUES 
('Cundinamarca', 1), 
('Antioquia', 1), 
('CDMX', 2), 
('Jalisco', 2), 
('Buenos Aires', 3), 
('Córdoba', 3), 
('Lima', 4), 
('Cusco', 4), 
('Santiago', 5), 
('Valparaíso', 5);

INSERT INTO Ciudades (nombre, estado_id) VALUES 
('Bogotá', 1), 
('Soacha', 1), 
('Medellín', 2), 
('Ciudad de México', 3), 
('Guadalajara', 4), 
('Buenos Aires', 5), 
('Rosario', 6), 
('Lima', 7), 
('Cusco', 8), 
('Santiago', 9), 
('Viña del Mar', 10), 
('Neiva', 1), 
('Manizales', 2), 
('Arequipa', 7), 
('Temuco', 9);

INSERT INTO Clientes (nombre, email) VALUES 
('Juan Pérez', 'juanp@example.com'), 
('Ana Torres', 'ana.t@example.com'), 
('Luis Gómez', 'luisg@example.com'),
('María Rodríguez', 'mariar@example.com'),
('Carlos Díaz', 'cdiaz@example.com'),
('Paola Rivas', 'paolar@example.com'),
('Esteban López', 'estebanl@example.com'),
('Juliana Ortiz', 'juliana.o@example.com'),
('Ricardo Muñoz', 'rmunoz@example.com'),
('Laura Mejía', 'lmejia@example.com');

INSERT INTO TelefonosClientes (cliente_id, numero_telefono, prefijo) VALUES 
(1, '3001111111', '+57'), 
(2, '3002222222', '+57'), 
(3, '3003333333', '+57'), 
(4, '3004444444', '+52'), 
(5, '3005555555', '+52'), 
(6, '3006666666', '+54'), 
(7, '3007777777', '+54'), 
(8, '3008888888', '+51'), 
(9, '3009999999', '+56'), 
(10, '3010000000', '+56');

INSERT INTO Ubicaciones (direccion, ciudad_id, codigo_postal, tipo_entidad, entidad_id) VALUES 
('Calle 1 #10-20', 1, '110111', 'cliente', 1),
('Cra 9 #20-30', 2, '110112', 'cliente', 2),
('Av 5 Sur #45', 3, '050001', 'cliente', 3),
('Insurgentes #123', 4, '010101', 'cliente', 4),
('Calle Reforma #456', 5, '010102', 'cliente', 5),
('San Martín 234', 6, '100100', 'cliente', 6),
('Bv. Illia 789', 7, '500200', 'cliente', 7),
('Av Arequipa 111', 8, '150100', 'cliente', 8),
('Calle Sacsayhuamán 321', 9, '080100', 'cliente', 9),
('Av. Providencia 89', 10, '750100', 'cliente', 10);

INSERT INTO Puestos (nombre, descripcion) VALUES 
('Vendedor', 'Encargado de atención al cliente y ventas'),
('Almacén', 'Manejo de productos y stock'),
('Gerente', 'Dirección general');

INSERT INTO DatosEmpleados (nombre, puesto_id, salario, fecha_contratacion) VALUES 
('Carlos Rojas', 1, 2500000, '2023-01-10'),
('Lucía Martínez', 3, 5000000, '2022-06-15'),
('Daniel Gómez', 2, 2000000, '2024-01-20'),
('Sofía Torres', 1, 2600000, '2024-03-05'),
('Andrés Niño', 2, 2100000, '2023-09-11'),
('Valentina Herrera', 3, 5200000, '2021-12-01');

INSERT INTO Proveedores (nombre, direccion) VALUES 
('TechParts S.A.', 'Cra 45 #89-10'), 
('ElectroWorld Ltda.', 'Av 7ma #12-50'), 
('CompoStore Inc.', 'Diagonal 10 #99-01'), 
('Suministros ABC', 'Transv 8 #34-21');

INSERT INTO ContactoProveedores (proveedor_id, nombre_contacto, telefono, email, cargo) VALUES 
(1, 'Mario López', '3204567890', 'mario@techparts.com', 'Ventas'),
(2, 'Sandra Díaz', '3019876543', 'sandra@electroworld.com', 'Soporte'),
(3, 'Julio Paredes', '3131231231', 'julio@compostore.com', 'Gerente'),
(4, 'Beatriz Uribe', '3163214567', 'beatriz@abc.com', 'Logística');

INSERT INTO EmpleadosProveedores (empleado_id, proveedor_id, rol, fecha_asignacion) VALUES 
(1, 1, 'Asesor comercial', '2023-02-10'),
(2, 2, 'Coordinadora', '2022-07-01'),
(3, 3, 'Encargado almacén', '2024-02-15'),
(4, 1, 'Atención técnica', '2024-04-01'),
(5, 4, 'Distribuidor', '2023-11-10');

INSERT INTO TiposProductos (tipo_nombre, tipo_id, descripcion) VALUES 
('Electrónica', NULL, 'Dispositivos electrónicos'),
('Componentes', 1, 'Piezas electrónicas'),
('Cables', 1, 'Cables y conexiones'),
('Herramientas', NULL, 'Herramientas de uso técnico'),
('Medición', 4, 'Instrumentos de medición'),
('Almacenamiento', NULL, 'Cajas y racks'),
('Sensores', 1, 'Sensores diversos'),
('Actuadores', 1, 'Motores y relés'),
('Protección', 1, 'Fusibles y breakers'),
('Energía', NULL, 'Baterías y fuentes');

INSERT INTO Productos (nombre, proveedor_id, tipo_id) VALUES 
('Resistencia 10kΩ', 1, 2),
('Capacitor 100µF', 2, 2),
('Cable USB-C', 3, 3),
('Multímetro Digital', 2, 5),
('Caja Plástica 10x10', 4, 6),
('Sensor de temperatura', 1, 7),
('Relé 5V', 1, 8),
('Fusible 2A', 4, 9),
('Batería 9V', 2, 10),
('Fuente 12V 2A', 3, 10),
('Cables Dupont 40pzas', 3, 3),
('Osciloscopio Mini', 2, 5),
('Cautín 60W', 4, 4),
('Pasta para soldar', 4, 4),
('Arduino Uno R3', 1, 1),
('LED RGB', 2, 2),
('Zumbador piezoeléctrico', 3, 8),
('Pinza amperimétrica', 2, 5),
('Protoboard 830pts', 4, 6),
('Sensor de gas MQ-2', 1, 7);

INSERT INTO PreciosProductos (producto_id, precio, fecha_inicio, fecha_fin) VALUES 
(1, 100.00, '2024-01-01', '2025-01-01'),
(2, 200.00, '2024-01-01', '2025-01-01'),
(3, 500.00, '2024-01-01', '2025-01-01'),
(4, 1500.00, '2024-01-01', '2025-01-01'),
(5, 700.00, '2024-01-01', '2025-01-01'),
(6, 1300.00, '2024-01-01', '2025-01-01'),
(7, 800.00, '2024-01-01', '2025-01-01'),
(8, 250.00, '2024-01-01', '2025-01-01'),
(9, 600.00, '2024-01-01', '2025-01-01'),
(10, 1200.00, '2024-01-01', '2025-01-01'),
(11, 400.00, '2024-01-01', '2025-01-01'),
(12, 3000.00, '2024-01-01', '2025-01-01'),
(13, 1800.00, '2024-01-01', '2025-01-01'),
(14, 300.00, '2024-01-01', '2025-01-01'),
(15, 8500.00, '2024-01-01', '2025-01-01'),
(16, 150.00, '2024-01-01', '2025-01-01'),
(17, 500.00, '2024-01-01', '2025-01-01'),
(18, 2500.00, '2024-01-01', '2025-01-01'),
(19, 750.00, '2024-01-01', '2025-01-01'),
(20, 900.00, '2024-01-01', '2025-01-01');

INSERT INTO Pedidos (cliente_id, fecha, total) VALUES 
(1, '2025-06-01', 3500.00),
(2, '2025-06-03', 6000.00),
(3, '2025-06-04', 11000.00),
(4, '2025-06-05', 1200.00),
(5, '2025-06-06', 2400.00),
(6, '2025-06-07', 1750.00),
(7, '2025-06-08', 3200.00),
(8, '2025-06-09', 2100.00),
(9, '2025-06-10', 4050.00),
(10, '2025-06-11', 3150.00),
(1, '2025-06-07', 1750.00),
(2, '2025-06-08', 3200.00),
(2, '2025-06-09', 2100.00),
(3, '2025-06-10', 4050.00);

INSERT INTO DetallesPedido (pedido_id, producto_id, cantidad, precio_unitario) VALUES 
(1, 1, 5, 100.00), 
(1, 2, 2, 200.00),
(2, 4, 1, 1500.00), 
(2, 6, 3, 1300.00),
(3, 15, 1, 8500.00), 
(3, 11, 5, 400.00),
(4, 8, 3, 250.00),
(5, 10, 2, 1200.00),
(6, 13, 1, 1800.00),
(7, 7, 2, 800.00),
(8, 19, 2, 750.00),
(9, 18, 1, 2500.00), 
(9, 16, 3, 150.00),
(10, 20, 2, 900.00),
(10, 17, 1, 500.00),
(1, 1, 5, 100.00), 
(1, 1, 2, 200.00),
(2, 1, 1, 1500.00), 
(2, 1, 3, 1300.00),
(3, 1, 1, 8500.00), 
(3, 1, 5, 400.00),
(4, 8, 3, 250.00);


INSERT INTO HistorialPedidos (pedido_id, fecha_cambio, cliente_id, fecha_pedido, total, usuario_modifico) VALUES 
(1, '2025-06-01 10:00:00', 1, '2025-06-01', 3500.00, 'admin'),
(2, '2025-06-03 12:30:00', 2, '2025-06-03', 6000.00, 'admin'),
(3, '2025-06-04 08:45:00', 3, '2025-06-04', 11000.00, 'admin');
```

**SUBCONSULTAS**

1. Consultar el producto mas caro en cada categoría

   ```mysql
   SELECT p.nombre as producto, pp.precio, tp.tipo_nombre AS categoria
   FROM Productos p
   JOIN PreciosProductos pp ON p.id = pp.producto_id
   JOIN TiposProductos as tp ON p.tipo_id = tp.id
   WHERE pp.precio = (
       SELECT MAX(pp2.precio)
       FROM Productos p2
       JOIN PreciosProductos pp2 ON p2.id = pp2.producto_id
       WHERE p2.tipo_id = p.tipo_id
   );
   ```

   resultado:

   ```
   +-----------------------+---------+----------------+
   | producto              | precio  | categoria      |
   +-----------------------+---------+----------------+
   | Arduino Uno R3        | 8500.00 | Electrónica    |
   | Capacitor 100µF       |  200.00 | Componentes    |
   | Cable USB-C           |  500.00 | Cables         |
   | Cautín 60W            | 1800.00 | Herramientas   |
   | Osciloscopio Mini     | 3000.00 | Medición       |
   | Protoboard 830pts     |  750.00 | Almacenamiento |
   | Sensor de temperatura | 1300.00 | Sensores       |
   | Relé 5V               |  800.00 | Actuadores     |
   | Fusible 2A            |  250.00 | Protección     |
   | Fuente 12V 2A         | 1200.00 | Energía        |
   +-----------------------+---------+----------------+
   10 rows in set (0,00 sec)
   ```

2. Encuentra el cliente con mayor numero de pedidos

   ```
   SELECT c.nombre, COUNT(p.id) AS Cantidad_pedidos
   FROM Clientes as c
   JOIN Pedidos as p ON c.id = p.cliente_id
   GROUP BY c.nombre
   ORDER BY Cantidad_pedidos DESC
   LIMIT 1;
   ```

   resultado:

   ```
   +-------------+------------------+
   | nombre      | Cantidad_pedidos |
   +-------------+------------------+
   | Juan Pérez  |                1 |
   +-------------+------------------+
   1 row in set (0,00 sec)
   ```

3. Listar empleados que ganan más que el salario promedio.

   ```mysql
   SELECT dp.nombre AS empleado, dp.salario
   FROM DatosEmpleados as dp
   WHERE salario > (
   	SELECT AVG(dp.salario)
   	FROM DatosEmpleados as dp)
   ```

   resultado:

   ```
   +-------------------+------------+
   | empleado          | salario    |
   +-------------------+------------+
   | Lucía Martínez    | 5000000.00 |
   | Valentina Herrera | 5200000.00 |
   +-------------------+------------+
   2 rows in set (0,00 sec)
   ```

4.Consultar productos que han sido pedidos más de 5 veces.

```mysql
SELECT p.id, p.nombre
FROM Productos p
WHERE p.id IN (
    SELECT producto_id
    FROM DetallesPedido
    GROUP BY producto_id
    HAVING SUM(cantidad) > 5
);
```
resultado:

```
+----+-------------------+
| id | nombre            |
+----+-------------------+
|  1 | Resistencia 10kΩ  |
|  8 | Fusible 2A        |
+----+-------------------+
```

5.Listar pedidos cuyo total es mayor al promedio de todos los pedidos

```mysql
SELECT *
FROM Pedidos
WHERE total > (
    SELECT AVG(total)
    FROM Pedidos
);
```

resultado :

```
+----+------------+------------+----------+
| id | cliente_id | fecha      | total    |
+----+------------+------------+----------+
|  2 |          2 | 2025-06-03 |  6000.00 |
|  3 |          3 | 2025-06-04 | 11000.00 |
|  9 |          9 | 2025-06-10 |  4050.00 |
+----+------------+------------+----------+
```

6.Seleccionar los 3 proveedores con más productos.

```mysql
SELECT pr.id, pr.nombre, sub.total_productos
FROM Proveedores pr
JOIN (
    SELECT proveedor_id, COUNT(*) AS total_productos
    FROM Productos
    GROUP BY proveedor_id
    ORDER BY total_productos DESC
    LIMIT 3
) AS sub ON pr.id = sub.proveedor_id;
```

resultado:

```
+----+--------------------+-----------------+
| id | nombre             | total_productos |
+----+--------------------+-----------------+
|  1 | TechParts S.A.     |               5 |
|  2 | ElectroWorld Ltda. |               6 |
|  4 | Suministros ABC    |               5 |
+----+--------------------+-----------------+
```

7.Consultar productos con precio superior al promedio en su tipo

```mysql
SELECT p.id, p.nombre, pp.precio, tp.tipo_nombre
FROM Productos p
JOIN PreciosProductos pp ON p.id = pp.producto_id
JOIN TiposProductos tp ON p.tipo_id = tp.id
WHERE pp.precio > (
    SELECT AVG(pp2.precio)
    FROM Productos p2
    JOIN PreciosProductos pp2 ON p2.id = pp2.producto_id
    WHERE p2.tipo_id = p.tipo_id
);
```

resultado: 

```
+----+-----------------------+---------+----------------+
| id | nombre                | precio  | tipo_nombre    |
+----+-----------------------+---------+----------------+
|  2 | Capacitor 100µF       |  200.00 | Componentes    |
|  3 | Cable USB-C           |  500.00 | Cables         |
| 13 | Cautín 60W            | 1800.00 | Herramientas   |
| 12 | Osciloscopio Mini     | 3000.00 | Medición       |
| 18 | Pinza amperimétrica   | 2500.00 | Medición       |
| 19 | Protoboard 830pts     |  750.00 | Almacenamiento |
|  6 | Sensor de temperatura | 1300.00 | Sensores       |
|  7 | Relé 5V               |  800.00 | Actuadores     |
| 10 | Fuente 12V 2A         | 1200.00 | Energía        |
+----+-----------------------+---------+----------------+
```

8.Mostrar clientes que han realizado más pedidos que la media.

```mysql
SELECT c.id, c.nombre, COUNT(p.id) AS total_pedidos
FROM Clientes c
JOIN Pedidos p ON c.id = p.cliente_id
GROUP BY c.id, c.nombre
HAVING total_pedidos > (
    SELECT AVG(cantidad_pedidos)
    FROM (
        SELECT COUNT(*) AS cantidad_pedidos
        FROM Pedidos
        GROUP BY cliente_id
    ) AS sub
);
```

resultado:

```
+----+-------------+---------------+
| id | nombre      | total_pedidos |
+----+-------------+---------------+
|  1 | Juan Pérez  |             2 |
|  2 | Ana Torres  |             3 |
|  3 | Luis Gómez  |             2 |
+----+-------------+---------------+
```

9.Encontrar productos cuyo precio es mayor que el promedio de todos los productos.

```mysql
SELECT p.id, p.nombre, pp.precio
FROM Productos p
JOIN PreciosProductos pp ON p.id = pp.producto_id
WHERE pp.precio > (
    SELECT AVG(precio)
    FROM PreciosProductos
);
```

resultado: 

```
+----+-----------------------+---------+
| id | nombre                | precio  |
+----+-----------------------+---------+
|  4 | Multímetro Digital    | 1500.00 |
|  6 | Sensor de temperatura | 1300.00 |
| 12 | Osciloscopio Mini     | 3000.00 |
| 13 | Cautín 60W            | 1800.00 |
| 15 | Arduino Uno R3        | 8500.00 |
| 18 | Pinza amperimétrica   | 2500.00 |
+----+-----------------------+---------+
```

10.Mostrar empleados cuyo salario es menor al promedio del departamento

```
+----+------------------+------------+----------+
| id | nombre           | salario    | puesto   |
+----+------------------+------------+----------+
|  1 | Carlos Rojas     | 2500000.00 | Vendedor |
|  2 | Lucía Martínez   | 5000000.00 | Gerente  |
|  3 | Daniel Gómez     | 2000000.00 | Almacén  |
+----+------------------+------------+----------+
```

**PROCEDIMIENTOS ALMACENADOS**

1.Crear un procedimiento para actualizar el precio de todos los productos de un proveedor.

```mysql
DELIMITER $$

CREATE PROCEDURE ActualizarPrecioBasico(
    IN prov_id INT,
    IN nuevo_precio DECIMAL(5,2)
)
BEGIN
    UPDATE Productos
    SET precio = nuevo_precio
    WHERE proveedor_id = prov_id;
END$$

DELIMITER ;
```

2. Un procedimiento que devuelva la dirección de un cliente por ID.

```mysql
DELIMITER $$

CREATE PROCEDURE ObtenerDireccionCliente(
    IN cliente_id INT
)
BEGIN
    SELECT u.direccion, u.codigo_postal, c.nombre AS ciudad
    FROM Ubicaciones u
    JOIN Ciudades c ON u.ciudad_id = c.id
    WHERE u.tipo_entidad = 'cliente' AND u.entidad_id = cliente_id;
END$$

DELIMITER ;
```

resultado:

```
mysql> CALL ObtenerDireccionCliente(1);
+----------------+---------------+---------+
| direccion      | codigo_postal | ciudad  |
+----------------+---------------+---------+
| Calle 1 #10-20 | 110111        | Bogotá  |
+----------------+---------------+---------+
1 row in set (0,00 sec)

Query OK, 0 rows affected (0,00 sec)
```

3.Crear un procedimiento que registre un pedido nuevo y sus detalles.

```mysql
DELIMITER $$

CREATE PROCEDURE RegistrarPedidoSimple(
    IN cliente_id INT,
    IN producto_id INT,
    IN cantidad INT,
    IN precio_unitario DECIMAL(10,2)
)
BEGIN
    DECLARE pedido_id INT;
    DECLARE total DECIMAL(10,2);
    SET total = cantidad * precio_unitario;
    INSERT INTO Pedidos (cliente_id, fecha, total)
    VALUES (cliente_id, CURDATE(), total);
    SET pedido_id = LAST_INSERT_ID();
    INSERT INTO DetallesPedido (pedido_id, producto_id, cantidad, precio_unitario)
    VALUES (pedido_id, producto_id, cantidad, precio_unitario);
END$$

DELIMITER ;
```

resultado:

```
mysql> CALL RegistrarPedidoSimple(1, 5, 3, 15000.00);
Query OK, 1 row affected (0,00 sec)
SELECT pedido_id, producto_id, cantidad, precio_unitario FROM DetallesPedido WHERE producto_id = 5;
+-----------+-------------+----------+-----------------+
| pedido_id | producto_id | cantidad | precio_unitario |
+-----------+-------------+----------+-----------------+
|        15 |           5 |        3 |        15000.00 |
+-----------+-------------+----------+-----------------+
1 row in set (0,00 sec)
```

4.Un procedimiento para calcular el total de ventas de un cliente

```mysql
DELIMITER $$

CREATE PROCEDURE CalcularTotalVentasCliente(
    IN cliente_id INT
)
BEGIN
    SELECT c.nombre AS cliente, SUM(p.total) AS total_ventas
    FROM Clientes c
    JOIN Pedidos p ON c.id = p.cliente_id
    WHERE c.id = cliente_id
    GROUP BY c.id;
END$$

DELIMITER ;
```

resultado:

```
mysql> CALL CalcularTotalVentasCliente(1);
+-------------+--------------+
| cliente     | total_ventas |
+-------------+--------------+
| Juan Pérez  |     50250.00 |
+-------------+--------------+
1 row in set (0,00 sec)

Query OK, 0 rows affected (0,00 sec)

```

5.Crear un procedimiento para obtener los empleados por puesto

```mysql
DELIMITER $$

CREATE PROCEDURE ObtenerEmpleadosPorPuesto(
    IN puesto_id INT
)
BEGIN
    SELECT e.id, e.nombre, e.salario, e.fecha_contratacion, p.nombre AS puesto
    FROM DatosEmpleados e
    JOIN Puestos p ON e.puesto_id = p.id
    WHERE e.puesto_id = puesto_id;
END$$

DELIMITER ;
```

resultado:

```
mysql> CALL ObtenerEmpleadosPorPuesto(2);
+----+---------------+------------+--------------------+----------+
| id | nombre        | salario    | fecha_contratacion | puesto   |
+----+---------------+------------+--------------------+----------+
|  3 | Daniel Gómez  | 2000000.00 | 2024-01-20         | Almacén  |
|  5 | Andrés Niño   | 2100000.00 | 2023-09-11         | Almacén  |
+----+---------------+------------+--------------------+----------+
2 rows in set (0,00 sec)

Query OK, 0 rows affected (0,00 sec)
```

6.Un procedimiento que actualice el salario de empleados por puesto.

```mysql
DELIMITER $$

CREATE PROCEDURE ActualizarSalarioPorPuesto(
    IN puesto_id INT,
    IN nuevo_salario DECIMAL(5,2)
)
BEGIN
    UPDATE DatosEmpleados
    SET salario = nuevo_salario
    WHERE puesto_id = puesto_id;
END$$

DELIMITER ;
```

resultado:

```
mysql> CALL ActualizarSalarioPorPuesto(1, 10.00);
Query OK, 6 rows affected (0,01 sec)

mysql> SELECT e.id, e.nombre, p.nombre AS puesto, e.salario
    -> FROM DatosEmpleados e
    -> JOIN Puestos p ON e.puesto_id = p.id
    -> WHERE e.puesto_id = 1;
+----+---------------+----------+---------+
| id | nombre        | puesto   | salario |
+----+---------------+----------+---------+
|  1 | Carlos Rojas  | Vendedor |   10.00 |
|  4 | Sofía Torres  | Vendedor |   10.00 |
+----+---------------+----------+---------+
2 rows in set (0,00 sec)

```

7.Crear un procedimiento que liste los pedidos entre dos fechas

```mysql
DELIMITER $$

CREATE PROCEDURE ListarPedidosEntreFechas(
    IN fecha_inicio DATE,
    IN fecha_fin DATE
)
BEGIN
    SELECT p.id AS pedido_id, c.nombre AS cliente, p.fecha, p.total
    FROM Pedidos p
    JOIN Clientes c ON p.cliente_id = c.id
    WHERE p.fecha BETWEEN fecha_inicio AND fecha_fin
    ORDER BY p.fecha;
END$$

DELIMITER ;
```

resultado:

```
mysql> CALL ListarPedidosEntreFechas('2024-01-01', '2025-12-31');
+-----------+-------------------+------------+----------+
| pedido_id | cliente           | fecha      | total    |
+-----------+-------------------+------------+----------+
|         1 | Juan Pérez        | 2025-06-01 |  3500.00 |
|         2 | Ana Torres        | 2025-06-03 |  6000.00 |
|         3 | Luis Gómez        | 2025-06-04 | 11000.00 |
|         4 | María Rodríguez   | 2025-06-05 |  1200.00 |
|         5 | Carlos Díaz       | 2025-06-06 |  2400.00 |
|         6 | Paola Rivas       | 2025-06-07 |  1750.00 |
|        11 | Juan Pérez        | 2025-06-07 |  1750.00 |
|         7 | Esteban López     | 2025-06-08 |  3200.00 |
|        12 | Ana Torres        | 2025-06-08 |  3200.00 |
|         8 | Juliana Ortiz     | 2025-06-09 |  2100.00 |
|        13 | Ana Torres        | 2025-06-09 |  2100.00 |
|         9 | Ricardo Muñoz     | 2025-06-10 |  4050.00 |
|        14 | Luis Gómez        | 2025-06-10 |  4050.00 |
|        10 | Laura Mejía       | 2025-06-11 |  3150.00 |
|        15 | Juan Pérez        | 2025-07-04 | 45000.00 |
+-----------+-------------------+------------+----------+
15 rows in set (0,00 sec)

Query OK, 0 rows affected (0,00 sec)
```

8.Un procedimiento para aplicar un descuento a productos de una categoría

```mysql
DELIMITER $$

CREATE PROCEDURE AplicarDescuentoPorCategoria(
    IN categoria_id INT,
    IN descuento DECIMAL(5,2)
)
BEGIN
    UPDATE PreciosProductos pp
    JOIN Productos p ON pp.producto_id = p.id
    SET pp.precio = pp.precio * (1 - descuento / 100)
    WHERE p.tipo_id = categoria_id;
END$$

DELIMITER ;
```

resultados:

```
mysql> CALL AplicarDescuentoPorCategoria(2, 15.00);
Query OK, 3 rows affected (0,01 sec)

mysql> SELECT 
    ->     p.nombre AS producto,
    ->     tp.tipo_nombre AS categoria,
    ->     pp.precio
    -> FROM Productos p
    -> JOIN TiposProductos tp ON p.tipo_id = tp.id
    -> JOIN PreciosProductos pp ON p.id = pp.producto_id
    -> WHERE p.tipo_id = 2;
+-------------------+-------------+--------+
| producto          | categoria   | precio |
+-------------------+-------------+--------+
| Resistencia 10kΩ  | Componentes |  85.00 |
| Capacitor 100µF   | Componentes | 170.00 |
| LED RGB           | Componentes | 127.50 |
+-------------------+-------------+--------+
3 rows in set (0,00 sec)
```

9.Crear un procedimiento que liste todos los proveedores de un tipo de producto.

```mysql
DELIMITER $$

CREATE PROCEDURE ListarProveedoresPorTipoProducto(
    IN tipo_producto_id INT
)
BEGIN
    SELECT DISTINCT pr.id AS proveedor_id, pr.nombre AS proveedor
    FROM Proveedores pr
    JOIN Productos p ON pr.id = p.proveedor_id
    WHERE p.tipo_id = tipo_producto_id;
END$$

DELIMITER ;
```

resultado:

```
CALL ListarProveedoresPorTipoProducto(1);
+--------------+----------------+
| proveedor_id | proveedor      |
+--------------+----------------+
|            1 | TechParts S.A. |
+--------------+----------------+
1 row in set (0,00 sec)

Query OK, 0 rows affected (0,00 sec)

```

10.Un procedimiento que devuelva el pedido de mayor valor

```mysql
DELIMITER $$

CREATE PROCEDURE PedidoDeMayorValor()
BEGIN
    SELECT p.id AS pedido_id, c.nombre AS cliente, p.fecha, p.total
    FROM Pedidos p
    JOIN Clientes c ON p.cliente_id = c.id
    WHERE p.total = (
        SELECT MAX(total)
        FROM Pedidos
    );
END$$

DELIMITER ;
```

resultado:

```
mysql> CALL PedidoDeMayorValor();
+-----------+-------------+------------+----------+
| pedido_id | cliente     | fecha      | total    |
+-----------+-------------+------------+----------+
|        15 | Juan Pérez  | 2025-07-04 | 45000.00 |
+-----------+-------------+------------+----------+
1 row in set (0,01 sec)

Query OK, 0 rows affected (0,01 sec)
```
