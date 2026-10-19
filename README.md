<?php
// 1. Подключаем базу данных
require_once 'BD.php'; // Убедитесь, что файл называется именно так

// ==========================================
// 2. ПОЛУЧАЕМ ДАННЫЕ ДЛЯ КАРТОЧЕК (ВЕРХНЯЯ ЧАСТЬ)
// ==========================================

// Считаем общее количество товаров
$res_products = mysqli_query($conn, "SELECT COUNT(id) as count FROM products"); // Замените products, если таблица называется иначе
$count_products = mysqli_fetch_assoc($res_products)['count'] ?? 0;

// Считаем общее количество категорий
$res_cats = mysqli_query($conn, "SELECT COUNT(id) as count FROM categories"); // Замените categories, если нужно
$count_cats = mysqli_fetch_assoc($res_cats)['count'] ?? 0;

// ЗАГЛУШКИ для заказов и пользователей, так как их таблиц нет на схеме
// Если у вас есть таблица orders, замените запрос на: SELECT COUNT(id) as count FROM orders
$count_orders = 0; 
// Если у вас есть таблица users, замените запрос на: SELECT COUNT(id) as count FROM users
$count_users = 0; 


// ==========================================
// 3. ПОЛУЧАЕМ ПОСЛЕДНИЕ 3 ТОВАРА
// ==========================================
// Используем JOIN, чтобы получить название категории по category_id
$sql_products = "
    SELECT p.id, p.name, p.price, p.stock, p.image, c.name AS category_name 
    FROM products p 
    LEFT JOIN categories c ON p.category_id = c.id 
    ORDER BY p.id DESC 
    LIMIT 3
";
$res_last_products = mysqli_query($conn, $sql_products);
$last_products = mysqli_fetch_all($res_last_products, MYSQLI_ASSOC);

// ==========================================
// 4. ПОЛУЧАЕМ ПОСЛЕДНИЕ ЗАКАЗЫ (Заглушка)
// ==========================================
// Если у вас есть таблица orders, раскомментируйте и настройте этот блок:
/*
$sql_orders = "
    SELECT id, user_name, created_at, total_price, status 
    FROM orders 
    ORDER BY id DESC 
    LIMIT 3
";
$res_last_orders = mysqli_query($conn, $sql_orders);
$last_orders = mysqli_fetch_all($res_last_orders, MYSQLI_ASSOC);
*/
$last_orders = []; // Пока оставляем пустым

?>
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TechStore Admin</title>
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <!-- Панель заголовка -->
    <div class="page-title">
        <h2>Главная панель</h2>
        <p>Добро пожаловать в систему управления интернет-магазином.</p>
    </div>

    <!-- Карточки со статистикой -->
    <section class="cards">
        <div class="card blue">
            <div class="card-info">
                <h3>Товаров</h3>
                <span><?= $count_products ?></span>
            </div>
            <div class="card-icon">📦</div>
        </div>
        <div class="card green">
            <div class="card-info">
                <h3>Категорий</h3>
                <span><?= $count_cats ?></span>
            </div>
            <div class="card-icon">📁</div>
        </div>
        <div class="card orange">
            <div class="card-info">
                <h3>Заказов</h3>
                <span><?= $count_orders ?></span>
            </div>
            <div class="card-icon">🛒</div>
        </div>
        <div class="card red">
            <div class="card-info">
                <h3>Пользователей</h3>
                <span><?= $count_users ?></span>
            </div>
            <div class="card-icon">👤</div>
        </div>
    </section>

    <!-- Кнопки быстрых действий -->
    <section class="quick-actions">
        <button class="btn btn-blue">+ Добавить товар</button>
        <button class="btn btn-green">+ Добавить категорию</button>
        <button class="btn btn-orange">+ Добавить бренд</button>
        <button class="btn btn-red">📋 Все заказы</button>
    </section>

    <!-- Таблица последних товаров -->
    <section class="panel">
        <div class="panel-header">
            <h2>Последние товары</h2>
        </div>
        <table class="table">
            <thead>
                <tr>
                    <th>Фото</th>
                    <th>Название</th>
                    <th>Категория</th>
                    <th>Цена</th>
                    <th>Остаток</th>
                    <th>Действия</th>
                </tr>
            </thead>
            <tbody>
                <?php if (empty($last_products)): ?>
                    <tr><td colspan="6">Товаров пока нет.</td></tr>
                <?php else: ?>
                    <?php foreach ($last_products as $product): ?>
                        <tr>
                            <td>
                                <!-- Если картинка есть в БД, выводим её. Путь настройте под себя -->
                                <?php if (!empty($product['image'])): ?>
                                    <img src="../img/products/<?= htmlspecialchars($product['image']) ?>" alt="Фото" width="50">
                                <?php else: ?>
                                    Нет фото
                                <?php endif; ?>
                            </td>
                            <td><?= htmlspecialchars($product['name']) ?></td>
                            <td><?= htmlspecialchars($product['category_name'] ?? 'Без категории') ?></td>
                            <!-- Форматируем цену с пробелами -->
                            <td><?= number_format($product['price'], 0, '', ' ') ?> ₽</td>
                            <td><?= (int)$product['stock'] ?></td>
                            <td>
                                <button class="btn-edit" data-id="<?= $product['id'] ?>">✏️</button>
                                <button class="btn-delete" data-id="<?= $product['id'] ?>">🗑️</button>
                            </td>
                        </tr>
                    <?php endforeach; ?>
                <?php endif; ?>
            </tbody>
        </table>
    </section>

    <!-- Таблица последних заказов -->
    <section class="panel">
        <div class="panel-header">
            <h2>Последние заказы</h2>
        </div>
        <table class="table">
            <thead>
                <tr>
                    <th>№</th>
                    <th>Покупатель</th>
                    <th>Дата</th>
                    <th>Сумма</th>
                    <th>Статус</th>
                </tr>
            </thead>
            <tbody>
                <?php if (empty($last_orders)): ?>
                    <!-- Заглушка, так как таблицы заказов пока нет -->
                    <tr>
                        <td colspan="5" style="text-align: center;">Таблица заказов не подключена или заказов нет.</td>
                    </tr>
                <?php else: ?>
                    <?php foreach ($last_orders as $order): ?>
                        <tr>
                            <td>#<?= $order['id'] ?></td>
                            <td><?= htmlspecialchars($order['user_name']) ?></td>
                            <td><?= date('d.m.Y', strtotime($order['created_at'])) ?></td>
                            <td><?= number_format($order['total_price'], 0, '', ' ') ?> ₽</td>
                            <td>
                                <!-- Пример вывода статуса (вам нужно будет настроить логику под ваши статусы) -->
                                <?php if ($order['status'] == 'paid'): ?>
                                    <span class="status status-green">Оплачен</span>
                                <?php else: ?>
                                    <span class="status status-orange">Ожидает</span>
                                <?php endif; ?>
                            </td>
                        </tr>
                    <?php endforeach; ?>
                <?php endif; ?>
            </tbody>
        </table>
    </section>
</body>
</html>
