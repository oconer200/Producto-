CREATE DATABASE agcontrol

USE agcontrol


CREATE TABLE Tipos_Suelo (
    id_suelo INT PRIMARY KEY,
    nombre VARCHAR(50)
);

CREATE TABLE Sistemas_Riego (
    id_riego INT PRIMARY KEY,
    nombre VARCHAR(50)
);

CREATE TABLE Cultivos (
    id_cultivo INT PRIMARY KEY,
    nombre_comun VARCHAR(50),
    nombre_cientifico VARCHAR(50),
    tiempo_crecimiento INT,
    agua_requerida VARCHAR(50),
    TempOptima DECIMAL(5,2)
);

CREATE TABLE Unidades_Medida (
    id_unidad INT PRIMARY KEY,
    nombre VARCHAR(20)
);
 
CREATE TABLE Tipos_Insumo (
    id_tipo_insumo INT PRIMARY KEY,
    nombre VARCHAR(50)
);

CREATE TABLE Especialidades (
    id_especialidad INT PRIMARY KEY,
    nombre VARCHAR(50)
);

CREATE TABLE Metodos_Pago (
    id_metodo_pago INT PRIMARY KEY,
    nombre VARCHAR(50)
);

CREATE TABLE Metodos_Cosecha (
    id_metodo_cosecha INT PRIMARY KEY,
    nombre VARCHAR(50)
);

CREATE TABLE Calidades (
    id_calidad INT PRIMARY KEY,
    nombre VARCHAR(50)
);

CREATE TABLE area  (
    AreaID INT PRIMARY KEY AUTO_INCREMENT,
    NombreArea VARCHAR(100)
);

CREATE TABLE Almacenes (
    id_almacen INT PRIMARY KEY,
    nombre VARCHAR(50),
    ubicacion VARCHAR(50)
);

CREATE TABLE Maquinaria (
    MaquinariaID INT PRIMARY KEY,
    Marca VARCHAR(50),
    Modelo VARCHAR(50),
    AñoFabricacion YEAR,
    Potencia VARCHAR(50),
    Combustible VARCHAR(30),
    Horometro DECIMAL(10,2),
    Estado VARCHAR(50)
);

CREATE TABLE Clientes (
    ClienteID INT PRIMARY KEY AUTO_INCREMENT,
    Nombre VARCHAR(100),
    RUC_DNI VARCHAR(20),
    DireccionFiscal VARCHAR(200),
    Telefono VARCHAR(20),
    Email VARCHAR(100),
    LineaCredito DECIMAL(10,2)
);

CREATE TABLE proveedores (
    ProveedorID INT PRIMARY KEY AUTO_INCREMENT,
    Nombre VARCHAR(100),
    RUC_DNI VARCHAR(20),
    DireccionFiscal VARCHAR(200),
    Telefono VARCHAR(20),
    Email VARCHAR(100),
    Especialidad VARCHAR(100),
    CondicionesPago VARCHAR(100)
);

CREATE TABLE Tareas (
    id_tarea INT PRIMARY KEY,
    nombre VARCHAR(50)
);

CREATE TABLE Fincas (
    id_finca INT PRIMARY KEY,
    nombre VARCHAR(50),
    extension INT,
    id_suelo INT,
    Latitud DECIMAL(9,6),
    Longitud DECIMAL(9,6),
	 Altitud INT,
    TempPromedio DECIMAL(5,2),
    FOREIGN KEY (id_suelo) REFERENCES Tipos_Suelo(id_suelo)
);

CREATE TABLE Parcelas (
    id_parcela INT PRIMARY KEY,
    codigo VARCHAR(20),
    area INT,
    id_finca INT,
    id_riego INT,
    HistorialUso TEXT,
    FOREIGN KEY (id_finca) REFERENCES Fincas(id_finca),
    FOREIGN KEY (id_riego) REFERENCES Sistemas_Riego(id_riego)
);

CREATE TABLE Empleados (
    dni VARCHAR(20) PRIMARY KEY,
    nombre VARCHAR(50),
    apellido VARCHAR(50),
    FechaNacimiento DATE,
    Direccion VARCHAR(200),
    telefono VARCHAR(20),
    id_especialidad INT,
    FechaContratacion DATE,
    salario INT,
    AreaID INT,
    FOREIGN KEY (AreaID) REFERENCES area(AreaID),
    FOREIGN KEY (id_especialidad) REFERENCES Especialidades(id_especialidad)
);

