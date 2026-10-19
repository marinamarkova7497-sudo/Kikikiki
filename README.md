
$res_last_products = mysqli_query($conn, $sql_products);

if ($res_last_products === false) {
    die("Ошибка SQL-запроса: " . mysqli_error($conn));
}

$last_products = mysqli_fetch_all($res_last_products, MYSQLI_ASSOC);
