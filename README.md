- 👋 Hi, I’m @MiguelTambriz
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
MiguelTambriz/MiguelTambriz is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->

create database ExamenRecu

--Realice un punto de venta una factura:
--1.	Cree sus propias tablas
create table Cliente (
id_cliente int primary key,
Nombre Varchar (30),
Apellido Varchar (30),
Direccion Varchar (50), 
Telefono int,
Email Varchar (30),
Nit varchar(15)
);

create table Factura (
Num_Factura int primary key,
fecha date,
id_cliente int 
foreign key (id_cliente) references Cliente  (id_cliente)
);

create table Categoria (
id_categoria int primary key,
Nombre varchar (20),
Descripcion Varchar (20)
);

create table Producto (
id_producto int primary key,
Nombre Varchar (30),
precio decimal (5,2),
Existencia integer,
id_categoria int,
foreign key (id_categoria) references Categoria (id_categoria)
);

create table DetalleFactura (
idDetalle int primary key,
id_Factura int,
Cantidad int,
id_producto int,
Subtotal decimal (5,2),
foreign key (id_producto) references Producto (id_producto),
foreign key (id_Factura) references Factura (Num_Factura)
);

create table Venta(
id_venta int primary key,
id_Factura int,
TotalFactura decimal (9,2),
foreign key (id_Factura) references Factura (Num_factura) 
);

--2.	Cree un trigger para descontar existencias cuando se consuma un producto

Create TRIGGER desCantProductoExistencias
    ON DetalleFactura
    AFTER INSERT, UPDATE
    AS
    begin
        declare @id_producto smallint, @Cantidad smallint
        set @id_producto = (select id_producto from inserted);
        set @Cantidad = (select Cantidad from inserted);
        update Producto set Existencia = Existencia - @Cantidad where id_producto = @id_producto
        
    end
GO

insert into Factura values (1, GETDATE(), 1)

insert into DetalleFactura values (1, 1, 1, 1, 1)

select * from Producto

--3.	Cree un procedimiento almacenado para insertar eliminar y actualizar cada una de sus tablas
create procedure insertProducto 
@id_producto int, 
@Nombre varchar(30), 
@precio decimal(5,2), 
@id_categoria int,
@Existencia integer 
as
begin
insert into Producto values (@id_producto, @Nombre, @precio, @id_categoria, @Existencia)
end
go 

exec  insertProducto 1, 'Salchicha', 1.25, 1, 10

select *from Categoria

create procedure actualizarProducto
@id_producto int,
@Nombre varchar (30),
@precio decimal (5,2),
@Existencia integer 
as
begin 
update Producto set Nombre = @Nombre, precio=@precio, Existencia = @Existencia
where id_producto = @id_producto
end 
go
 
 actualizarProducto 1, 'Jamon', 3.50, 10

 select *from Producto

create procedure EliminarProducto
@id_producto int
as
begin 
Delete Producto 
where id_producto = @id_producto
end
go

EliminarProducto 1
 select *from Producto

create procedure insertCliente 
@id_cliente int,
@Nombre Varchar (30),
@Apellido Varchar (30),
@Direccion Varchar (50),
@Telefono int,
@Email Varchar (30),
@Nit Varchar(15)
as
begin
insert into Cliente values (@id_cliente, @Nombre, @Apellido, @Direccion, @Telefono, @Email, @Nit)
end
go 

exec insertCliente 6, 'Miguel', 'Tambriz', 'Solola', '56425206', 'corne_ko@hotmail.com', '343543-3'

create procedure actualizarCliente
@id_cliente int,
@Nombre varchar (30),
@Telefono int
as
begin 
update Cliente set Nombre = @Nombre, Telefono = @Telefono
where id_cliente = @id_cliente
end 
go

actualizarCliente 1, 'Juan', '45765768'

create procedure EliminarClienete
@id_cliente int
as
begin 
Delete Cliente 
where id_cliente = @id_cliente
end
go

create procedure insertCategoria 
@id_categoria int,
@Nombre Varchar (20),
@Descripcion Varchar (20)
as
begin
insert into Categoria values (@id_categoria, @Nombre, @Descripcion) 
end
go 

insertCategoria 6, 'sucaritas', 'sereales' 