CREATE TABLE Insumos (
    id_insumo INT PRIMARY KEY,
    nombre VARCHAR(50),
    id_tipo_insumo INT,
	 id_unidad INT,
    stock INT,
    id_almacen INT,
    FechaCaducidad DATE,
	 precio INT,
    ProveedorID INT,
    FOREIGN KEY (ProveedorID) REFERENCES Proveedores(ProveedorID),
    FOREIGN KEY (id_tipo_insumo) REFERENCES Tipos_Insumo(id_tipo_insumo),
    FOREIGN KEY (id_unidad) REFERENCES Unidades_Medida(id_unidad),
    FOREIGN KEY (id_almacen) REFERENCES Almacenes(id_almacen)
);

CREATE TABLE Movimientos_Insumos (
    id_movimiento INT PRIMARY KEY,
    id_insumo INT,
    fecha DATE,
    tipo VARCHAR(10),
    cantidad INT,
    ResponsableID INT,
    Destino VARCHAR(100),
    FOREIGN KEY (id_insumo) REFERENCES Insumos(id_insumo),
    FOREIGN KEY (ResponsableID) REFERENCES Empleados(EmpleadoID)
);

CREATE TABLE Mantenimientos (
    MantenimientoID INT PRIMARY KEY AUTO_INCREMENT,
    MaquinariaID INT NOT NULL,
    Fecha DATE,
    Tipo VARCHAR(50),
    Descripcion TEXT,
    PiezasReemplazadas TEXT,
    Costo DECIMAL(10,2),
    TecnicoID INT,
    FOREIGN KEY (MaquinariaID) REFERENCES Maquinaria(MaquinariaID),
    FOREIGN KEY (TecnicoID) REFERENCES Empleados(EmpleadoID)
);

CREATE TABLE Repuestos_Usados (
    id_repUsados INT PRIMARY KEY,
	 id_mantenimiento INT,
    id_insumo INT,
    cantidad INT,
    FOREIGN KEY (id_mantenimiento) REFERENCES Mantenimientos(id_mantenimiento),
    FOREIGN KEY (id_insumo) REFERENCES Insumos(id_insumo)
);


CREATE TABLE Siembras (
    SiembraID INT PRIMARY KEY,
    ParcelaID INT NOT NULL,
    CultivoID INT NOT NULL,
    FechaSiembra DATE,
    DensidadSiembra DECIMAL(10,2),
    FechaEstCosecha DATE,
    RendimientoEsperado DECIMAL(10,2),
    FOREIGN KEY (ParcelaID) REFERENCES Parcelas(ParcelaID),
    FOREIGN KEY (CultivoID) REFERENCES Cultivos(CultivoID)
);

CREATE TABLE Cosechas (
    id_cosecha INT PRIMARY KEY,
    fecha_ini DATE,
    fecha_fin DATE,
    cantidad INT,
    id_siembra INT,
    id_metodo_cosecha INT,
    id_calidad INT,
    FOREIGN KEY (id_siembra) REFERENCES Siembras(id_siembra),
    FOREIGN KEY (id_metodo_cosecha) REFERENCES Metodos_Cosecha(id_metodo_cosecha),
    FOREIGN KEY (id_calidad) REFERENCES Calidades(id_calidad)
);

CREATE TABLE Asignaciones (
    id_asignacion INT PRIMARY KEY,
    fecha DATE,
    horas INT,
    dni_empleado VARCHAR(20),
    id_parcela INT,
    id_tarea INT,
    Observaciones TEXT,
    FOREIGN KEY (dni_empleado) REFERENCES Empleados(dni),
    FOREIGN KEY (id_parcela) REFERENCES Parcelas(id_parcela),
    FOREIGN KEY (id_tarea) REFERENCES Tareas(id_tarea)
);

CREATE TABLE Ventas (
    id_venta INT PRIMARY KEY,
    fecha DATE,
    id_cliente INT,
    id_metodo_pago INT,
    Descuento DECIMAL(10,2),
    Impuestos DECIMAL(10,2),
    total INT,
    FOREIGN KEY (id_cliente) REFERENCES Clientes(id_cliente),
    FOREIGN KEY (id_metodo_pago) REFERENCES Metodos_Pago(id_metodo_pago)
);

CREATE TABLE Detalles_Venta (
    id_detalle INT PRIMARY KEY,
    id_venta INT,
    id_cosecha INT,
    cantidad INT,
    precio INT,
    FOREIGN KEY (id_venta) REFERENCES Ventas(id_venta),
    FOREIGN KEY (id_cosecha) REFERENCES Cosechas(id_cosecha)
);

CREATE TABLE Analisis_Suelo (
    AnalisisID INT PRIMARY KEY AUTO_INCREMENT,
    ParcelaID INT NOT NULL,
    FechaMuestreo DATE,
    pH DECIMAL(4,2),
    NutrienteN DECIMAL(10,2),
    NutrienteP DECIMAL(10,2),
    NutrienteK DECIMAL(10,2),
    MateriaOrganica DECIMAL(10,2),
    ConductividadElectrica DECIMAL(10,2),
    Laboratorio VARCHAR(100),
    Recomendaciones TEXT,
    FOREIGN KEY (ParcelaID) REFERENCES Parcelas(ParcelaID)
);


