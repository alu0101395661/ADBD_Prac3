# ADBD_Prac3

## Modelo entidad-relación

<img width="1547" height="998" alt="Diagrama drawio" src="https://github.com/user-attachments/assets/f032fa47-27a4-4d6e-b8c8-541976a4b758" />



## Modelo Relacional

<img width="1068" height="1009" alt="Diagrama drawio (3)" src="https://github.com/user-attachments/assets/bd26b00f-8c81-4da4-bf3f-61e530077e09" />




## Script SQL

### Debe crear una base de datos viveros.

CREATE DATABASE viveros;

### Tablas del modelo relacional

### Tabla de Vivero

CREATE TABLE Vivero (
    ID_vivero SERIAL,
    Nombre VARCHAR(100) NOT NULL,
    Latitud DECIMAL(9,6) NULL CHECK (Latitud BETWEEN -90.000000 AND 90.000000),
    Longitud DECIMAL(9,6) NULL CHECK (Longitud BETWEEN -180.000000 AND 180.000000),
    CONSTRAINT PK_Vivero PRIMARY KEY (ID_vivero)
);

ID_vivero SERIAL: Número que se asigna a cada elemento de la tabla y va incrementando según se añade más elementos.

Nombre VARCHAR(100) NOT NULL: Variable de hasta 100 caracteres que no puede ser vacio.

Latitud DECIMAL(9,6) NULL CHECK (Latitud BETWEEN -90.000000 AND 90.000000): número decimal que debe estar entre el -90 y el 90 con un total de 9 dígitos, siendo 6 de ellos números decimales.

Longitud DECIMAL(9,6) NULL CHECK (Longitud BETWEEN -180.000000 AND 180.000000):  número decimal que debe estar entre el -180 y el 180 con un total de 9 dígitos, siendo 6 de ellos números decimales.

 PK_Vivero PRIMARY KEY (ID_vivero): ID_vivero es la llave primaria.

### Ejemplos añadidos

INSERT INTO Vivero (Nombre, Latitud, Longitud) VALUES
('Vivero Central Anaga', 28.481100, -16.321100),
('Vivero Sur Las Chafiras', 28.032100, -16.612000),
('Vivero Norte La Orotava', 28.391000, -16.523000),
('Vivero Costa Adeje', 28.080000, -16.730000),
('Vivero Gran Canaria Tamaraceite', 28.099000, -15.462000);

<img width="877" height="227" alt="image" src="https://github.com/user-attachments/assets/3331f17a-570a-4cc9-b1a8-c7c22ce2720f" />

### Tabla de Zona

CREATE TABLE Zona (
    ID_vivero INT NOT NULL,
    ID_Zona INT NOT NULL,
    Nombre VARCHAR(50) NOT NULL,
    Latitud DECIMAL(9,6) NULL CHECK (Latitud BETWEEN -90.000000 AND 90.000000),
    Longitud DECIMAL(9,6) NULL CHECK (Longitud BETWEEN -180.000000 AND 180.000000),
    CONSTRAINT PK_Zona PRIMARY KEY (ID_vivero, ID_Zona),
    CONSTRAINT FK_Zona_Vivero FOREIGN KEY (ID_vivero) 
        REFERENCES Vivero(ID_vivero) 
        ON DELETE CASCADE ON UPDATE CASCADE
);

ID_vivero INT NOT NULL:  Entero que no puede ser negativo que hace referencia al vivero asociado.

ID_Zona INT NOT NULL:  Entero que no puede ser negativo que hace referencia a la zona que es.

Nombre VARCHAR(50) NOT NULL: Variable de hasta 50 caracteres que no puede ser vacio.

Latitud DECIMAL(9,6) NULL CHECK (Latitud BETWEEN -90.000000 AND 90.000000): número decimal que debe estar entre el -90 y el 90 con un total de 9 dígitos, siendo 6 de ellos números decimales.

