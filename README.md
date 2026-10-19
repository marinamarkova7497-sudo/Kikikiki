<?php
// Подключаем базу данных
require_once 'bd.php';

try {
    // ВАЖНО: Замените `categories` на имя вашей таблицы.
    // А также убедитесь, что колонки title, description, icon, products_count существуют.
    $stmt = $pdo->query("SELECT * FROM categories ORDER BY id ASC");
    $categories = $stmt->fetchAll();
} catch (PDOException $e) {
    die("Ошибка при получении данных: " . $e->getMessage());
}
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
                            <!-- Иконка -->
                            <div class="category-icon">
                                <?= htmlspecialchars($cat['icon'] ?? '📦') ?>
                            </div>
                            
                            <!-- Название (если у вас колонка name, замените $cat['title'] на $cat['name']) -->
                            <h3><?= htmlspecialchars($cat['title'] ?? 'Без названия') ?></h3>
                            
                            <!-- Описание -->
                            <p><?= htmlspecialchars($cat['description'] ?? 'Описание отсутствует') ?></p>
                            
                            <div class="category-footer">
                                <!-- Количество товаров -->
                                <span><?= (int)($cat['products_count'] ?? 0) ?> товаров</span>
                                
                                <div>
                                    <!-- Передаем ID категории в data-атрибут, чтобы потом можно было удалить или редактировать -->
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
