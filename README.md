Task 1

```sql
-- 1. Створення таблиці авторів
CREATE TABLE authors (
    author_id INT AUTO_INCREMENT PRIMARY KEY,
    author_name VARCHAR(255) NOT NULL
);

-- 2. Створення таблиці жанрів
CREATE TABLE genres (
    genre_id INT AUTO_INCREMENT PRIMARY KEY,
    genre_name VARCHAR(100) NOT NULL
);

-- 3. Створення таблиці користувачів
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(150) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL
);

-- 4. Створення таблиці книг (з іноземними ключами)
CREATE TABLE books (
    book_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    publication_year YEAR,
    author_id INT,
    genre_id INT,
    FOREIGN KEY (author_id) REFERENCES authors(author_id) ON DELETE SET NULL,
    FOREIGN KEY (genre_id) REFERENCES genres(genre_id) ON DELETE SET NULL
);

-- 5. Створення таблиці орендованих книг
CREATE TABLE borrowed_books (
    borrow_id INT AUTO_INCREMENT PRIMARY KEY,
    book_id INT,
    user_id INT,
    borrow_date DATE NOT NULL,
    return_date DATE,
    FOREIGN KEY (book_id) REFERENCES books(book_id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
);
```

Task 2
```sql 
-- Додаємо авторів
INSERT INTO authors (author_name) 
VALUES ('Сергій Жадан'), ('Джоан Роулінг');

-- Додаємо жанри
INSERT INTO genres (genre_name) 
VALUES ('Сучасна проза'), ('Фентезі');

-- Додаємо користувачів
INSERT INTO users (username, email) 
VALUES ('andriy_k', 'andriy@email.com'), ('olena_read', 'olena@email.com');

-- Додаємо книги (прив'язуємо до id авторів та жанрів)
INSERT INTO books (title, publication_year, author_id, genre_id) 
VALUES ('Інтернат', 2017, 1, 1), 
       ('Гаррі Поттер і філософський камінь', 1997, 2, 2);

-- Додаємо записи про оренду книг
INSERT INTO borrowed_books (book_id, user_id, borrow_date, return_date) 
VALUES (1, 1, '2023-10-01', '2023-10-15'), 
       (2, 2, '2023-11-20', NULL);
```


Task 3
```sql 
SELECT *
FROM order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id
INNER JOIN categories ON products.category_id = categories.id;

```

Task 4

```sql 
SELECT COUNT(*)
FROM order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id
INNER JOIN categories ON products.category_id = categories.id;

```

Зміна INNER на LEFT/RIGHT (Пояснення)
Що відбувається: Кількість рядків може збільшитися або залишитися незмінною.  
Чому: INNER JOIN повертає лише ті рядки, які мають збіги в обох таблицях. Якщо для якогось замовлення не вказано, то цей рядок зникне з результату.
LEFT JOIN - залишить усі рядки з лівої таблиці, навіть якщо в правій немає відповідного ключа. Замість даних будуть NULL.
У цій базі даних кількість рядків зростає, якщо є "сирі" дані, які INNER JOIN просто відсікав.
В моїч кейсах 2 LEFT JOIN і 1 RIGHT JOIN дали той самий результат що і вихідний запит.

Підсумковий складний запит (об'єднала всі умови в 4.3-4.7 в 1 запит)
```sql 
SELECT categories.name AS category_name, COUNT(*) AS total_rows, AVG(order_details.quantity) AS average_quantity
FROM order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id
INNER JOIN categories ON products.category_id = categories.id
WHERE orders.employee_id > 3 AND orders.employee_id <= 10
GROUP BY categories.name
HAVING AVG(order_details.quantity) > 21
ORDER BY total_rows DESC
LIMIT 4 OFFSET 1;
```