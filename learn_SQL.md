create table customer(id serial primary key, name varchar(255), phone varchar(30), email varchar(255));

create table product (id serial primary key, name varchar(255), description text, price integer);

create table product_photo (id serial primary key, url varchar(255), product_id integer references product(id));

create table cart(customer_id integer references customer(id), id serial primary key);

create table cart_product(cart_id integer references cart(id), product_id integer references product(id));

insert into customer(name, phone, email) values ('Василий', '02', 'vac@gmail.com');
insert into customer(name, phone, email) values ('Пётр', '03', 'petr@gmail.com');

select * from customer;

insert into product(name, description, price) values ('iPhone', 'крутой телефон', 10000);
insert into product(name, description, price) values ('Apple watch', 'крутые часы', 50000);

delete from product where id = 5;
delete from product where id = 6;

insert into product_photo (url, product_id) values ('iphone_photo', 1);

select * from product_photo;
select * from product;

select pp.*, p.name from product_photo pp left join product p on p.id = pp.product_id;

select pp.*, p.name from product_photo pp right join product p on p.id = pp.product_id;

select pp.*, p.name from product_photo pp inner join product p on p.id = pp.product_id;

delete from product_photo where id = 1;

update product_photo set url='image_photo_2' where id=1;

--left join ??

--alter table product_photo drop constraint product_photo_product_id_fkey;

select * from product_photo;

select * from product;

insert into product_photo (url, product_id) values ('unknown_photo', 100);

select * from cart_product;

insert into cart (customer_id) values (1);

insert into cart_product (cart_id, product_id) VALUES (1, 1), (1, 2);

select * from cart_product;

select c.name, coalesce(sum(p.price), 0) as orders_sum from customer c left join cart on cart.customer_id=c.id
left join cart_product cp on cart.id = cart.id left join product p on p.id=cp.product_id
group by c.name having sum(p.price) > 0;

select * from customer order by name LIMIT 1 offset 1;

select * from product;
update product set price=100000 where id=1;