Longitud DECIMAL(9,6) NULL CHECK (Longitud BETWEEN -180.000000 AND 180.000000):  número decimal que debe estar entre el -180 y el 180 con un total de 9 dígitos, siendo 6 de ellos números decimales.

PK_Zona PRIMARY KEY (ID_vivero, ID_Zona): Asignación de llaves primarias.

FK_Zona_Vivero FOREIGN KEY (ID_vivero): Asignación de llaves foráneas. 

Vivero(ID_vivero) ON DELETE CASCADE ON UPDATE CASCADE: En caso de que se borre el id_vivero, se actualizará la cascada automáticamente. 

### Ejemplos añadidos

INSERT INTO Zona (ID_vivero, ID_Zona, Nombre, Latitud, Longitud) VALUES
(1, 1, 'Zona Exterior Plantas', 28.481110, -16.321110),
(1, 2, 'Almacén Principal', 28.481120, -16.321120),
(2, 1, 'Invernadero A', 28.032110, -16.612010),
(2, 2, 'Zona Decoración', 28.032120, -16.612020),
(3, 1, 'Zona Herramientas y Abonos', 28.391010, -16.523010);


<img width="941" height="252" alt="image" src="https://github.com/user-attachments/assets/3fedcda8-f24e-4ad8-838e-1a60c5d6b50d" />

### Tabla de Producto

CREATE TABLE Producto (
    ID_producto SERIAL,
    Nombre VARCHAR(100) NOT NULL,
    Tipo VARCHAR(50) NULL CHECK (Tipo IN ('Planta', 'Herramienta', 'Abono', 'Decoración')),
    Precio DECIMAL(10,2) NOT NULL CHECK (Precio >= 0.00),
    CONSTRAINT PK_Producto PRIMARY KEY (ID_producto)
);

ID_producto SERIAL: Número que se asigna a cada elemento de la tabla y va incrementando según se añade más elementos.

Nombre VARCHAR(100) NOT NULL: Variable de hasta 100 caracteres que no puede ser vacio.

Tipo VARCHAR(50) NULL CHECK (Tipo IN ('Planta', 'Herramienta', 'Abono', 'Decoración')): Elemento de hasta 50 caracteres que debe ser una planta, herramienta, abono o decoración. 


Precio DECIMAL(10,2) NOT NULL CHECK (Precio >= 0.00): Decimal de hasta 10 cifras, de los cuales 2 son número decimales, que deberá ser mayor que 0 y no puede ser un elemento o atributo vacío.

PK_Producto PRIMARY KEY (ID_producto): Asignación de llaves primarias.



### Ejemplos añadidos

INSERT INTO Producto (Nombre, Tipo, Precio) VALUES
('Rosal Rojo', 'Planta', 12.50),
('Crece plantas 50L', 'Abono', 8.95),
('Tijeras de Podar Pro', 'Herramienta', 18.20),
('Maceta de Cerámica', 'Decoración', 15.00);

<img width="770" height="202" alt="image" src="https://github.com/user-attachments/assets/c9abe751-7052-4ba0-b684-f8b59ed9cecc" />

### Tabla de Stock


CREATE TABLE Stock (
    ID_vivero INT NOT NULL,
    ID_Zona INT NOT NULL,
    ID_producto INT NOT NULL,
    Cantidad INT NOT NULL DEFAULT 0 CHECK (Cantidad >= 0),
    CONSTRAINT PK_Stock PRIMARY KEY (ID_vivero, ID_Zona, ID_producto),
    CONSTRAINT FK_Stock_Zona FOREIGN KEY (ID_vivero, ID_Zona) 
        REFERENCES Zona(ID_vivero, ID_Zona) 
        ON DELETE CASCADE ON UPDATE CASCADE,
    CONSTRAINT FK_Stock_Producto FOREIGN KEY (ID_producto) 
        REFERENCES Producto(ID_producto) 
        ON DELETE RESTRICT ON UPDATE CASCADE
);

ID_vivero INT NOT NULL:  Entero que no puede ser negativo que hace referencia al vivero asociado.

