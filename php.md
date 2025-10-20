# PHP Cheat Sheet — Gotowe fragmenty kodu

Plik: `php_cheatsheet.md`  
Krótko, treściwie i gotowe do wklejenia. Używaj z rozsądkiem. Kod przykładowy używa PDO dla DB i bezpiecznych funkcji (prepared statements, random_int).

---

## 1) Pobieranie argumentu z URL (GET / POST / REQUEST)

```php
// GET (najbezpieczniej używać filter_input)
$param = filter_input(INPUT_GET, 'param', FILTER_SANITIZE_STRING);
$param = $param !== null ? $param : 'domyślna_wartość';

// POST
$post = filter_input(INPUT_POST, 'pole', FILTER_SANITIZE_STRING);

// REQUEST (GET lub POST)
$any = isset($_REQUEST['x']) ? trim((string)$_REQUEST['x']) : null;

// Przykład z fallback i walidacją liczby całkowitej
$id = filter_input(INPUT_GET, 'id', FILTER_VALIDATE_INT);
if ($id === false || $id === null) {
    $id = 0; // fallback
}
```

> Uwaga: `FILTER_SANITIZE_STRING` usuwa niektóre znaki; do HTML używaj `htmlspecialchars()` przy wyświetlaniu.

---

## 2) Ustawianie cookie u klienta

```php
// Musisz wywołać setcookie przed wysłaniem jakiegokolwiek outputu (echo, HTML).
$name = 'moje_cookie';
$value = 'wartosc';
$expires = time() + 60*60*24*30; // 30 dni
$path = '/';
$domain = ''; // zostaw puste lub 'example.com'
$secure = isset($_SERVER['HTTPS']); // tylko HTTPS
$httponly = true; // niedostępne z JS
$sameSite = 'Lax'; // 'Lax' albo 'Strict' albo 'None' (z 'None' wymagane secure)

setcookie($name, $value, [
    'expires' => $expires,
    'path' => $path,
    'domain' => $domain,
    'secure' => $secure,
    'httponly' => $httponly,
    'samesite' => $sameSite
]);

// Alternatywnie (starsze PHP)
setcookie($name, $value, $expires, $path, $domain, $secure, $httponly);
```

---

## 3) Sprawdzanie cookie u klienta

```php
if (isset($_COOKIE['moje_cookie'])) {
    $cookie = $_COOKIE['moje_cookie'];
    // waliduj/odfiltruj
    $cookie = trim((string)$cookie);
} else {
    // brak cookie
}
```

---

## 4) Połączenie z MySQL (PDO) — gotowy kod

```php
function get_db_connection(array $cfg): PDO {
    // $cfg = [
    //   'host'=>'127.0.0.1',
    //   'port'=>3306,
    //   'dbname'=>'nazwa_db',
    //   'user'=>'uzytkownik',
    //   'pass'=>'haslo'
    // ];
    $host = $cfg['host'] ?? '127.0.0.1';
    $port = $cfg['port'] ?? 3306;
    $dbname = $cfg['dbname'] ?? '';
    $user = $cfg['user'] ?? '';
    $pass = $cfg['pass'] ?? '';
    $dsn = "mysql:host={$host};port={$port};dbname={$dbname};charset=utf8mb4";

    $opts = [
        PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
        PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
        PDO::ATTR_EMULATE_PREPARES => false,
    ];

    try {
        return new PDO($dsn, $user, $pass, $opts);
    } catch (PDOException $e) {
        // Nie ujawniać haseł w prod — loguj ostrożnie.
        throw new RuntimeException('DB connection failed: ' . $e->getMessage());
    }
}
```

---

## 5) Funkcja: sprawdzanie wartości w kolumnie tabeli

```php
function exists_in_column(PDO $pdo, string $table, string $column, $value): bool {
    // Uwaga: table i column nie mogą być parametrami prepared statement.
    // Należy upewnić się, że nazwy tabel i kolumn są zaufane lub whitelistowane.
    $allowedTables = ['users','products']; // przykład whitelisty
    if (!in_array($table, $allowedTables, true)) {
        throw new InvalidArgumentException('Nieznana tabela');
    }

    $sql = "SELECT 1 FROM `{$table}` WHERE `{$column}` = :val LIMIT 1";
    $stmt = $pdo->prepare($sql);
    $stmt->execute([':val' => $value]);
    return (bool) $stmt->fetchColumn();
}
```

---

## 6) Funkcja: wpisywanie (insert) z tablicy asocjacyjnej

```php
function insert_into_table(PDO $pdo, string $table, array $data): int {
    // $data = ['col1'=>'val1','col2'=>'val2']
    $allowedTables = ['users','products']; // whitelistuj tabele
    if (!in_array($table, $allowedTables, true)) {
        throw new InvalidArgumentException('Nieznana tabela');
    }
    if (empty($data)) {
        throw new InvalidArgumentException('Brak danych do insertu');
    }

    $cols = array_keys($data);
    $placeholders = array_map(function($c){ return ':' . $c; }, $cols);

    $sql = sprintf(
        "INSERT INTO `%s` (`%s`) VALUES (%s)",
        $table,
        implode('`,`', $cols),
        implode(',', $placeholders)
    );

    $stmt = $pdo->prepare($sql);
    $params = [];
    foreach ($data as $k => $v) {
        $params[':' . $k] = $v;
    }
    $stmt->execute($params);
    return (int)$pdo->lastInsertId();
}
```