INSERT INTO Tipos_Suelo VALUES (1,'Arcilloso'),(2,'Arenoso'),(3,'Limoso'),(4,'Franco'),(5,'Salino'),(6,'Turba'),(7,'Calizo'),(8,'Franco-Arcilloso'),(9,'Franco-Arenoso'),(10,'Volcánico');
INSERT INTO Sistemas_Riego VALUES (1,'Goteo'),(2,'Aspersión'),(3,'Gravedad'),(4,'Pivot Central'),(5,'Microaspersión'),(6,'Manual'),(7,'Exudación'),(8,'Multicompuerta'),(9,'Subterráneo'),(10,'Hidropónico');
INSERT INTO Cultivos VALUES (1,'Maíz','Zea mays',120,'Media'),(2,'Trigo','Triticum',150,'Baja'),(3,'Arroz','Oryza sativa',110,'Alta'),(4,'Tomate','Solanum lycopersicum',90,'Media'),(5,'Papa','Solanum tuberosum',130,'Media'),(6,'Café','Coffea arabica',1000,'Alta'),(7,'Girasol','Helianthus annuus',100,'Baja'),(8,'Cebolla','Allium cepa',120,'Baja'),(9,'Zanahoria','Daucus carota',110,'Media'),(10,'Fresa','Fragaria',75,'Alta');
INSERT INTO Unidades_Medida VALUES (1,'Kilogramos'),(2,'Litros'),(3,'Toneladas'),(4,'Gramos'),(5,'Metros'),(6,'Sacos'),(7,'Galones'),(8,'Bultos'),(9,'Cajas'),(10,'Mililitros');
INSERT INTO Tipos_Insumo VALUES (1,'Fertilizante'),(2,'Pesticida'),(3,'Semilla'),(4,'Herbicida'),(5,'Fungicida'),(6,'Combustible'),(7,'Lubricante'),(8,'Herramienta Manual'),(9,'Abono Orgánico'),(10,'Repuesto');
INSERT INTO Especialidades VALUES (1,'Agrónomo'),(2,'Ingeniera Agrícola'),(3,'Mecánico'),(4,'Operaria'),(5,'Logística'),(6,'Ventas'),(7,'Almacenista'),(8,'Control de calidad'),(9,'Administrador'),(10,'Investigadora');
INSERT INTO Metodos_Pago VALUES (1,'Efectivo'),(2,'Transferencia'),(3,'Crédito 30 días'),(4,'Cheque'),(5,'Tarjeta Débito'),(6,'Tarjeta Crédito'),(7,'Trueque Insumos'),(8,'Remesa'),(9,'App Móvil'),(10,'Giro');
INSERT INTO Metodos_Cosecha VALUES (1,'Manual'),(2,'Mecánica Combinada'),(3,'Manual con Tijera'),(4,'Vibración'),(5,'Segadora'),(6,'Arranque'),(7,'Ordeño'),(8,'Corte Directo'),(9,'Escalera'),(10,'Automatizada');
INSERT INTO Calidades VALUES (1,'Premium (Exportación)'),(2,'Primera'),(3,'Segunda'),(4,'Industrial'),(5,'Para Semilla'),(6,'Orgánica Certificada'),(7,'Procesada'),(8,'Procesada'),(9,'Granel'),(10,'Maduración Media');
INSERT INTO area (NombreArea) VALUES('Cultivos'),('Riego'),('Mantenimiento'),('Cosecha'),('Logística'),('Ventas'),('Almacén'),('Calidad'),('Administración'),('Investigación');
INSERT INTO Tareas VALUES (1,'Cultivos'),(2,'Riego'),(3,'Mantenimiento'),(4,'Cosecha'),(5,'Logística'),(6,'Ventas'),(7,'Almacén'),(8,'Calidad'),(9,'Administración'),(10,'Investigación');

