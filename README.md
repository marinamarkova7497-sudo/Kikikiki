<?php
// Подключаем базу данных
require_once 'BD.php';

// Делаем запрос к базе данных (считаем товары)
$sql = "
    SELECT 
        c.id, 
        c.name, 
        c.description, 
        COUNT(p.id) AS products_count
    FROM shop_categories c
    LEFT JOIN shop_products p ON c.id = p.category_id
    GROUP BY c.id
    ORDER BY c.id ASC
";

$result = mysqli_query($conn, $sql);

if (!$result) {
    die("Ошибка запроса: " . mysqli_error($conn));
}

$categories = mysqli_fetch_all($result, MYSQLI_ASSOC);
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
    <div class="main">
        <main class="content">
            <div class="page-title">
                <h2>Категории товаров</h2>
                <p>Управление категориями интернет-магазина.</p>
            </div>
            
            <section class="quick-actions">
                <button class="btn btn-blue">+ Добавить категорию</button>
            </section>
            
            <section class="categories">
                <?php if (empty($categories)): ?>
                    <p>В базе данных пока нет категорий.</p>
                <?php else: ?>
                    <?php foreach ($categories as $cat): ?>
                        <div class="category-card">
                            
                            <!-- ВЫВОД ИКОНКИ (ЭМОДЗИ) -->
                            <div class="category-icon">
                                <?php 
                                    // Подбираем иконку в зависимости от названия категории
                                    $catName = mb_strtolower($cat['name']); // Переводим в нижний регистр для удобства
                                    $icon = '📦'; // Иконка по умолчанию
                                    
                                    if (mb_strpos($catName, 'смартфон') !== false || mb_strpos($catName, 'телефон') !== false) {
                                        $icon = '📱';
                                    } elseif (mb_strpos($catName, 'ноутбук') !== false || mb_strpos($catName, 'компьютер') !== false) {
                                        $icon = '💻';
                                    } elseif (mb_strpos($catName, 'наушник') !== false || mb_strpos($catName, 'аудио') !== false) {
                                        $icon = '🎧';
                                    } elseif (mb_strpos($catName, 'приставк') !== false || mb_strpos($catName, 'игр') !== false) {
                                        $icon = '🎮';
                                    } elseif (mb_strpos($catName, 'телевизор') !== false || mb_strpos($catName, 'tv') !== false) {
                                        $icon = '📺';
                                    } elseif (mb_strpos($catName, 'час') !== false) {
                                        $icon = '⌚';
                                    } elseif (mb_strpos($catName, 'электроник') !== false) {
                                        $icon = '💻';
                                    } elseif (mb_strpos($catName, 'спорт') !== false) {
                                        $icon = '⚽';
                                    } elseif (mb_strpos($catName, 'быт') !== false) {
                                        $icon = '🏠';
                                    } elseif (mb_strpos($catName, 'мебел') !== false) {
                                        $icon = '🛋️';
                                    } elseif (mb_strpos($catName, 'одежд') !== false) {
                                        $icon = '👕';
                                    } elseif (mb_strpos($catName, 'книг') !== false) {
                                        $icon = '📚';
                                    }
                                    
                                    echo $icon;
                                ?>
                            </div>
                            
                            <!-- Название категории -->
                            <h3><?= htmlspecialchars($cat['name']) ?></h3>
                            
                            <!-- Описание -->
                            <p><?= htmlspecialchars($cat['description'] ?? 'Описание отсутствует') ?></p>
                            
                            <div class="category-footer">
                                <!-- Количество товаров -->
                                <span><?= (int)$cat['products_count'] ?> товаров</span>
                                
                                <div>
                                    <button class="btn-edit" data-id="<?= $cat['id'] ?>">✏️</button>
                                    <button class="btn-delete" data-id="<?= $cat['id'] ?>">🗑️</button>
                                </div>
                            </div>
                        </div>
                    <?php endforeach; ?>
                <?php endif; ?>
            </section>
        </main>
    </div>
</body>
</html>