ID_Zona INT NOT NULL:  Entero que no puede ser negativo que hace referencia a la zona que es.

ID_producto INT NOT NULL:  Entero que no puede ser negativo que hace referencia al producto referenciado.

Cantidad INT NOT NULL DEFAULT 0 CHECK (Cantidad >= 0): Entero que no puede ser menor que 0 que no puede ser elemento vacio.

PK_Stock PRIMARY KEY (ID_vivero, ID_Zona, ID_producto): Asignacion de llaves primarias.

FK_Stock_Zona FOREIGN KEY (ID_vivero, ID_Zona): Asignación de llave foránea.

FK_Stock_Producto FOREIGN KEY (ID_producto): Asignación de llave foránea.

### Ejemplos añadidos

INSERT INTO Stock (ID_vivero, ID_Zona, ID_producto, Cantidad) VALUES
(1, 1, 1, 150),
(1, 2, 3, 300),
(2, 1, 2, 45),
(2, 2, 5, 80),
(3, 1, 4, 25);

### Tabla de Empleado

CREATE TABLE Empleado (
    ID_empleado SERIAL,
    DNI_NIE VARCHAR(10) NOT NULL,
    Nombre VARCHAR(50) NOT NULL,
    Apellidos VARCHAR(50) NOT NULL,
    CONSTRAINT PK_Empleado PRIMARY KEY (ID_empleado),
    CONSTRAINT UQ_Empleado_DNI UNIQUE (DNI_NIE)
);

ID_empleado SERIAL: Número que se asigna a cada elemento de la tabla y va incrementando según se añade más elementos.

DNI_NIE VARCHAR(10) NOT NULL: Variable de hasta 10 caracteres que no puede ser vacio.

Nombre VARCHAR(50) NOT NULL: Variable de hasta 50 caracteres que no puede ser vacio.

Apellidos VARCHAR(50) NOT NULL: Variable de hasta 50 caracteres que no puede ser vacio.

PK_Empleado PRIMARY KEY (ID_empleado): Asignacion de llave primaria.

UQ_Empleado_DNI UNIQUE (DNI_NIE): Asignación de llave única.


### Ejemplos añadidos

INSERT INTO Empleado (DNI_NIE, Nombre, Apellidos) VALUES
('12345678A', 'Juan', 'Pérez Gómez'),
('87654321B', 'María', 'López Hernández'),
('11223344C', 'Carlos', 'Rodríguez Martín'),
('44332211D', 'Laura', 'Sánchez Díaz'),
('55667788E', 'Pedro', 'García Torres');

<img width="700" height="191" alt="image" src="https://github.com/user-attachments/assets/4b04800b-c785-4bfb-a6fb-aab9d1c6ed21" />


### Tabla de Historico_puesto

CREATE TABLE Historico_puesto (
    ID_empleado INT NOT NULL,
    ID_vivero INT NOT NULL,
    ID_Zona INT NOT NULL,
    Fecha_ini DATE NOT NULL,
    Fecha_fin DATE NULL,
    Puesto_Tarea VARCHAR(50) NOT NULL,
    Indicador_Productividad DECIMAL(5,2) NULL CHECK (Indicador_Productividad BETWEEN 0.00 AND 100.00),
    CONSTRAINT PK_Historico_puesto PRIMARY KEY (ID_empleado, ID_vivero, ID_Zona, Fecha_ini),
    CONSTRAINT CK_Historico_Fechas CHECK (Fecha_fin IS NULL OR Fecha_fin >= Fecha_ini),
    CONSTRAINT FK_Historico_Empleado FOREIGN KEY (ID_empleado) 
        REFERENCES Empleado(ID_empleado) 
        ON DELETE CASCADE ON UPDATE CASCADE,
    CONSTRAINT FK_Historico_Zona FOREIGN KEY (ID_vivero, ID_Zona) 
        REFERENCES Zona(ID_vivero, ID_Zona) 
        ON DELETE CASCADE ON UPDATE CASCADE
);

