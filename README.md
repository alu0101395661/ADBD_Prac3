# ADBD_Prac3

<img width="1547" height="998" alt="Diagrama drawio" src="https://github.com/user-attachments/assets/f032fa47-27a4-4d6e-b8c8-541976a4b758" />




<img width="1068" height="1009" alt="Diagrama drawio (3)" src="https://github.com/user-attachments/assets/bd26b00f-8c81-4da4-bf3f-61e530077e09" />




## Script SQL

### Debe crear una base de datos viveros.

CREATE DATABASE viveros;

### Tablas del modelo relacional

CREATE TABLE Vivero ( ID_vivero INT AUTO_INCREMENT, Nombre VARCHAR(100) NOT NULL, Latitud DECIMAL(9,6) CHECK (Latitud BETWEEN -90 AND 90)
,  Longitud DECIMAL(9,6) CHECK (Longitud BETWEEN -180 AND 180), CONSTRAINT PK_Vivero PRIMARY KEY (ID_vivero) );

CREATE TABLE Zona (ID_vivero INT, ID_Zona INT, Nombre VARCHAR(50) NOT NULL, Latitud DECIMAL(9,6) CHECK (Latitud BETWEEN -90 AND 90)
,  Longitud DECIMAL(9,6) CHECK (Longitud BETWEEN -180 AND 180), CONSTRAINT PK_Zona PRIMARY KEY (ID_vivero, ID_Zona), CONSTRAINT FK_Zona_Vivero FOREIGN KEY (ID_vivero) REFERENCES Vivero(ID_vivero)  ON DELETE CASCADE ON UPDATE CASCADE );

CREATE TABLE Producto (ID_producto INT AUTO_INCREMENT, Nombre VARCHAR(100) NOR NULL, Tipo VARCHAR(50) CHECK (Tipo IN ('Planta de Exterior', 'Planta de Interior', 'Herramienta', 'Abono', 'Decoración')), Precio DECIMAL(10,2) NOT NULL CHECK (Precio >= 0), CONSTRAINT PK_Producto PRIMARY KEY (ID_producto) );

CREATE TABLE Stock (ID_vivero INT, ID_Zona INT, ID_producto INT, Cantidad INT NOT NULL CHECK (Cantidad >= 0), CONSTRAINT PK_Stock PRIMARY KEY (ID_vivero, ID_Zona, ID_producto), CONSTRAINT FK_Stock_Zona FOREIGN KEY (ID_vivero, ID_Zona) REFERENCES Zona(ID_vivero, ID_Zona) ON DELETE CASCADE ON UPDATE CASCADE, CONSTRAINT FK_Stock_Producto FOREIGN KEY (ID_producto) REFERENCES Producto(ID_producto)  ON DELETE RESTRICT ON UPDATE CASCADE );

CREATE TABLE Empleado (
    ID_empleado INT AUTO_INCREMENT, DNI_NIE VARCHAR(10) NOT NULL UNIQUE, Nombre VARCHAR(50) NOT NULL, Apellidos VARCHAR(100) NOT NULL, CONSTRAINT PK_Empleado PRIMARY KEY (ID_empleado)
);


CREATE TABLE Historico_puesto (
    ID_empleado INT, ID_vivero INT, ID_Zona INT, Fecha_ini DATE NOT NULL, Fecha_fin DATE, Puesto_Tarea VARCHAR(50) NOT NULL, Indicador_Productividad DECIMAL(5,2) CHECK (Indicador_Productividad BETWEEN 0 AND 100), CONSTRAINT PK_Historico PRIMARY KEY (ID_empleado, ID_vivero, ID_Zona, Fecha_ini), CONSTRAINT CK_Fechas CHECK (Fecha_fin IS NULL OR Fecha_fin >= Fecha_ini), CONSTRAINT FK_Historico_Empleado FOREIGN KEY (ID_empleado) REFERENCES Empleado(ID_empleado) ON DELETE CASCADE ON UPDATE CASCADE, CONSTRAINT FK_Historico_Zona FOREIGN KEY (ID_vivero, ID_Zona) REFERENCES Zona(ID_vivero, ID_Zona) ON DELETE CASCADE ON UPDATE CASCADE);