create procedure actualizarCategoria 
@id_categoria int,
@Nombre varchar (20),
@Descripcion varchar (20)
as
begin 
update Categoria set Nombre = @Nombre, Descripcion = @Descripcion 
where id_categoria = @id_categoria
end 
go

insertCategoria 1, 'leche', 'consumo diario'

create procedure EliminarCategoria 
@id_categoria int
as
begin 
Delete Categoria 
where id_categoria = @id_categoria
end
go

create  procedure InsertDetalleFactura
@id_Factura int,
@Cantidad int,
@id_Producto int
as
begin

insert into DetalleFactura values ((select MAX(idDetalle)+1 from DetalleFactura),  @id_Factura, @Cantidad, @id_Producto,
									(select precio*@Cantidad from Producto where id_producto = @id_Producto)) 
end
go 

exec InsertDetalleFactura 1, 10, 2
select *from DetalleFactura

select *from Producto

create  procedure actualizarDetalleFactura
@id_Factura int,
@Cantidad int,
@id_Producto int
as
begin
update DetalleFactura set id_Factura = @id_Factura, Cantidad = @Cantidad, id_Producto = @id_Producto
where id_Factura = @id_Factura
end
go 

exec actualizarDetalleFactura 2, 4, 3
select *from DetalleFactura

create procedure EliminarDetalleFactura
@idDetalle int
as
begin 
Delete DetalleFactura
where idDetalle = @idDetalle
end
go

exec EliminarDetalleFactura 1 
select *from DetalleFactura 

alter  procedure insertFactura
@Num_Factura int,
@id_cliente int
as
begin
insert into Factura values (@Num_Factura, GETDATE(), @id_cliente) 
end
go 

exec insertFactura 7, 1 
select *from Factura

Alter procedure actualizarFactura
@Num_factura int,
@id_cliente int
as
begin 
update Factura set Num_Factura = @Num_factura, id_cliente = @id_cliente
where Num_Factura = @Num_factura
end 
go

exec actualizarFactura 1, 3
select *from Factura

create procedure EliminarFactura
@id_cliente int
as
begin 
Delete Factura
where id_cliente = @id_cliente
end
go

exec EliminarFactura 1 
select *from Factura

--4.	Cree un cursor que visualice la factura completa encabezado detalle y todo lo que se consumio en una factura
Alter procedure factura_completa @idFactura smallint
as
    begin
        declare @factura int, @fecha date, @nombreC varchar(50),
            @apellido varchar(50), @nit varchar(10)
        select @factura = a.Num_Factura, @fecha = a.fecha,
               @nombreC =  C.Nombre, @apellido = C.Apellido, @nit = C.Nit
        from Factura a inner join Cliente C on C.id_cliente = a.id_cliente
        where Num_Factura = @idFactura;

        print('---------------------------------------------------------')
        print(concat('Factura: ',@idFactura,space(10),'Fecha: ',@fecha))
        print(concat('Cliente: ',@nombreC,' ',@apellido,space(10),'nit: ',@nit,space(10)))
        declare @cantidad integer, @subtotal decimal(9,2),
            @nombre varchar(50), @precio decimal(9,2), @total decimal(9,2)
        declare cFactura cursor for
        select cantidad,Subtotal, P.Nombre, P.precio from DetalleFactura
        inner join Producto P on P.id_producto = DetalleFactura.id_producto
        where id_Factura = @idFactura

	print('---------------------------------------------------------')
        open cFactura
        fetch next from cFactura into @cantidad, @subtotal, @nombre, @precio
        print(concat('Cantidad',space(10),'Producto',space(10),'Precio unitario',space(10),'Subtotal'))
        set @total= 0
        while @@FETCH_STATUS = 0
        begin
            set @total = @total + @subtotal;
            print (concat(@cantidad,space(20),@nombre,space(20),@precio,space(20),@subtotal))
            fetch next from cFactura into @cantidad, @subtotal, @nombre, @precio
        end
        print(concat('Total: ',@total))
        close cFactura
        deallocate cFactura
    end

	exec factura_completa 1

--	5.	Realice un cursor que muestre como resultado los clientes y los productos que mas se venden en dos columnas






