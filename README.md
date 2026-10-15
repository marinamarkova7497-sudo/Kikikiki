<?php
// sidebar.php
// Проверяем, задана ли переменная $page. Если нет — ставим пустую строку,
// чтобы избежать ошибок PHP при выводе класса active.
$page = $page ?? ''; 
?>

<aside class="sidebar">
    <div class="logo">
        <span class="logo-icon">🛒</span>
        <span class="logo-text">TechStore</span>
        <span class="logo-admin">ADMIN</span>
    </div>
    
    <div class="admin-card">
        <img src="img/avatar.jpg" alt="Администратор">
        <div class="admin-info">
            <h3>Администратор</h3>
            <p>admin@techstore.ru</p>
        </div>
    </div>
    
    <nav>
        <div class="menu-title">ОСНОВНОЕ</div>
        <ul>
            <!-- Тернарный оператор внутри PHP выводит класс 'active', если переменная совпадает -->
            <li class="<?php echo ($page == 'dashboard') ? 'active' : ''; ?>">
                <a href="index.php"><span>📊</span>Панель управления</a>
            </li>
            <li class="<?php echo ($page == 'products') ? 'active' : ''; ?>">
                <a href="products.php"><span>📦</span>Товары</a>
            </li>
            <li class="<?php echo ($page == 'categories') ? 'active' : ''; ?>">
                <a href="category.php"><span>📁</span>Категории</a>
            </li>
            <li class="<?php echo ($page == 'brands') ? 'active' : ''; ?>">
                <a href="#"><span>🏷️</span>Бренды</a>
            </li>
            <li class="<?php echo ($page == 'images') ? 'active' : ''; ?>">
                <a href="#"><span>🖼️</span>Изображения</a>
            </li>
        </ul>

        <div class="menu-title">ПРОДАЖИ</div>
        <ul>
            <li class="<?php echo ($page == 'orders') ? 'active' : ''; ?>">
                <a href="#"><span>🛒</span>Заказы</a>
            </li>
            <li class="<?php echo ($page == 'users') ? 'active' : ''; ?>">
                <a href="#"><span>👤</span>Пользователи</a>
            </li>
            <li class="<?php echo ($page == 'reviews') ? 'active' : ''; ?>">
                <a href="#"><span>⭐</span>Отзывы</a>
            </li>
        </ul>

        <div class="menu-title">СИСТЕМА</div>
        <ul>
            <li class="<?php echo ($page == 'settings') ? 'active' : ''; ?>">
                <a href="#"><span>⚙️</span>Настройки</a>
            </li>
            <li class="<?php echo ($page == 'backup') ? 'active' : ''; ?>">
                <a href="#"><span>💾</span>Резервное копирование</a>
            </li>
            <li>
                <a href="#"><span>🚪</span>Выход</a>
            </li>
        </ul>
    </nav>
</aside>
