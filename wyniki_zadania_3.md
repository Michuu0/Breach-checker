# Zadanie 3 - Wyniki

## 1. Własne etykiety i pochodzenie warstw

Wynik polecenia `docker inspect --format '{{json .Config.Labels}}' michal-frontend:v1`:

```
{"autor":"Michał Trojan","data":"2026-10-22","maintainer":"NGINX Docker Maintainers <docker-maint@nginx.com>"}

```

Wynik polecenia `docker history michal-frontend:v1`:

```
IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT
1da27661570c   5 minutes ago   EXPOSE [80/tcp]                                 0B        buildkit.dockerfile.v0
<missing>      5 minutes ago   RUN /bin/sh -c date > /usr/share/nginx/html/…   24.6kB    buildkit.dockerfile.v0
<missing>      8 minutes ago   RUN /bin/sh -c htmlhint /usr/share/nginx/htm…   4.1kB     buildkit.dockerfile.v0
<missing>      8 minutes ago   COPY . /usr/share/nginx/html # buildkit         32.8kB    buildkit.dockerfile.v0
<missing>      8 minutes ago   RUN /bin/sh -c apk add --no-cache nodejs npm…   102MB     buildkit.dockerfile.v0
<missing>      8 minutes ago   LABEL autor=Michał Trojan data=2026-10-14       0B        buildkit.dockerfile.v0

```

**Odpowiedź:**
Najniższe warstwy o rozmiarze większym niż 0B to system plików obrazu bazowego nginx:alpine. Wyższe warstwy (np. z instrukcją COPY) pochodzą z mojego Dockerfile. Niektóre warstwy mają rozmiar 0B, ponieważ instrukcje takie jak LABEL czy CMD nie dodają plików do systemu, a jedynie modyfikują metadane. Widać tylko moją wartość etykiety, bo warstwy nakładają się na siebie i zmiany z wyższych warstw przykrywają ustawienia z obrazu bazowego.

## 2. RUN działa raz

Oba kontenery w pliku `build.txt` mają ten sam czas: `Thu Oct 22 17:04:39 UTC 2026`

Wynik `docker ps`:

```
CONTAINER ID   IMAGE                COMMAND                  CREATED              STATUS              PORTS                                     NAMES
83dca55eae54   michal-frontend:v1   "/docker-entrypoint.…"   57 seconds ago       Up 56 seconds       0.0.0.0:8082->80/tcp, [::]:8082->80/tcp   cnt2
c66082f54a01   michal-frontend:v1   "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   cnt1

```

**Dlaczego czas w build.txt jest taki sam w obu kontenerach i nie jest to czas ich uruchomienia?**
Czas jest taki sam, bo polecenie RUN wykonuje się tylko raz podczas budowania obrazu (docker build). Ten wygenerowany plik z datą po prostu zapisuje się w obrazie na stałe, a nowo tworzone kontenery tylko go odtwarzają, dlatego mają tę samą datę z momentu budowy.

## 3. Nadpisanie CMD

Wynik polecenia `docker run --rm michal-frontend:v2 cat /etc/nginx/conf.d/default.conf`:

```
server {
    listen       80;
    server_name  localhost;

    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}

```

**Linia root:** `root   /usr/share/nginx/html;`

**Odpowiedź:**
Ta linijka z configu pokazuje docelowy folder (root), z którego Nginx domyślnie wczytuje stronę. Dlatego musimy kopiować nasze pliki dokładnie do /usr/share/nginx/html, żeby serwer je w ogóle widział i wyświetlił użytkownikowi.

## 4. Diagnoza błędnego COPY

Wynik `docker exec zepsuty_kontener ls /usr/share/nginx`:

```
html
index.html
script.js
style.css
zadanie03.md

```

Wynik `docker exec zepsuty_kontener ls /usr/share/nginx/html`:

```
50x.html
build.txt
index.html

```

**Odpowiedź:**
Htmlhint sprawdził nasz plik index.html. Build przeszedł bez błędu, bo samo polecenie COPY zadziałało poprawnie i Docker po prostu skopiował pliki o jeden folder za płytko. Nginx wyświetlił domyślną stronę, bo dalej szukał plików w swoim domyślnym /usr/share/nginx/html, gdzie wciąż był stary plik z obrazu bazowego.

## 5. Usunięcie tagu a usunięcie obrazu

Komunikaty z usuwania:

```
Untagged: breach-checker-frontend:latest
Untagged: breach-checker-frontend:v2
Deleted: sha256:881e0185e02ae0f342dfc0a0dedbadfac721096553aeb70a2c3a6d026fe4dce6

```

**Odpowiedź:**
Polecenie docker rmi faktycznie usuwa obraz z dysku (pojawia się komunikat Deleted) dopiero wtedy, gdy skasujemy jego ostatni tag. Jak obraz ma przypisane kilka tagów, to usunięcie jednego z nich kasuje tylko samą nazwę (Untagged), a fizyczne dane nadal zostają na dysku.