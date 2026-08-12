
Task II Schema

```sql
create table category(
category_id integer primary key auto_increment,
category_name varchar(100) not null unique,
description varchar(225)
);

create table product(
product_id int primary key auto_increment,
product_name varchar(100) not null,
category_id int not null,
price decimal(10,2) not null,
stock int not null,
brand varchar (25),
foreign key(category_id)
references category(category_id)
);

insert into category(category_name,description)
values
("Electronics","Electronic Products and Accessories"),
("Mobiles","Mobile Phones and Tablets"),
("Faashion","Clothing and Fashion Products"),
("Appliances","Home and Kitchen Appliances");

insert into product(product_name,category_id,price,stock,brand)
values
("iPhone 15",2,69999.00,25,"Apple"),
("Galaxy S24",2,64999.00,30,"Samsung"),
("Wirleless Headphones",1,2999.00,50,"Sony"),
("Smart TV 43 inch",1,32999.00,15,"LG"),
("Men Casual Shirt",3,399.00,100,"Roadster"),
("Washing Machine",4,28999.00,15,"Whirlpool");

update category 
set category_name="Fashion"
where category_id = 3

delete from product where product_id = 6;

select category_name, count(*) as total_product
from Category
join Product
on Category.category_id = Product.category_id
group by category_name;

select * from product
select * from category

```
