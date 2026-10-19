<?php
// 1. Подключаем базу данных
require_once 'BD.php';

// 2. Получаем данные из БД

// Получаем категории для выпадающего списка
$cat_result = mysqli_query($conn, "SELECT id, name FROM shop_categories ORDER BY name ASC");
$categories = mysqli_fetch_all($cat_result, MYSQLI_ASSOC);

// Получаем бренды для выпадающего списка
$brand_result = mysqli_query($conn, "SELECT id, name FROM shop_brands ORDER BY name ASC");
$brands = mysqli_fetch_all($brand_result, MYSQLI_ASSOC);

// Получаем список товаров со всеми связями
$sql_products = "
    SELECT 
        p.id, 
        p.name, 
        p.price, 
        p.stock, 
        p.rating, 
        c.name AS category_name, 
        b.name AS brand_name,
        pi.image AS product_image
    FROM shop_products p
    LEFT JOIN shop_categories c ON p.category_id = c.id
    LEFT JOIN shop_brands b ON p.brand_id = b.id
    LEFT JOIN product_images pi ON p.id = pi.product_id AND pi.is_main = 1
    ORDER BY p.id DESC
";

$res_products = mysqli_query($conn, $sql_products);

if (!$res_products) {
    die("Ошибка запроса: " . mysqli_error($conn));
}

$products = mysqli_fetch_all($res_products, MYSQLI_ASSOC);
?>
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Управление товарами</title>
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <!-- Шапка страницы -->
    <div class="page-title">
        <h2>Товары</h2>
        <p>Управление товарами интернет-магазина.</p>
    </div>

    <!-- Кнопки действий -->
    <section class="quick-actions">
        <button class="btn btn-blue">+ Добавить товар</button>
        <button class="btn btn-green">📥 Импорт</button>
        <button class="btn btn-orange">📤 Экспорт</button>
    </section>

    <!-- Блок фильтрации (Поиск) -->
    <section class="panel">
        <form method="GET" action="products.php" class="filter-form">
            <div class="form-group">
                <label>Поиск</label>
                <input type="text" name="search" placeholder="Название товара">
            </div>
            <div class="form-group">
                <label>Категория</label>
                <select name="category">
                    <option value="">Все категории</option>
                    <?php foreach ($categories as $cat): ?>
                        <option value="<?= $cat['id'] ?>"><?= htmlspecialchars($cat['name']) ?></option>
                    <?php endforeach; ?>
                </select>
            </div>
            <div class="form-group">
                <label>Бренд</label>
                <select name="brand">
                    <option value="">Все бренды</option>
                    <?php foreach ($brands as $brand): ?>
                        <option value="<?= $brand['id'] ?>"><?= htmlspecialchars($brand['name']) ?></option>
                    <?php endforeach; ?>
                </select>
            </div>
            <div class="form-group">
                <button type="submit" class="btn btn-blue">Найти</button>
            </div>
        </form>
    </section>

    <!-- Таблица товаров -->
    <section class="panel">
        <div class="panel-header">
            <h2>Список товаров</h2>
        </div>
        <table class="table">
            <thead>
                <tr>
                    <th>Фото</th>
                    <th>Название</th>
                    <th>Категория</th>
                    <th>Бренд</th>
                    <th>Цена</th>
                    <th>Рейтинг</th>
                    <th>Остаток</th>
                    <th>Действия</th>
                </tr>
            </thead>
            <tbody>
                <?php if (empty($products)): ?>
                    <tr>
                        <td colspan="8" class="text-center">Товары не найдены.</td>
                    </tr>
                <?php else: ?>
                    <?php foreach ($products as $product): ?>
                        <tr>
                            <td>
                                <?php if (!empty($product['product_image'])): ?>
                                    <img src="../img/products/<?= htmlspecialchars($product['product_image']) ?>" alt="Фото" class="table-img">
                                <?php else: ?>
                                    <div class="img-placeholder">📦</div>
                                <?php endif; ?>
                            </td>
                            <td><?= htmlspecialchars($product['name']) ?></td>
                            <td><?= htmlspecialchars($product['category_name'] ?? '—') ?></td>
                            <td><?= htmlspecialchars($product['brand_name'] ?? '—') ?></td>
                            <td><?= number_format($product['price'], 0, '', ' ') ?> ₽</td>
                            <td>
                                <span class="rating-star">★</span> <?= htmlspecialchars($product['rating'] ?? '0.0') ?>
                            </td>
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

    <!-- Пагинация -->
    <div class="pagination">
        <button class="page-btn">◀</button>
        <button class="page-btn active">1</button>
        <button class="page-btn">2</button>
        <button class="page-btn">3</button>
        <button class="page-btn">▶</button>
    </div>

</body>
</html>
