# php api->json
```bash
<?php
// VD1/dev/api-login/index.php

$data = [
    "json" => "abc",
];

// phản hồi json
header("Content-Type: application/json");
echo json_encode($data);
?>
```