---

## 7) Użycie powyższych funkcji — przykład kompletny

```php
require 'db.php'; // zakładamy, że get_db_connection jest dostępne

$config = [
    'host' => '127.0.0.1',
    'port' => 3306,
    'dbname' => 'mojadb',
    'user' => 'root',
    'pass' => 'sekret'
];

$pdo = get_db_connection($config);

$username = trim((string)($_POST['username'] ?? ''));

// sprawdź czy istnieje w kolumnie 'username' tabeli 'users'
if (exists_in_column($pdo, 'users', 'username', $username)) {
    echo "Użytkownik już istnieje";
} else {
    $newId = insert_into_table($pdo, 'users', [
        'username' => $username,
        'created_at' => date('Y-m-d H:i:s')
    ]);
    echo "Dodano użytkownika o ID: $newId";
}
```

---

## 8) Sprawdzenie IP klienta (najbardziej przydatne nagłówki)

```php
function get_client_ip(): string {
    $keys = [
        'HTTP_CLIENT_IP',
        'HTTP_X_FORWARDED_FOR',
        'HTTP_X_FORWARDED',
        'HTTP_FORWARDED_FOR',
        'HTTP_FORWARDED',
        'REMOTE_ADDR'
    ];
    foreach ($keys as $key) {
        if (!empty($_SERVER[$key])) {
            $ip = $_SERVER[$key];
            // X-Forwarded-For może zawierać listę adresów
            if (strpos($ip, ',') !== false) {
                $parts = array_map('trim', explode(',', $ip));
                $ip = $parts[0];
            }
            return $ip;
        }
    }
    return '0.0.0.0';
}
```

> Uwaga: Nagłówki jak `X-Forwarded-For` można podrobić — ufa się tylko jeżeli za reverse proxy które kontrolujesz.

---

## 9) Sprawdzanie nagłówków od klienta (wszystkich)

```php
// PHP ma getallheaders() w większości serwerów Apache/NGINX + FastCGI.
function get_request_headers_safe(): array {
    if (function_exists('getallheaders')) {
        return getallheaders();
    }
    $headers = [];
    foreach ($_SERVER as $name => $value) {
        if (substr($name, 0, 5) === 'HTTP_') {
            $header = str_replace(' ', '-', ucwords(strtolower(str_replace('_', ' ', substr($name, 5)))));
            $headers[$header] = $value;
        }
    }
    return $headers;
}
$all = get_request_headers_safe();
// przykładowe wypisanie
// var_export($all);
```

---

## 10) Sprawdzenie konkretnego headera i jego zawartości

```php
function get_header_value(string $name): ?string {
    $headers = get_request_headers_safe();
    $nameNorm = str_replace(' ', '-', ucwords(strtolower(str_replace('-', ' ', $name))));
    return $headers[$nameNorm] ?? null;
}

// użycie:
$userAgent = get_header_value('User-Agent'); // np. Mozilla/...
$myHeader = get_header_value('X-Custom-Header');
if ($myHeader !== null) {
    // waliduj wartość
}
```

---

## 11) Generator losowego stringa (bezpieczny kryptograficznie)

```php
function generate_random_string(int $length = 16, bool $upper = true, bool $lower = true, bool $digits = true, bool $special = false): string {
    if ($length <= 0) return '';
    $sets = [];
    if ($upper) $sets[] = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
    if ($lower) $sets[] = 'abcdefghijklmnopqrstuvwxyz';
    if ($digits) $sets[] = '0123456789';
    if ($special) $sets[] = '!@#$%^&*()-_=+[]{}<>?,.';
    if (empty($sets)) {
        throw new InvalidArgumentException('Brak wybranych zestawów znaków');
    }
    $all = implode('', $sets);
    $max = strlen($all) - 1;
    $out = '';
    for ($i = 0; $i < $length; $i++) {
        $idx = random_int(0, $max);
        $out .= $all[$idx];
    }
    return $out;
}

// przykłady:
$pass = generate_random_string(12, true, true, true, true);
$token = generate_random_string(32, false, true, true, false);
```

---

## 12) Przydatne wskazówki bezpieczeństwa
- Nigdy nie składaj SQL z niezaufanych wartości bez przygotowanych zapytań.
- Whitelistuj nazwy tabel/kolumn, jeżeli musisz dopuszczać je dynamicznie.
- Używaj `httponly` i `secure` na ciasteczkach sesji.
- Nie polegaj na `X-Forwarded-For` bez kontroli proxy.
- Hasła przechowuj z `password_hash()` i weryfikuj `password_verify()`.

---

## 13) Szybkie FAQ
- Q: Czy `filter_input` wystarczy do walidacji?  
  A: Dość na wejście; przy wyświetlaniu stosuj `htmlspecialchars()`. Dla DB używaj prepared statements.
- Q: Czy setcookie działa po echo?  
  A: Nie, nagłówki muszą iść przed outputem.
- Q: Czy random_int jest bezpieczny?  
  A: Tak, to kryptograficznie bezpieczny generator w PHP.

---