INSERT INTO Fincas VALUES (1,'La Esperanza',50,4,6.032145, -75.428765, 2100, 18.5),(2,'El Recreo',30,1,6.123456, -75.456789, 1950, 19.0),(3,'San José',100,3,6.234567, -75.567890, 1800, 20.0),(4,'Bella Vista',25,10,6.345678, -75.678901, 1700, 21.0),(5,'Los Olivos',45,8,6.456789, -75.789012, 1600, 22.0),(6,'La Pampa',80,2,6.567890, -75.890123, 1750, 19.5),(7,'El Paraíso',60,4,6.678901, -75.901234, 1850, 20.5),(8,'Don Antón',35,3,6.789012, -75.912345, 1900, 18.0),(9,'Villa Flor',20,9,6.890123, -75.923456, 2000, 17.5),(10,'Las Acacias',55,4,6.901234, -75.934567, 2100, 16.5);
INSERT INTO Parcelas VALUES (1,'P-01',5,1,1,'Maíz en 2024'),(2,'P-02',5,1,2,''),(3,'P-03',10,2,3,'Papa en 2024'),(4,'P-04',8,3,4,''),(5,'P-05',4,4,5,'Tomate en 2024'),(6,'P-06',12,5,1,''),(7,'P-07',15,6,3,'Trigo en 2024'),(8,'P-08',6,7,2,''),(9,'P-09',5,8,1,'Aguacate en 2024'),(10,'P-10',10,9,5,'');
INSERT INTO Empleados VALUES ('1001', 'Carlos', 'Ramírez', '1985-05-10', 'Medellín', '3001111111', 1, '2020-01-15', 2500000, 1),('1002', 'Ana', 'Gómez', '1990-07-20', 'Cali', '3002222222', 2, '2021-03-10', 2800000, 2),('1003', 'Luis', 'Martínez', '1988-09-12', 'Bogotá', '3003333333', 3, '2019-06-01', 2200000, 3),('1004', 'María', 'Fernández', '1992-11-05', 'Manizales', '3004444444', 4, '2022-02-20', 1800000, 4),('1005', 'Jorge', 'Pérez', '1987-03-18', 'Pereira', '3005555555', 5, '2020-09-15', 2000000, 5),('1006', 'Sofía', 'López', '1995-01-25', 'Barranquilla', '3006666666', 6, '2021-05-30', 2300000, 6),('1007', 'Andrés', 'Torres', '1989-04-14', 'Tunja', '3007777777', 7, '2018-08-10', 1900000, 7),('1008', 'Paula', 'Castro', '1993-06-22', 'Medellín', '3008888888', 8, '2022-07-01', 2100000, 8),('1009', 'Felipe', 'Moreno', '1986-02-11', 'Cali', '3009999999', 9, '2017-04-12', 3000000, 9),('1010', 'Laura', 'Jiménez', '1994-12-30', 'Bogotá', '3010000000', 10, '2023-01-05', 2700000, 10);

INSERT INTO Almacenes VALUES (1,'Bodega Central','B100'),(2,'Bodega Central','A900'),(3,'Bodega Central','A700'),(4,'Bodega Central','A800'),(5,'Bodega Central','A600'),(6,'Bodega Central','A500'),(7,'Bodega Central','A400'),(8,'Bodega Central','A100'),(9,'Bodega Central','A300'),(10,'Bodega Central','A200');
INSERT INTO Insumos VALUES (1,'Urea', 1, 1, 500, 1, '2026-12-31', 1200, 1),(2,'Semilla Maíz Híbrido', 3, 1, 200, 2, '2027-03-01', 5000, 2),(3,'Herbicida Glifosato', 2, 2, 100, 5, '2026-06-30', 8000, 3),(4,'Fertilizante NPK', 1, 1, 300, 5, '2026-11-30', 2500, 4),(5,'Sistema Riego Goteo', 8, 1, 10, 8, '2028-01-01', 1500000, 5),(6,'Semilla Café', 3, 1, 150, 9, '2027-05-15', 6000, 6),(7,'Tractor John Deere', 8, 3, 2, 3, '2030-01-01', 120000000, 7),(8,'Biofertilizante Orgánico', 1, 2, 80, 5, '2026-09-30', 15000, 8),(9,'Insecticida Biológico', 2, 2, 60, 5, '2026-08-15', 20000, 9),(10,'Fertilizante Fosfato', 1, 1, 400, 5, '2026-10-31', 1800, 10);
INSERT INTO Movimientos_Insumos VALUES (1, 2, '2025-01-10', 'Entrada', 200, 1, 'Bodega 1'),(2, 2,'2025-02-05', 'Salida', 50, 2, 'Parcela 1'),(3, 3, '2025-01-15', 'Entrada', 100, 3, 'Bodega 2'),(4, 5,'2025-02-20', 'Salida', 30, 4, 'Parcela 2'),(5, 5,'2025-01-25', 'Entrada', 50, 5, 'Bodega 3'),(6, 9,'2025-03-01', 'Salida', 20, 6, 'Parcela 3'),(7, 5,'2025-02-10', 'Entrada', 150, 7, 'Bodega 1'),(8, 4,'2025-03-15', 'Salida', 40, 8, 'Parcela 4'),(9, 3,'2025-02-28', 'Entrada', 5, 9, 'Bodega 4'),(10, 9,'2025-03-20', 'Salida', 2, 10, 'Parcela 5');

