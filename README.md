<?php
// 1. Подключаем базу данных
require_once 'BD.php'; // Убедитесь, что путь к файлу правильный

// 2. Получаем данные из БД

// Получаем категории для выпадающего списка
$cat_result = mysqli_query($conn, "SELECT id, name FROM shop_categories ORDER BY name ASC");
$categories = mysqli_fetch_all($cat_result, MYSQLI_ASSOC);

// Получаем бренды для выпадающего списка
$brand_result = mysqli_query($conn, "SELECT id, name FROM shop_brands ORDER BY name ASC");
$brands = mysqli_fetch_all($brand_result, MYSQLI_ASSOC);

// Получаем список товаров со всеми связями
// Используем LEFT JOIN, чтобы получить категории, бренды и главные картинки
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
    <section class="quick-actions" style="margin-bottom: 20px; display: flex; gap: 10px;">
        <button class="btn btn-blue">+ Добавить товар</button>
        <button class="btn btn-green">📥 Импорт</button>
        <button class="btn btn-orange">📤 Экспорт</button>
    </section>

    <!-- Блок фильтрации (Поиск) -->
    <section class="panel" style="margin-bottom: 20px; padding: 20px; background: #fff; border-radius: 8px;">
        <form method="GET" action="products.php" style="display: flex; gap: 15px; align-items: flex-end;">
            <div style="flex: 1;">
                <label style="display:block; margin-bottom: 5px;">Поиск</label>
                <input type="text" name="search" placeholder="Название товара" style="width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px;">
            </div>
            <div style="flex: 1;">
                <label style="display:block; margin-bottom: 5px;">Категория</label>
                <select name="category" style="width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px;">
                    <option value="">Все категории</option>
                    <?php foreach ($categories as $cat): ?>
                        <option value="<?= $cat['id'] ?>"><?= htmlspecialchars($cat['name']) ?></option>
                    <?php endforeach; ?>
                </select>
            </div>
            <div style="flex: 1;">
                <label style="display:block; margin-bottom: 5px;">Бренд</label>
                <select name="brand" style="width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px;">
                    <option value="">Все бренды</option>
                    <?php foreach ($brands as $brand): ?>
                        <option value="<?= $brand['id'] ?>"><?= htmlspecialchars($brand['name']) ?></option>
                    <?php endforeach; ?>
                </select>
            </div>
            <div>
                <button type="submit" class="btn btn-blue" style="padding: 9px 20px;">Найти</button>
            </div>
        </form>
    </section>

    <!-- Таблица товаров -->
    <section class="panel">
        <div class="panel-header">
            <h2>Список товаров</h2>
        </div>
        <table class="table" style="width: 100%; border-collapse: collapse;">
            <thead>
                <tr style="text-align: left; border-bottom: 2px solid #eee;">
                    <th style="padding: 10px;">Фото</th>
                    <th style="padding: 10px;">Название</th>
                    <th style="padding: 10px;">Категория</th>
                    <th style="padding: 10px;">Бренд</th>
                    <th style="padding: 10px;">Цена</th>
                    <th style="padding: 10px;">Рейтинг</th>
                    <th style="padding: 10px;">Остаток</th>
                    <th style="padding: 10px;">Действия</th>
                </tr>
            </thead>
            <tbody>
                <?php if (empty($products)): ?>
                    <tr><td colspan="8" style="text-align: center; padding: 20px;">Товары не найдены.</td></tr>
                <?php else: ?>
                    <?php foreach ($products as $product): ?>
                        <tr style="border-bottom: 1px solid #eee;">
                            <td style="padding: 10px;">
                                <?php if (!empty($product['product_image'])): ?>
                                    <!-- ВАЖНО: Проверьте правильность пути к папке с картинками -->
                                    <img src="../img/products/<?= htmlspecialchars($product['product_image']) ?>" alt="Фото" style="width: 50px; height: 50px; object-fit: cover; border-radius: 4px;">
                                <?php else: ?>
                                    <div style="width: 50px; height: 50px; background: #eee; display: flex; align-items: center; justify-content: center; border-radius: 4px;">📦</div>
                                <?php endif; ?>
                            </td>
                            <td style="padding: 10px;"><?= htmlspecialchars($product['name']) ?></td>
                            <td style="padding: 10px;"><?= htmlspecialchars($product['category_name'] ?? '—') ?></td>
                            <td style="padding: 10px;"><?= htmlspecialchars($product['brand_name'] ?? '—') ?></td>
                            <td style="padding: 10px;"><?= number_format($product['price'], 0, '', ' ') ?> ₽</td>
                            <td style="padding: 10px;">
                                <!-- Вывод рейтинга -->
                                <span style="color: #f39c12;">★</span> <?= htmlspecialchars($product['rating'] ?? '0.0') ?>
                            </td>
                            <td style="padding: 10px;"><?= (int)$product['stock'] ?></td>
                            <td style="padding: 10px;">
                                <!-- Кнопки действий -->
                                <button class="btn-edit" data-id="<?= $product['id'] ?>" style="background:#3498db; color:white; border:none; padding: 5px 10px; border-radius: 4px; cursor:pointer;">✏️</button>
                                <button class="btn-delete" data-id="<?= $product['id'] ?>" style="background:#e74c3c; color:white; border:none; padding: 5px 10px; border-radius: 4px; cursor:pointer;">🗑️</button>
                            </td>
                        </tr>
                    <?php endforeach; ?>
                <?php endif; ?>
            </tbody>
        </table>
    </section>

    <!-- Пагинация (Пока статичная для примера) -->
    <div style="display: flex; justify-content: center; margin-top: 20px; gap: 5px;">
        <button style="padding: 5px 10px; border: 1px solid #ccc; background: #fff;">◀</button>
        <button style="padding: 5px 10px; border: 1px solid #3498db; background: #3498db; color: #fff;">1</button>
        <button style="padding: 5px 10px; border: 1px solid #ccc; background: #fff;">2</button>
        <button style="padding: 5px 10px; border: 1px solid #ccc; background: #fff;">3</button>
        <button style="padding: 5px 10px; border: 1px solid #ccc; background: #fff;">▶</button>
    </div>

</body>
</html>
