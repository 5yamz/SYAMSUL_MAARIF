<?php
header("Content-Type: application/json");

$conn = new mysqli("localhost", "root", "", "todo_app");

$method = $_SERVER['REQUEST_METHOD'];

switch ($method) {
    case "GET":
        $result = $conn->query("SELECT * FROM todo_list");
        $tasks = $result->fetch_all(MYSQLI_ASSOC);
        echo json_encode($tasks);
        break;

    case "POST":
        $data = json_decode(file_get_contents("php://input"), true);
        $stmt = $conn->prepare("INSERT INTO todo_list (task) VALUES (?)");
        $stmt->bind_param("s", $data['task']);
        $stmt->execute();
        echo json_encode(["message" => "Task added"]);
        break;

    case "PUT":
        $data = json_decode(file_get_contents("php://input"), true);
        $stmt = $conn->prepare("UPDATE todo_list SET task = ?, status = ? WHERE id = ?");
        $stmt->bind_param("ssi", $data['task'], $data['status'], $data['id']);
        $stmt->execute();
        echo json_encode(["message" => "Task updated"]);
        break;

    case "DELETE":
        $data = json_decode(file_get_contents("php://input"), true);
        $stmt = $conn->prepare("DELETE FROM todo_list WHERE id = ?");
        $stmt->bind_param("i", $data['id']);
        $stmt->execute();
        echo json_encode(["message" => "Task deleted"]);
        break;

    default:
        http_response_code(405);
        echo json_encode(["message" => "Method not allowed"]);
}
?>