INSERT INTO Maquinaria VALUES (1,'John Deere', 'JD5050', 2020, '50 HP', 'Diésel', 1200, 'Operativa'),(2,'Case IH', 'Farmall 75', 2019, '75 HP', 'Diésel', 1500, 'Operativa'),(3,'Kubota', 'M7040', 2021, '70 HP', 'Diésel', 800, 'Operativa'),(4,'New Holland', 'T5.95', 2018, '95 HP', 'Diésel', 2000, 'Mantenimiento'),(5,'Massey Ferguson', 'MF4707', 2022, '75 HP', 'Diésel', 500, 'Operativa'),(6,'John Deere', 'JD6110', 2017, '110 HP', 'Diésel', 2500, 'Operativa'),(7,'Case IH', 'Magnum 250', 2020, '250 HP', 'Diésel', 1800, 'Operativa'),(8,'Kubota', 'L6060', 2019, '60 HP', 'Diésel', 1300, 'Operativa'),(9,'New Holland', 'T7.210', 2021, '210 HP', 'Diésel', 900, 'Operativa'),(10,'Massey Ferguson', 'MF5711', 2022, '110 HP', 'Diésel', 400, 'Operativa');
INSERT INTO Mantenimientos VALUES (1,1,'2025-01-15', 'Preventivo', 'Cambio de aceite', 'Filtro de aceite', 500000, 1),(2,2, '2025-02-10', 'Correctivo', 'Reparación de frenos', 'Pastillas de freno', 1200000, 2),(3, 3,'2025-03-05', 'Preventivo', 'Lubricación general', 'Grasa industrial', 300000, 3),(4,4, '2025-01-20', 'Correctivo', 'Cambio de bomba hidráulica', 'Bomba hidráulica', 2500000, 4),(5, 5,'2025-02-25', 'Preventivo', 'Revisión eléctrica', 'Batería', 800000, 5),(6, 6,'2025-03-15', 'Correctivo', 'Cambio de embrague', 'Kit embrague', 1800000, 6),(7, 7,'2025-01-30', 'Preventivo', 'Cambio de filtros', 'Filtro aire', 400000, 7),(8,8, '2025-02-18', 'Correctivo', 'Reparación motor', 'Pistones', 3500000, 8),(9, 9,'2025-03-10', 'Preventivo', 'Revisión transmisión', 'Aceite transmisión', 600000, 9),(10, 10,'2025-03-22', 'Correctivo', 'Cambio de neumáticos', 'Neumáticos traseros', 2200000, 10);
INSERT INTO Repuestos_Usados VALUES (1,1,6,2),(2,1,10,1),(3,2,6,4),(4,4,6,5),(5,8,10,2),(6,10,6,2),(7,2,10,1),(8,3,6,1),(9,5,6,1),(10,6,10,1);
 
INSERT INTO Siembras VALUES (1,1, 1, '2024-03-01', 30000, '2024-07-01', 8000),(2,2, 2, '2024-02-15', 5000, '2024-12-04', 4000),(3,3, 3, '2024-04-10', 25000, '2024-07-10', 7000),(4,4, 4, '2024-03-20', 20000, '2024-06-20', 6000),(5,5, 5, '2024-01-30', 15000, '2024-05-30', 9000),(6,6, 6, '2024-02-05', 18000, '2024-06-05', 7500),(7,7, 7, '2024-03-25', 22000, '2024-08-25', 10000),(8,8, 8, '2024-01-10', 1000, '2024-01-10', 15000),(9,9, 9, '2024-02-28', 800, '2024-11-30', 12000),(10,10, 10, '2024-03-15', 1200, '2024-10-15', 11000);
INSERT INTO Cosechas VALUES (1,'2024-03-01','2024-07-01',12000,1,2,1),(2,'2024-02-15','2024-06-15',8000,2,1,2),(3,'2024-04-10','2024-08-20',15000,3,6,3),(4,'2024-03-20','2024-08-01',25000,4,5,1),(5,'2024-01-30','2024-08-10',2000,5,3,1),(6,'2024-02-05','2024-10-15',10000,6,2,2),(7,'2024-03-25','2024-09-30',5000,7,1,1),(8,'2024-01-10','2024-10-05',6000,8,1,2),(9,'2024-02-28','2024-10-10',3000,9,3,2),(10,'2024-03-15','2024-11-01',7000,10,8,1);
INSERT INTO Asignaciones VALUES (1,'2024-03-01',8,1,1,1,'Condiciones óptimas'),(2,'2024-03-02',8,2,2,2,'Se rego el cultivo'),(3,'2024-03-15',6,3,3,3,'Cambio de aceite'),(4,'2024-04-01',8,4,3,6,'Buen rendimiento'),(5,'2024-05-01',4,5,5,5,'Entrega a bodega'),(6,'2024-06-15',10,6,6,6,'Cliente satisfecho'),(7,'2024-07-01',8,7,7,7,'Stock actualizado'),(8,'2024-08-01',10,8,8,8,'Producto excelente'),(9,'2024-09-01',8,9,9,9,'Revisión contratos'),(10,'2024-10-01',8,10,10,10,'Prueba de fertilizantes');

