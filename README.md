# DML та DDL команди. Складні SQL вирази

### Завдання 1

Створіть базу даних для керування бібліотекою книг згідно зі структурою,
наведеною нижче. Використовуйте DDL-команди для створення необхідних таблиць
та їх зв'язків

```sql
CREATE SCHEMA LibraryManagement;

USE LibraryManagement;

CREATE TABLE authors (
	author_id INT AUTO_INCREMENT PRIMARY KEY,
    author_name VARCHAR(255)
);

CREATE TABLE genres (
	genre_id INT AUTO_INCREMENT PRIMARY KEY,
    genre_name VARCHAR(255)
);

CREATE TABLE books (
	book_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255),
    publication_year YEAR,
    author_id INT,
    genre_id INT,
    FOREIGN KEY (author_id) REFERENCES authors(author_id),
    FOREIGN KEY (genre_id) REFERENCES genres(genre_id)
);

CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255),
    email VARCHAR(255)
);

CREATE TABLE borrowed_books (
    borrow_id INT AUTO_INCREMENT PRIMARY KEY,
    book_id INT,
    user_id INT,
    borrow_date DATE,
    return_date DATE,
    FOREIGN KEY (book_id) REFERENCES books(book_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

### Завдання 2

Заповніть таблиці простими видуманими тестовими даними. Достатньо одного-двох рядків у кожну таблицю.

```sql
USE LibraryManagement;

INSERT INTO authors (author_name) VALUES
  ('George Orwell'),
  ('Jane Austen');

INSERT INTO genres (genre_name) VALUES
  ('Dystopian'),
  ('Romance');

INSERT INTO books (title, publication_year, author_id, genre_id) VALUES
  ('Animal Farm', 1944, 1, 1),
  ('Pride and Prejudice', 1913, 2, 2);

INSERT INTO users (username, email) VALUES
  ('john', 'john.doe@example.com'),
  ('jane', 'jane.smith@example.com');

INSERT INTO borrowed_books (book_id, user_id, borrow_date, return_date) VALUES
  (1, 1, '2024-09-01', '2024-11-14'),
  (2, 2, '2024-10-02', NULL);
```

### Завдання 3

Перейдіть до бази даних, з якою працювали у темі 3. Напишіть запит за допомогою операторів FROM та INNER JOIN, що об’єднує всі таблиці даних, які ми завантажили з файлів: order_details, orders, customers, products, categories, employees, shippers, suppliers. Для цього ви маєте знайти спільні ключі.

Перевірте правильність виконання запиту.

```sql
USE mydb;

SELECT
    o.id AS order_id,
    o.date AS order_date,
    c.name AS customer_name,
    c.contact AS customer_contact,
    c.city AS customer_city,
    c.country AS customer_country,
    e.first_name AS employee_first_name,
    e.last_name AS employee_last_name,
    s.name AS shipper_name,
    s.phone AS shipper_phone,
    p.name AS product_name,
    p.price AS unit_price,
    p.unit AS unit_type,
    od.quantity,
    cat.name AS category_name,
    cat.description AS category_description,
    sup.name AS supplier_name,
    sup.contact AS supplier_contact,
    sup.country AS supplier_country
FROM
    orders o
        INNER JOIN
    customers c ON o.customer_id = c.id
        INNER JOIN
    employees e ON o.employee_id = e.employee_id
        INNER JOIN
    shippers s ON o.shipper_id = s.id
        INNER JOIN
    order_details od ON o.id = od.order_id
        INNER JOIN
    products p ON od.product_id = p.id
        INNER JOIN
    categories cat ON p.category_id = cat.id
        INNER JOIN
    suppliers sup ON p.supplier_id = sup.id;
```

### Завдання 4

Виконайте запити, перелічені нижче.

#### 4.1 Визначте, скільки рядків ви отримали (за допомогою оператора COUNT).

```sql
SELECT
    COUNT(*) AS total_rows
FROM
    orders o
        INNER JOIN
    customers c ON o.customer_id = c.id
        INNER JOIN
    employees e ON o.employee_id = e.employee_id
        INNER JOIN
    shippers s ON o.shipper_id = s.id
        INNER JOIN
    order_details od ON o.id = od.order_id
        INNER JOIN
    products p ON od.product_id = p.id
        INNER JOIN
    categories cat ON p.category_id = cat.id
        INNER JOIN
    suppliers sup ON p.supplier_id = sup.id;
```

#### 4.2 Змініть декілька операторів INNER на LEFT чи RIGHT. Визначте, що відбувається з кількістю рядків. Чому? Напишіть відповідь у текстовому файлі.

```sql
SELECT
    COUNT(*) AS total_rows
FROM
    orders o
        LEFT JOIN
    customers c ON o.customer_id = c.id
        LEFT JOIN
    employees e ON o.employee_id = e.employee_id
        LEFT JOIN
    shippers s ON o.shipper_id = s.id
        INNER JOIN
    order_details od ON o.id = od.order_id
        RIGHT JOIN
    products p ON od.product_id = p.id
        RIGHT JOIN
    categories cat ON p.category_id = cat.id
        RIGHT JOIN
    suppliers sup ON p.supplier_id = sup.id;