### Ejemplos añadidos


INSERT INTO Historico_puesto (ID_empleado, ID_vivero, ID_Zona, Fecha_ini, Fecha_fin, Puesto_Tarea, Indicador_Productividad) VALUES
(1, 1, 1, '2025-01-01', '2025-06-30', 'Encargado de Riego', 92.50),
(1, 1, 2, '2025-07-01', NULL, 'Mozo de Almacén', 88.00),
(2, 2, 1, '2025-02-15', NULL, 'Especialista Invernadero', 95.00),
(3, 2, 2, '2025-03-01', '2025-08-31', 'Vendedor Decoración', 85.00),
(4, 3, 1, '2025-01-10', NULL, 'Atención al Cliente', 90.00);

<img width="1411" height="207" alt="image" src="https://github.com/user-attachments/assets/2ee05fcb-5656-4b95-9e82-267602eb7346" />

### Tabla de Cliente_Plus

CREATE TABLE Cliente_Plus (
    ID_Cliente SERIAL,
    Nombre VARCHAR(50) NOT NULL,
    Apellidos VARCHAR(100) NOT NULL,
    Email VARCHAR(100) NOT NULL,
    Fecha_Ingreso_Plus DATE NOT NULL DEFAULT CURRENT_DATE,
    CONSTRAINT PK_Cliente_Plus PRIMARY KEY (ID_Cliente),
    CONSTRAINT UQ_Cliente_Plus_Email UNIQUE (Email),
    CONSTRAINT CK_Cliente_Plus_Email_Format CHECK (Email LIKE '%@%.%')
);

### Ejemplos añadidos

INSERT INTO Cliente_Plus (Nombre, Apellidos, Email, Fecha_Ingreso_Plus) VALUES
('Ana', 'Fernández Ramos', 'ana.fernandez@email.com', '2024-01-15'),
('David', 'Suárez Castro', 'david.suarez@email.com', '2024-03-20'),
('Elena', 'Morales Gil', 'elena.morales@email.com', '2024-05-10'),
('Roberto', 'Mendoza Delgado', 'roberto.mendoza@email.com', '2024-08-01'),
('Carmen', 'Santana Ruiz', 'carmen.santana@email.com', '2025-01-05');

<img width="1092" height="197" alt="image" src="https://github.com/user-attachments/assets/422a4bf2-e251-411e-8b1d-6e2c5225494e" />



### Tabla de Bonificacion

CREATE TABLE Bonificacion (
    ID_bonificacion SERIAL,
    ID_Cliente INT NOT NULL,
    Mes_año VARCHAR(7) NOT NULL, -- Formato 'YYYY-MM'
    Volumen_compras DECIMAL(10,2) NOT NULL DEFAULT 0.00 CHECK (Volumen_compras >= 0.00),
    Bonificacion DECIMAL(10,2) NOT NULL DEFAULT 0.00 CHECK (Bonificacion >= 0.00),
    CONSTRAINT PK_Bonificacion PRIMARY KEY (ID_bonificacion),
    CONSTRAINT UQ_Bonificacion_Cliente_Mes UNIQUE (ID_Cliente, Mes_año),
    CONSTRAINT CK_Bonificacion_MesFormat CHECK (Mes_año ~ '^[0-9]{4}-(0[1-9]|1[0-2])$'),
    CONSTRAINT FK_Bonificacion_Cliente FOREIGN KEY (ID_Cliente) 
        REFERENCES Cliente_Plus(ID_Cliente) 
        ON DELETE CASCADE ON UPDATE CASCADE
);

### Ejemplos añadidos

INSERT INTO Bonificacion (ID_Cliente, Mes_año, Volumen_compras, Bonificacion) VALUES
(1, '2026-01', 150.00, 15.00),
(1, '2026-02', 200.00, 20.00),
(2, '2026-01', 80.00, 5.00),
(3, '2026-02', 310.00, 35.00),
(4, '2026-02', 50.00, 0.00);