INSERT INTO Clientes VALUES (1,'Supermercado Éxito', '800123456', 'Medellín', '3101111111', 'compras@exito.com', 50000000),(2,'Almacenes Olímpica', '800234567', 'Barranquilla', '3102222222', 'ventas@olimpica.com', 40000000),(3,'Carulla S.A.', '800345678', 'Bogotá', '3103333333', 'contacto@carulla.com', 30000000),(4,'Makro Colombia', '800456789', 'Cali', '3104444444', 'compras@makro.com', 60000000),(5,'Jumbo', '800567890', 'Bogotá', '3105555555', 'ventas@jumbo.com', 35000000),(6,'Tiendas D1', '800678901', 'Medellín', '3106666666', 'compras@d1.com', 25000000),(7,'Ara', '800789012', 'Cali', '3107777777', 'ventas@ara.com', 20000000),(8,'La Vaquita', '800890123', 'Tunja', '3108888888', 'contacto@lavaquita.com', 15000000),(9,'Surtimax', '800901234', 'Manizales', '3109999999', 'ventas@surtimax.com', 18000000),(10,'Colsubsidio', '801012345', 'Bogotá', '3110000000', 'compras@colsubsidio.com', 22000000);
INSERT INTO Proveedores VALUES (1,'Agroinsumos S.A.', '900123456', 'Medellín', '3001234567', 'ventas@agroinsumos.com', 'Fertilizantes', '30 días'),(2,'Semillas del Valle', '900234567', 'Cali', '3012345678', 'info@semillasvalle.com', 'Semillas', 'Contado'),(3,'Agroquímicos Andinos', '900345678', 'Bogotá', '3023456789', 'contacto@agroandinos.com', 'Plaguicidas', '15 días'),(4,'NutriCampo Ltda.', '900456789', 'Manizales', '3034567890', 'ventas@nutricampo.com', 'Fertilizantes', '30 días'),(5,'AgroRiego SAS', '900567890', 'Pereira', '3045678901', 'soporte@agroriego.com', 'Sistemas de riego', 'Contado'),(6,'CampoFértil S.A.', '900678901', 'Medellín', '3056789012', 'ventas@campofertil.com', 'Semillas', '30 días'),(7,'AgroMaquinaria Ltda.', '900789012', 'Bogotá', '3067890123', 'info@agromaquinaria.com', 'Maquinaria', '60 días'),(8,'BioAgro SAS', '900890123', 'Cali', '3078901234', 'bio@bioagro.com', 'Biofertilizantes', 'Contado'),(9,'AgroProtec S.A.', '900901234', 'Barranquilla', '3089012345', 'ventas@agroprotec.com', 'Plaguicidas', '30 días'),(10,'AgroAndes Ltda.', '901012345', 'Tunja', '3090123456', 'contacto@agroandes.com', 'Fertilizantes', '15 días');
INSERT INTO Ventas VALUES (1,'2023-07-05',1,1,250000, 120000, 1500000),(2,'2023-06-20',2,2,30000, 80000, 1200000),(3,'2023-08-25',3,3,40000, 100000, 1800000),(4,'2023-08-05',4,2,60000, 150000, 2500000),(5,'2023-08-15',6,5,20000, 70000, 1100000),(6,'2023-10-20',1,2,50000, 90000, 1600000),(7,'2023-10-05',10,3,30000, 80000, 1400000),(8,'2023-10-15',8,2,25000, 60000, 1000000),(9,'2023-10-25',5,1,40000, 100000, 1700000),(10,'2023-11-05',3,2,35000, 95000, 1900000);
INSERT INTO Detalles_Venta VALUES (1,1,1,1000,5),(2,2,2,500,6),(3,3,3,2000,4),(4,4,4,5000,2),(5,5,5,100,45),(6,6,6,2000,3),(7,7,10,1000,3),(8,8,8,1200,4),(9,9,9,300,5),(10,10,7,1500,6);
INSERT INTO Analisis_Suelo VALUES (1,1, '2025-02-01', 6.5, 45, 20, 30, 3.2, 1.5, 'AgroLab Medellín', 'Aplicar fertilizante nitrogenado'),(2,2,'2025-02-05', 5.8, 30, 15, 25, 2.8, 1.2, 'AgroLab Cali', 'Corregir acidez con cal agrícola'),(3,3,'2025-02-10', 6.2, 40, 18, 28, 3.0, 1.4, 'AgroLab Bogotá', 'Aplicar fósforo'),(4,4,'2025-02-15', 6.8, 50, 22, 35, 3.5, 1.6, 'AgroLab Manizales', 'Buen balance, mantener manejo'),(5,5,'2025-02-20', 5.5, 25, 12, 20, 2.5, 1.1, 'AgroLab Pereira', 'Aumentar materia orgánica'),(6,6,'2025-02-25', 6.0, 35, 17, 27, 2.9, 1.3, 'AgroLab Tunja', 'Aplicar potasio'),(7,7,'2025-03-01', 6.4, 42, 19, 29, 3.1, 1.5, 'AgroLab Medellín', 'Mantener fertilización balanceada'),(8,8,'2025-03-05', 5.9, 28, 14, 22, 2.7, 1.2, 'AgroLab Cali', 'Aplicar cal y compost'),(9,9,'2025-03-10', 6.6, 48, 21, 33, 3.4, 1.6, 'AgroLab Bogotá', 'Condiciones óptimas'),(10,10,'2025-03-15', 6.1, 36, 16, 26, 3.0, 1.4, 'AgroLab Manizales', 'Reforzar nitrógeno y fósforo');
ALTER TABLE Siembras ADD COLUMN Estado VARCHAR(30) DEFAULT 'En Crecimiento';
ALTER TABLE Insumos ADD COLUMN Alerta VARCHAR(50) DEFAULT 'Normal';
ALTER TABLE Empleados ADD COLUMN FechaFinContrato DATE;
ALTER TABLE Empleados ADD COLUMN AlertaContrato VARCHAR(50) DEFAULT 'Vigente';





