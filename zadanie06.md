# Zadanie 06

## Zadanie 1: Własne testy

### Wszystkie testy zaliczone

```
PS C:\Users\Michal\Desktop\WRTLZ\Breach-checker> docker compose exec backend pytest -v

============================= test session starts ==============================
platform linux -- Python 3.12.16, pytest-8.4.2, pluggy-1.7.0 -- /usr/local/bin/python3.12
cachedir: .pytest_cache
rootdir: /app
collected 8 items

tests/test_check.py::test_leaked_hash_is_found PASSED                    [ 12%]
tests/test_check.py::test_unknown_hash_is_not_found PASSED               [ 25%]
tests/test_check.py::test_missing_hash_field PASSED                      [ 37%]
tests/test_check.py::test_plaintext_password_is_rejected PASSED          [ 50%]
tests/test_check.py::test_hash_too_short_is_rejected PASSED              [ 62%]
tests/test_check.py::test_hash_non_hex_chars_is_rejected PASSED          [ 75%]
tests/test_check.py::test_non_json_body_is_rejected PASSED               [ 87%]
tests/test_check.py::test_leaked_hash_uppercase_is_found PASSED          [100%]

============================== 8 passed in 0.16s ===============================
```

### Testy bez .lower() (1 failed)

```
PS C:\Users\Michal\Desktop\WRTLZ\Breach-checker> docker compose exec backend pytest

============================= test session starts ==============================
platform linux -- Python 3.12.16, pytest-8.4.2, pluggy-1.7.0 -- /usr/local/bin/python3.12
cachedir: .pytest_cache
rootdir: /app
collected 8 items

tests/test_check.py ....F...                                             [100%]

=================================== FAILURES ===================================
_____________________ test_leaked_hash_uppercase_is_found ______________________

client = <FlaskClient <Flask 'app'>>

    def test_leaked_hash_uppercase_is_found(client):
        response = client.post("/check", json={"hash": LEAKED.upper()})
>       assert response.status_code == 200
E       assert 400 == 200
E        +  where 400 = <WrapperTestResponse streamed [400 BAD REQUEST]>.status_code

tests/test_check.py:39: AssertionError
=========================== short test summary info ============================
FAILED tests/test_check.py::test_leaked_hash_uppercase_is_found - assert 400 == 200
========================= 1 failed, 7 passed in 0.16s ==========================
```

### Dlaczego testy z zajęć nie wykryły usunięcia .lower()

Testy z zajęć zawsze podają stałą `LEAKED` małymi literami — walidacja regex `[0-9a-f]{40}` przepuszcza taki hash, a baza przechowuje skróty małymi literami, więc zapytanie SQL zwraca poprawny wynik. Żaden z oryginalnych testów nie wysyła skrótu wielkimi literami, więc brak normalizacji wielkości liter pozostaje niewykryty.

---

## Zadanie 2: Awaria a blokada

### Wynik curl

```
PS C:\Users\Michal\Desktop\WRTLZ\Breach-checker> '{"hash":"7c4a8d09ca3762af61e59520943dc26494f8941b"}' | curl.exe -s -o blad.html -w "HTTP %{http_code}" -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'
HTTP 500
```

### Wynik docker compose ps -a i docker inspect po restarcie backendu

```
PS C:\Users\Michal\Desktop\WRTLZ\Breach-checker> docker compose ps -a
NAME                        IMAGE                                                                     COMMAND                  SERVICE    CREATED          STATUS                            PORTS
breach-checker-backend-1    sha256:db46906d3d05b9a6a9698076babc73d0c9af5459b0f5303837fec2acc4da0117   "flask --app app run…"   backend    18 minutes ago   Up 3 seconds (health: starting)   5000/tcp
breach-checker-db-1         postgres:16-alpine                                                        "docker-entrypoint.s…"   db         18 minutes ago   Exited (0) 2 minutes ago
breach-checker-frontend-1   sha256:e801a9d557b29379708d49d5257a5fe90f7c7109ca531e27ddd0a639166aeb78   "/docker-entrypoint.…"   frontend   18 minutes ago   Up 6 minutes                      0.0.0.0:8080->80/tcp, [::]:8080->80/tcp

PS C:\Users\Michal\Desktop\WRTLZ\Breach-checker> docker inspect --format '{{.State.Status}} {{.State.ExitCode}}' breach-checker-backend-1
exited 1
```

### Tabela: awaria vs blokada

| | Awaria (brak bazy) | Blokada (krok 4) |
|---|---|---|
| status w `docker compose ps` | `Exited (1)` | `Up (healthy)` |
| CPU w `docker stats` | 0% (proces nie istnieje) | ~0% (proces czeka) |
| ostatnie linie logów | traceback przy starcie, brak linii z `/check` | linia `check: hash=...` bez kończącego `200` |
| co dostał klient | HTTP 500 | HTTP 504 (po 60s timeout nginx) |
| po jakim czasie | natychmiast | po 60 sekundach |

### Co zawierał blad.html i problem bezpieczeństwa

`blad.html` zawierał stronę debuggera Flaska ze szczegółowym traceback'iem — widoczna była ścieżka do pliku `app.py`, fragment kodu źródłowego oraz dane połączenia z bazą (host, nazwa bazy, użytkownik). Na serwerze produkcyjnym taka odpowiedź byłaby problemem bezpieczeństwa, ponieważ ujawnia atakującemu wewnętrzną strukturę aplikacji, nazwy zmiennych i konfigurację — informacje, które ułatwiają dalszy atak. Dlatego tryb debug nigdy nie może być włączony na produkcji.


---

## Zadanie 3: Frontend w trybie deweloperskim

### curl http://localhost:8080/nginx.conf przed poprawką (cały katalog jako bind mount)

```
PS C:\Users\Michal\Desktop\WRTLZ\Breach-checker> curl.exe http://localhost:8080/nginx.conf
(plik nginx.conf został pobrany - nginx serwował cały katalog frontend/)
```

### curl -I http://localhost:8080/nginx.conf po poprawce (osobne bind mounty dla 3 plików)

```
PS C:\Users\Michal\Desktop\WRTLZ\Breach-checker> curl.exe -I http://localhost:8080/nginx.conf
HTTP/1.1 404 Not Found
Server: nginx/1.31.8
Date: Thu, 12 Nov 2026 16:04:01 GMT
Content-Type: text/html
Content-Length: 153
Connection: keep-alive
```

### Dlaczego obraz produkcyjny frontendu nie ma tego problemu i dlaczego montowanie całego katalogu jest ryzykowne

Obraz produkcyjny kopiuje do kontenera tylko konkretne pliki strony (`COPY index.html style.css script.js .`), więc `nginx.conf` i `Dockerfile` nigdy nie trafiają do katalogu serwowanego przez nginx. Montowanie całego katalogu projektu do katalogu serwowanego przez serwer WWW jest ryzykowne, bo udostępnia wszystkie pliki z tego katalogu — w tym pliki konfiguracyjne i `Dockerfile` — które mogą ujawnić atakującemu strukturę i konfigurację serwera.