<img width="937" height="220" alt="image" src="https://github.com/user-attachments/assets/fbc4b735-46b5-48c0-bc30-95bbb32b6371" />


### Tabla de Pedido

CREATE TABLE Pedido (
    ID_pedido SERIAL,
    Fecha TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    Importe_Total DECIMAL(10,2) NOT NULL DEFAULT 0.00 CHECK (Importe_Total >= 0.00), -- Atributo Derivado
    ID_Cliente INT NOT NULL,
    ID_empleado INT NOT NULL,
    CONSTRAINT PK_Pedido PRIMARY KEY (ID_pedido),
    CONSTRAINT FK_Pedido_Cliente FOREIGN KEY (ID_Cliente) 
        REFERENCES Cliente_Plus(ID_Cliente) 
        ON DELETE RESTRICT ON UPDATE CASCADE,
    CONSTRAINT FK_Pedido_Empleado FOREIGN KEY (ID_empleado) 
        REFERENCES Empleado(ID_empleado) 
        ON DELETE RESTRICT ON UPDATE CASCADE
);

### Ejemplos añadidos

INSERT INTO Pedido (Fecha, Importe_Total, ID_Cliente, ID_empleado) VALUES
('2026-02-01 10:30:00', 37.50, 1, 1),
('2026-02-05 12:15:00', 25.00, 2, 2),
('2026-02-10 16:45:00', 45.40, 3, 4),
('2026-02-14 11:00:00', 15.00, 1, 1),
('2026-02-20 18:20:00', 89.50, 4, 3);

<img width="932" height="232" alt="image" src="https://github.com/user-attachments/assets/e93fd587-486e-4759-9d8a-13287022d17a" />

### Tabla de Detalle_Pedido

CREATE TABLE Detalle_Pedido (
    ID_pedido INT NOT NULL,
    ID_producto INT NOT NULL,
    Cantidad INT NOT NULL CHECK (Cantidad > 0),
    Precio_Unitario DECIMAL(10,2) NOT NULL CHECK (Precio_Unitario >= 0.00),
    CONSTRAINT PK_Detalle_Pedido PRIMARY KEY (ID_pedido, ID_producto),
    CONSTRAINT FK_Detalle_Pedido_Pedido FOREIGN KEY (ID_pedido) 
        REFERENCES Pedido(ID_pedido) 
        ON DELETE CASCADE ON UPDATE CASCADE,
    CONSTRAINT FK_Detalle_Pedido_Producto FOREIGN KEY (ID_producto) 
        REFERENCES Producto(ID_producto) 
        ON DELETE RESTRICT ON UPDATE CASCADE
);

### Ejemplos añadidos

INSERT INTO Detalle_Pedido (ID_pedido, ID_producto, Cantidad, Precio_Unitario) VALUES
(1, 1, 3, 12.50), 
(2, 2, 1, 25.00), 
(3, 3, 2, 8.95), 
(3, 4, 1, 18.20), 
(4, 5, 1, 15.00);


<img width="657" height="211" alt="image" src="https://github.com/user-attachments/assets/dc9e66db-bb27-4d06-af7e-c197f56d5b42" />




## Ejemplos de la operación DELETE

### Borrado en Cascada:
Al eliminar un Vivero con el comando:

DELETE FROM Vivero 
WHERE ID_vivero = 1;

La base de datos elimina automáticamente las zonas asociadas en la tabla Zona, el stock que estaba en las zonas, y los registros del historico_puesto vinculado a esas zonas.

### Borrado de Padre
Al borrar a un cliente_plus con el comando:

DELETE FROM Cliente_Plus 
WHERE ID_Cliente = 5;

Sus bonificaciones mensuales se eliminan en cascada debido a las entidades dependientes.

### Prueba de Restricción Integridad  (ON DELETE RESTRICT)

La siguiente sentencia fallará si se intenta borrar un Empleado o Producto que tiene Pedidos asgignaods.

DELETE FROM Empleado WHERE ID_empleado = 1;