-- 1. FN_CalcularDiasHastaCosecha: Calcula los días restantes hasta la fecha estimada de cosecha.

DELIMITER //

CREATE FUNCTION FN_CalcularDiasHastaCosecha(p_id_siembra INT) 
RETURNS INT
DETERMINISTIC
BEGIN
    DECLARE v_fecha_est DATE;
    DECLARE v_dias_restantes INT;

    
    SELECT FechaEstCosecha INTO v_fecha_est 
    FROM Siembras 
    WHERE SiembraID = p_id_siembra;

    
    SET v_dias_restantes = DATEDIFF(v_fecha_est, CURDATE());

    
    IF v_dias_restantes < 0 THEN
        SET v_dias_restantes = 0;
    END IF;

    RETURN v_dias_restantes;
END //

DELIMITER ;



-- 2. FN_ObtenerDisponibilidadParcela: Determina si una parcela está disponible para una nueva siembra.


DELIMITER //

CREATE FUNCTION FN_ObtenerDisponibilidadParcela(p_id_parcela INT) 
RETURNS VARCHAR(20)
DETERMINISTIC
BEGIN
    DECLARE v_conteo INT;
    DECLARE v_resultado VARCHAR(20);

    -- Contamos si hay siembras activas en esa parcela
    SELECT COUNT(*) INTO v_conteo
    FROM Siembras
    WHERE ParcelaID = p_id_parcela 
      AND Estado IN ('En Crecimiento', 'Listo para Cosecha');

    -- Si el conteo es mayor a 0, la parcela está ocupada
    IF v_conteo > 0 THEN
        SET v_resultado = 'OCUPADA';
    ELSE
        SET v_resultado = 'DISPONIBLE';
    END IF;

    RETURN v_resultado;
END //

DELIMITER ;



-- 3. FN_CalcularRendimientoEstimado: Estima el rendimiento de un cultivo en una parcela específica basado en históricos.


DELIMITER //

CREATE FUNCTION FN_CalcularRendimientoEstimado(p_id_parcela INT, p_id_cultivo INT) 
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE v_promedio_historico DECIMAL(10,2);

    
    SELECT AVG(c.cantidad) INTO v_promedio_historico
    FROM Cosechas c
    JOIN Siembras s ON c.id_siembra = s.SiembraID
    WHERE s.ParcelaID = p_id_parcela 
      AND s.CultivoID = p_id_cultivo
      AND c.cantidad > 0;

   
    IF v_promedio_historico IS NULL THEN
        SELECT AVG(cantidad) INTO v_promedio_historico
        FROM Cosechas c
        JOIN Siembras s ON c.id_siembra = s.SiembraID
        WHERE s.CultivoID = p_id_cultivo
          AND c.cantidad > 0;
    END IF;

    
    RETURN IFNULL(v_promedio_historico, 0.00);
END //




-- 4. FN_ObtenerConsumoCombustibleMaquinaria: Calcula el consumo de combustible por máquina y periodo.

DELIMITER //