CREATE TABLE Cliente_Plus ( ID_Cliente INT AUTO_INCREMENT, Nombre VARCHAR(50) NOT NULL, Apellidos VARCHAR(100) NOT NULL, Email VARCHAR(100) NOT NULL UNIQUE CHECK (Email LIKE '%@%.%'), Fecha_Ingreso_Plus DATE NOT NULL DEFAULT CURRENT_DATE, CONSTRAINT PK_Cliente_Plus PRIMARY KEY (ID_Cliente) );


CREATE TABLE Bonificacion (
    ID_bonificacion INT AUTO_INCREMENT, ID_Cliente INT NOT NULL, Mes_año VARCHAR(7) NOT NULL CHECK (Mes_año LIKE '____-__'), 
Volumen_compras DECIMAL(10,2) DEFAULT 0.00 CHECK (Volumen_compras >= 0), Bonificacion DECIMAL(10,2) DEFAULT 0.00 CHECK (Bonificacion >= 0), CONSTRAINT PK_Bonificacion PRIMARY KEY (ID_bonificacion), CONSTRAINT UQ_Cliente_Mes UNIQUE (ID_Cliente, Mes_año), CONSTRAINT FK_Bonificacion_Cliente FOREIGN KEY (ID_Cliente)  REFERENCES Cliente_Plus(ID_Cliente) ON DELETE CASCADE ON UPDATE CASCADE);

CREATE TABLE Pedido (
    ID_pedido INT AUTO_INCREMENT, Fecha TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP, Importe_Total DECIMAL(10,2) DEFAULT 0.00 CHECK (Importe_Total >= 0), ID_Cliente INT NOT NULL, ID_empleado INT NOT NULL, CONSTRAINT PK_Pedido PRIMARY KEY (ID_pedido), CONSTRAINT FK_Pedido_Cliente FOREIGN KEY (ID_Cliente) REFERENCES Cliente_Plus(ID_Cliente) ON DELETE RESTRICT ON UPDATE CASCADE, CONSTRAINT FK_Pedido_Empleado FOREIGN KEY (ID_empleado) REFERENCES Empleado(ID_empleado) ON DELETE RESTRICT ON UPDATE CASCADE);


CREATE TABLE Detalle_Pedido (
    ID_pedido INT, ID_producto INT, Cantidad INT NOT NULL CHECK (Cantidad > 0), Precio_Unitario DECIMAL(10,2) NOT NULL CHECK (Precio_Unitario >= 0), CONSTRAINT PK_Detalle_Pedido PRIMARY KEY (ID_pedido, ID_producto), CONSTRAINT FK_Detalle_Pedido_Pedido FOREIGN KEY (ID_pedido) REFERENCES Pedido(ID_pedido) ON DELETE CASCADE ON UPDATE CASCADE, CONSTRAINT FK_Detalle_Pedido_Producto FOREIGN KEY (ID_producto) REFERENCES Producto(ID_producto) ON DELETE RESTRICT ON UPDATE CASCADE );



CREATE VIEW Vista_Pedidos_Totales AS
SELECT 
    p.ID_pedido,
    p.Fecha,
    p.ID_Cliente,
    p.ID_empleado,
    COALESCE(SUM(dp.Cantidad * dp.Precio_Unitario), 0.00) AS Importe_Total_Calculado
FROM Pedido p
LEFT JOIN Detalle_Pedido dp ON p.ID_pedido = dp.ID_pedido
GROUP BY p.ID_pedido, p.Fecha, p.ID_Cliente, p.ID_empleado;



INSERT INTO Vivero (Nombre, Latitud, Longitud) VALUES
('Vivero Central Anaga', 28.481100, -16.321100),
('Vivero Sur Las Chafiras', 28.032100, -16.612000),
('Vivero Norte La Orotava', 28.391000, -16.523000),
('Vivero Costa Adeje', 28.080000, -16.730000),
('Vivero Gran Canaria Tamaraceite', 28.099000, -15.462000);



