# Obrigado.PHP
Criação da pagina de agradecimento.

<?php
// Inclui o script de segurança que verifica se o usuário está autenticado //
include('segurancaum.php');

// Inclui o cabeçalho da página (HTML padrão, menus, etc.) //
include('cabecalho.php');

// Inclui o arquivo de conexão com o banco de dados //
include('conn.php');

// Verifica se existe uma sessão de carrinho ativa; caso não exista, redireciona para a página inicial //
session_start(); // Inicia a sessão para acessar as variáveis de sessão //
if(!isset($_SESSION['carrinho'])) {
    header('Location: index.php'); // Redireciona para a página inicial //
    exit(); // Encerra o script para evitar execução adicional //
}

// Verifica se o método de pagamento foi enviado via POST; se não, redireciona para a página inicial //
if(!isset($_POST['pagamento'])) {
    header('Location: index.php'); // Redireciona para a página inicial //
    exit(); // Encerra o script //
}

//Pegar os dados do usuário//
$tp_pagamento = $_POST['pagamento'];
//Retirar os produtos do estoque//
// Consulta os itens do carrinho atual, identificando o ID do produto e a quantidade de cada item //
$sql = "SELECT id_produto_carrinho, quantidade_carrinho FROM tb_carrinhos WHERE numero_carrinho = '{$_SESSION['carrinho']}'";
$result = mysqli_query($link, $sql);

// Loop para percorrer cada item do carrinho //
while($tbl = mysqli_fetch_array($result)) {
    // Armazena o ID do produto e a quantidade comprada em variáveis //
    $id_produto = $tbl[0];
    $quantidade = $tbl[1];

    // Atualiza o estoque do produto subtraindo a quantidade comprada //
    $sql = "UPDATE tb_produtos SET estoque_produto = estoque_produto - $quantidade WHERE id_produto = $id_produto";
    mysqli_query($link, $sql);
}

// Atualiza o status do carrinho com o tipo de pagamento utilizado //
$sql = "UPDATE tb_carrinhos SET status_carrinho = $tp_pagamento WHERE numero_carrinho = '{$_SESSION['carrinho']}'";
mysqli_query($link, $sql);

// Encerra a conexão com o banco de dados //
mysqli_close($link);

// Remove a variável de sessão do carrinho, finalizando a compra //
unset($_SESSION['carrinho']);

?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Obrigado</title>
    <link rel="stylesheet" href="estilo3.css">
    <meta http-equiv="refresh" content="5;url=index.php">
</head>
<body>
   <br>
    <h1>Obrigado pela compra!</h1>
    </body>
</html>