CREATE FUNCTION FN_ObtenerConsumoCombustibleMaquinaria(
    p_maquinaria_id INT, 
    p_fecha_inicio DATE, 
    p_fecha_fin DATE
) 
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE v_total_combustible DECIMAL(10,2);

    
    
    SELECT SUM(m.cantidad) INTO v_total_combustible
    FROM Movimientos_Insumos m
    JOIN Insumos i ON m.id_insumo = i.id_insumo
    WHERE i.id_tipo_insumo = 6 
      AND m.Destino LIKE CONCAT('%ID Maquina: ', p_maquinaria_id, '%') 
      AND m.fecha BETWEEN p_fecha_inicio AND p_fecha_fin;

    RETURN IFNULL(v_total_combustible, 0.00);
END //

DELIMITER ;



--  5. FN_CalcularRentabilidadCultivo: Calcula la rentabilidad de un cultivo comparando costos vs ingresos por venta.

DELIMITER //

CREATE FUNCTION FN_CalcularRentabilidadCultivo(p_id_cultivo INT) 
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE v_ingresos DECIMAL(10,2);
    DECLARE v_costos DECIMAL(10,2);
    DECLARE v_rentabilidad DECIMAL(10,2);

    
    SELECT SUM(dv.cantidad * dv.precio) INTO v_ingresos
    FROM Detalles_Venta dv
    JOIN Cosechas c ON dv.id_cosecha = c.id_cosecha
    JOIN Siembras s ON c.id_siembra = s.SiembraID
    WHERE s.CultivoID = p_id_cultivo;

    
    
    SELECT SUM(i.precio * s.DensidadSiembra) INTO v_costos
    FROM Siembras s
    JOIN Cultivos cu ON s.CultivoID = cu.id_cultivo
    JOIN Insumos i ON i.nombre = cu.nombre_comun 
    WHERE s.CultivoID = p_id_cultivo;

    
    SET v_rentabilidad = IFNULL(v_ingresos, 0) - IFNULL(v_costos, 0);

    RETURN v_rentabilidad;
END //

DELIMITER ;
 
-- 1. TR_ActualizarStockInsumos: Actualiza automáticamente el stock de insumos después de un movimiento (entrada o salida).

DELIMITER //

CREATE TRIGGER TR_ActualizarStockInsumos
AFTER INSERT ON Movimientos_Insumos
FOR EACH ROW
BEGIN
    
    IF NEW.tipo = 'Entrada' THEN
        UPDATE Insumos 
        SET stock = stock + NEW.cantidad
        WHERE id_insumo = NEW.id_insumo;
        
   
    ELSEIF NEW.tipo = 'Salida' THEN
        UPDATE Insumos 
        SET stock = stock - NEW.cantidad
        WHERE id_insumo = NEW.id_insumo;
    END IF;
END //

DELIMITER ;

-- 2. TR_VerificarHoroMetroMaquinaria: Valida que el nuevo valor del horómetro de una máquina sea mayor al anterior.

CREATE TRIGGER TR_VerificarHoroMetroMaquinaria
BEFORE UPDATE ON Maquinaria
FOR EACH ROW
BEGIN
    IF NEW.horometro <= OLD.horometro THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = 'Error: El nuevo valor del horómetro debe ser mayor al anterior.';
    END IF;
END;

-- 3. TR_RegistrarHistorialParcela: Actualiza el historial de uso de una parcela cuando se registra una nueva siembra.

CREATE TRIGGER TR_RegistrarHistorialParcela
AFTER INSERT ON Siembras
FOR EACH ROW
BEGIN
    INSERT INTO HistorialParcela (id_parcela, fecha_evento, descripcion)
    VALUES (
        NEW.id_parcela, 
        NOW(), 
        CONCAT('Nueva siembra registrada: ', NEW.cultivo, ' - Variedad: ', NEW.variedad)
    );
END;

-- 4. TR_AlertaStockMinimo: Genera una alerta cuando el stock de un insumo cae por debajo del mínimo establecido.

CREATE TRIGGER TR_AlertaStockMinimo
AFTER UPDATE ON Insumos
FOR EACH ROW
BEGIN
    
    IF NEW.stock_actual < NEW.stock_minimo THEN
        INSERT INTO Alertas (id_insumo, mensaje, fecha_alerta)
        VALUES (
            NEW.id_insumo, 
            CONCAT('Alerta: El insumo ', NEW.nombre, ' está por debajo del mínimo (', NEW.stock_minimo, ')'), 
            NOW()
        );
    END IF;
END;

-- 5. TR_ActualizarEstadoParcelaPorCosecha: Actualiza el estado de una parcela cuando se completa una cosecha.

CREATE TRIGGER TR_ActualizarEstadoParcelaPorCosecha
AFTER INSERT ON Cosechas
FOR EACH ROW
BEGIN
    UPDATE Parcelas
    SET estado = 'Disponible'
    WHERE id_parcela = NEW.id_parcela;
END;
