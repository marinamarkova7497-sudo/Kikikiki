<?php
// Подключаем вашу базу данных
require_once 'BD.php';

// Делаем запрос к базе данных (используем $conn вместо $pdo)
$sql = "
    SELECT 
        c.id, 
        c.name, 
        c.description, 
        c.image,
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

// Получаем все данные в виде массива
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
                            <!-- Вывод картинки -->
                            <div class="category-icon">
                                <?php if (!empty($cat['image'])): ?>
                                    <!-- Если картинки лежат в папке, например uploads, допишите путь: src="uploads/<?= htmlspecialchars($cat['image']) ?>" -->
                                    <img src="<?= htmlspecialchars($cat['image']) ?>" alt="<?= htmlspecialchars($cat['name']) ?>" style="max-width: 100%; height: auto;">
                                <?php else: ?>
                                    📦 <!-- Заглушка, если нет картинки -->
                                <?php endif; ?>
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