INSERT INTO Zona (ID_vivero, ID_Zona, Nombre, Latitud, Longitud) VALUES
(1, 1, 'Zona Exterior Plantas', 28.481110, -16.321110),
(1, 2, 'Almacén Principal', 28.481120, -16.321120),
(2, 1, 'Invernadero A', 28.032110, -16.612010),
(2, 2, 'Zona Decoración', 28.032120, -16.612020),
(3, 1, 'Zona Herramientas y Abonos', 28.391010, -16.523010);


INSERT INTO Producto (Nombre, Tipo, Precio) VALUES
('Rosal Rojo', 'Planta de Exterior', 12.50),
('Ficus Benjamina', 'Planta de Interior', 25.00),
('Sustrato Universal 50L', 'Abono', 8.95),
('Tijeras de Podar Pro', 'Herramienta', 18.20),
('Maceta Cerámica Artesanal', 'Decoración', 15.00);



INSERT INTO Stock (ID_vivero, ID_Zona, ID_producto, Cantidad) VALUES
(1, 1, 1, 150),
(1, 2, 3, 300),
(2, 1, 2, 45),
(2, 2, 5, 80),
(3, 1, 4, 25);



INSERT INTO Empleado (DNI_NIE, Nombre, Apellidos) VALUES
('12345678A', 'Juan', 'Pérez Gómez'),
('87654321B', 'María', 'López Hernández'),
('11223344C', 'Carlos', 'Rodríguez Martín'),
('44332211D', 'Laura', 'Sánchez Díaz'),
('55667788E', 'Pedro', 'García Torres');



INSERT INTO Historico_puesto (ID_empleado, ID_vivero, ID_Zona, Fecha_ini, Fecha_fin, Puesto_Tarea, Indicador_Productividad) VALUES
(1, 1, 1, '2025-01-01', '2025-06-30', 'Encargado de Riego', 92.50),
(1, 1, 2, '2025-07-01', NULL, 'Mozo de Almacén', 88.00),
(2, 2, 1, '2025-02-15', NULL, 'Especialista Invernadero', 95.00),
(3, 2, 2, '2025-03-01', '2025-08-31', 'Vendedor Decoración', 85.00),
(4, 3, 1, '2025-01-10', NULL, 'Atención al Cliente', 90.00);


INSERT INTO Cliente_Plus (Nombre, Apellidos, Email, Fecha_Ingreso_Plus) VALUES
('Ana', 'Fernández Ramos', 'ana.fernandez@email.com', '2024-01-15'),
('David', 'Suárez Castro', 'david.suarez@email.com', '2024-03-20'),
('Elena', 'Morales Gil', 'elena.morales@email.com', '2024-05-10'),
('Roberto', 'Mendoza Delgado', 'roberto.mendoza@email.com', '2024-08-01'),
('Carmen', 'Santana Ruiz', 'carmen.santana@email.com', '2025-01-05');



INSERT INTO Bonificacion (ID_Cliente, Mes_año, Volumen_compras, Bonificacion) VALUES
(1, '2026-01', 150.00, 15.00),
(1, '2026-02', 200.00, 20.00),
(2, '2026-01', 80.00, 5.00),
(3, '2026-02', 310.00, 35.00),
(4, '2026-02', 50.00, 0.00);



INSERT INTO Pedido (Fecha, Importe_Total, ID_Cliente, ID_empleado) VALUES
('2026-02-01 10:30:00', 37.50, 1, 1),
('2026-02-05 12:15:00', 25.00, 2, 2),
('2026-02-10 16:45:00', 45.40, 3, 4),
('2026-02-14 11:00:00', 15.00, 1, 1),
('2026-02-20 18:20:00', 89.50, 4, 3);

INSERT INTO Detalle_Pedido (ID_pedido, ID_producto, Cantidad, Precio_Unitario) VALUES
(1, 1, 3, 12.50), 
(2, 2, 1, 25.00), 
(3, 3, 2, 8.95), 
(3, 4, 1, 18.20), 
(4, 5, 1, 15.00);

UPDATE Pedido p
SET Importe_Total = (
    SELECT COALESCE(SUM(dp.Cantidad * dp.Precio_Unitario), 0)
    FROM Detalle_Pedido dp
    WHERE dp.ID_pedido = p.ID_pedido
);
