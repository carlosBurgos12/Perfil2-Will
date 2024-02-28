DROP DATABASE db_RapidMart;

CREATE DATABASE db_RapidMart;
USE db_RapidMart;


-- Create the categorias table
CREATE TABLE categorias (
  id_categoria CHAR(36) DEFAULT (UUID()),
  nombre_categoria VARCHAR(50) NOT NULL
);


-- Insertar 10 registros en la tabla categorias
INSERT INTO categorias (nombre_categoria) VALUES
('Categoría 1'),
('Categoría 2'),
('Categoría 3'),
('Categoría 4'),
('Categoría 5'),
('Categoría 6'),
('Categoría 7'),
('Categoría 8'),
('Categoría 9'),
('Categoría 10');

SELECT * FROM categorias;

-- Create the proveedores table
CREATE TABLE proveedores (
  id_proveedor CHAR(36)DEFAULT (UUID()),
  nombre_proveedor VARCHAR(50) NOT NULL,
  direccion_proveedor VARCHAR(50) NOT NULL,
  telefono_proveedor VARCHAR(10) NOT NULL
);

-- Insertar 10 registros en la tabla proveedores
INSERT INTO proveedores (nombre_proveedor, direccion_proveedor, telefono_proveedor) VALUES
('Proveedor 1', 'Dirección 1', '1234567890'),
('Proveedor 2', 'Dirección 2', '2345678901'),
('Proveedor 3', 'Dirección 3', '3456789012'),
('Proveedor 4', 'Dirección 4', '4567890123'),
('Proveedor 5', 'Dirección 5', '5678901234'),
('Proveedor 6', 'Dirección 6', '6789012345'),
('Proveedor 7', 'Dirección 7', '7890123456'),
('Proveedor 8', 'Dirección 8', '8901234567'),
('Proveedor 9', 'Dirección 9', '9012345678'),
('Proveedor 10', 'Dirección 10', '0123456789');

SELECT * FROM proveedores;

-- Create the productos table
CREATE TABLE productos (
  id_producto CHAR(36) DEFAULT (UUID()),
  id_categoria CHAR(36) NOT NULL,
  nombre_producto VARCHAR(50) NOT NULL,
  descripcion_producto VARCHAR(255) NOT NULL,
  precio_unitario DECIMAL(10, 2) NOT NULL
);

INSERT INTO productos ( nombre_producto, descripcion_producto, precio_unitario) VALUES
( 'Producto 1', 'Descripción 1', 10.50),
( 'Producto 2', 'Descripción 2', 20.75),
( 'Producto 3', 'Descripción 3', 15.25),
( 'Producto 4', 'Descripción 4', 30.00),
( 'Producto 5', 'Descripción 5', 18.90),
('Producto 6', 'Descripción 6', 25.60),
( 'Producto 7', 'Descripción 7', 12.75),
( 'Producto 8', 'Descripción 8', 22.00),
( 'Producto 9', 'Descripción 9', 28.50),
( 'Producto 10', 'Descripción 10', 35.25);

SELECT * FROM productos;


-- Create the inventarios table
CREATE TABLE inventarios(
	id_inventario CHAR(36) DEFAULT (UUID()),
	cantidad_disponible INT,
	fecha_ingreso DATE,
	id_producto CHAR(36) NOT NULL
);

INSERT INTO inventarios (cantidad_disponible, fecha_ingreso, id_producto) VALUES
(100, '2024-02-28', '7ffe7b90-d65c-11ee-a1f7-7486e220044e'),
(120, '2024-02-28', '7ffe8b7f-d65c-11ee-a1f7-7486e220044e'),
(80, '2024-02-28', '7ffe8bf0-d65c-11ee-a1f7-7486e220044e'),
(150, '2024-02-28', '7ffe8c60-d65c-11ee-a1f7-7486e220044e'),
(90, '2024-02-28', '7ffe8c60-d65c-11ee-a1f7-7486e220044e'),
(110, '2024-02-28', '7ffe8c8e-d65c-11ee-a1f7-7486e220044e'),
(70, '2024-02-28', '7ffe8cb9-d65c-11ee-a1f7-7486e220044e'),
(130, '2024-02-28', '7ffe8ce3-d65c-11ee-a1f7-7486e220044e'),
(100, '2024-02-28', '7ffe8d12-d65c-11ee-a1f7-7486e220044e'),
(140, '2024-02-28', '7ffe8d3e-d65c-11ee-a1f7-7486e220044e');

SELECT * FROM inventarios;

-- Create the movimientos_inventarios table
CREATE TABLE movimientos_inventarios (
  id_movimiento_inventario CHAR(36) DEFAULT (UUID()),
  id_inventario CHAR(36) NOT NULL,
  tipo_movimiento ENUM('Entrada', 'Salida') NOT NULL,
  cantidad INT NOT NULL,
  fecha_movimiento DATE NOT NULL
);


INSERT INTO movimientos_inventarios ( tipo_movimiento, cantidad, fecha_movimiento) VALUES
( 'Entrada', 50, '2024-02-28'),
( 'Salida', 30, '2024-02-28'),
( 'Entrada', 40, '2024-02-28'),
( 'Salida', 20, '2024-02-28'),
( 'Entrada', 60, '2024-02-28'),
( 'Salida', 25, '2024-02-28'),
( 'Entrada', 35, '2024-02-28'),
( 'Salida', 15, '2024-02-28'),
( 'Entrada', 45, '2024-02-28'),
( 'Salida', 10, '2024-02-28');

SELECT * FROM movimientos_inventarios;


DELIMITER //

CREATE TRIGGER trigger_actualizar_inventario AFTER INSERT ON movimientos_inventarios
FOR EACH ROW
BEGIN
    DECLARE cantidad_movimiento INT;
    DECLARE tipo_movimiento VARCHAR(10);
    
   
    SET tipo_movimiento = NEW.tipo_movimiento;
    SET cantidad_movimiento = NEW.cantidad;
  
    IF tipo_movimiento = 'Entrada' THEN
        UPDATE inventarios
        SET cantidad_disponible = cantidad_disponible + cantidad_movimiento
        WHERE id_inventario = NEW.id_inventario;
    ELSEIF tipo_movimiento = 'Salida' THEN
        UPDATE inventarios
        SET cantidad_disponible = cantidad_disponible - cantidad_movimiento
        WHERE id_inventario = NEW.id_inventario;
    END IF;
END;
//

DELIMITER ;