```

#### Пояснення

Кількість рядків не змінилася (518 рядків), незважаючи на заміну кількох операторів `INNER JOIN` на `LEFT JOIN` або `RIGHT JOIN`. Це відбувається скорше за все через специфіку даних у таблицях.
Всі записи у з'єднуваних таблицях мають відповідності. Немає таких рядків, де відповідність відсутня, а значення можуть бути `NULL`.

- `INNER JOIN` повертає тільки ті рядки, які мають відповідності в обох таблицях.
- `LEFT JOIN` та `RIGHT JOIN` повертають всі рядки з правої таблиці та відповідні рядки з лівої таблиці. Якщо праву таблицю можна відобразити у відповідності з лівою, то результат буде таким самим, як і у `INNER JOIN`.

#### 4.3 Оберіть тільки ті рядки, де employee_id > 3 та ≤ 10.

```sql
SELECT
    o.id AS order_id,
    o.date AS order_date,
    c.name AS customer_name,
    c.contact AS customer_contact,
    c.city AS customer_city,
    c.country AS customer_country,
    e.first_name AS employee_first_name,
    e.last_name AS employee_last_name,
    s.name AS shipper_name,
    s.phone AS shipper_phone,
    p.name AS product_name,
    p.price AS unit_price,
    p.unit AS unit_type,
    od.quantity,
    cat.name AS category_name,
    cat.description AS category_description,
    sup.name AS supplier_name,
    sup.contact AS supplier_contact,
    sup.country AS supplier_country
FROM
    orders o
        INNER JOIN
    customers c ON o.customer_id = c.id
        INNER JOIN
    employees e ON o.employee_id = e.employee_id
        INNER JOIN
    shippers s ON o.shipper_id = s.id
        INNER JOIN
    order_details od ON o.id = od.order_id
        INNER JOIN
    products p ON od.product_id = p.id
        INNER JOIN
    categories cat ON p.category_id = cat.id
        INNER JOIN
    suppliers sup ON p.supplier_id = sup.id
WHERE
    e.employee_id > 3
        AND e.employee_id <= 10;
```

#### 4.4 Згрупуйте за іменем категорії, порахуйте кількість рядків у групі, середню кількість товару (кількість товару знаходиться в order_details.quantity)

```sql
SELECT
    cat.name,
    COUNT(*) AS count,
    AVG(od.quantity) AS average_quantity
FROM
    orders o
        INNER JOIN
    customers c ON o.customer_id = c.id
        INNER JOIN
    employees e ON o.employee_id = e.employee_id
        INNER JOIN
    shippers s ON o.shipper_id = s.id
        INNER JOIN
    order_details od ON o.id = od.order_id
        INNER JOIN
    products p ON od.product_id = p.id
        INNER JOIN
    categories cat ON p.category_id = cat.id
        INNER JOIN
    suppliers sup ON p.supplier_id = sup.id
GROUP BY cat.name;
```

#### 4.5 Відфільтруйте рядки, де середня кількість товару більша за 21.

```sql
SELECT
    cat.name,
    COUNT(*) AS count,
    AVG(od.quantity) AS average_quantity
FROM
    orders o
        INNER JOIN
    customers c ON o.customer_id = c.id
        INNER JOIN
    employees e ON o.employee_id = e.employee_id
        INNER JOIN
    shippers s ON o.shipper_id = s.id
        INNER JOIN
    order_details od ON o.id = od.order_id
        INNER JOIN
    products p ON od.product_id = p.id
        INNER JOIN
    categories cat ON p.category_id = cat.id
        INNER JOIN
    suppliers sup ON p.supplier_id = sup.id
GROUP BY cat.name
HAVING average_quantity > 21;
```

#### 4.6 Відсортуйте рядки за спаданням кількості рядків.

```sql
SELECT
    cat.name,
    COUNT(*) AS count,
    AVG(od.quantity) AS average_quantity
FROM
    orders o
        INNER JOIN
    customers c ON o.customer_id = c.id
        INNER JOIN
    employees e ON o.employee_id = e.employee_id
        INNER JOIN
    shippers s ON o.shipper_id = s.id
        INNER JOIN
    order_details od ON o.id = od.order_id
        INNER JOIN
    products p ON od.product_id = p.id
        INNER JOIN
    categories cat ON p.category_id = cat.id
        INNER JOIN
    suppliers sup ON p.supplier_id = sup.id
GROUP BY cat.name
HAVING average_quantity > 21
ORDER BY count DESC;
```

#### 4.7 Виведіть на екран (оберіть) чотири рядки з пропущеним першим рядком.

```sql
SELECT
    cat.name,
    COUNT(*) AS count,
    AVG(od.quantity) AS average_quantity
FROM
    orders o
        INNER JOIN
    customers c ON o.customer_id = c.id
        INNER JOIN
    employees e ON o.employee_id = e.employee_id
        INNER JOIN
    shippers s ON o.shipper_id = s.id
        INNER JOIN
    order_details od ON o.id = od.order_id
        INNER JOIN
    products p ON od.product_id = p.id
        INNER JOIN
    categories cat ON p.category_id = cat.id
        INNER JOIN
    suppliers sup ON p.supplier_id = sup.id
GROUP BY cat.name
HAVING average_quantity > 21
ORDER BY count DESC
LIMIT 4 OFFSET 1;
```
