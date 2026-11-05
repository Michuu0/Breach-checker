PS C:\\Users\\Michal\\Desktop\\projekty\\Breach-checker> docker compose up -d --build

\[+] Building 1.8s (23/23) FINISHED

&#x20;=> \[internal] load local bake definitions                                                                         0.0s

&#x20;=> => reading from stdin 1.10kB                                                                                   0.0s

&#x20;=> \[backend internal] load build definition from Dockerfile                                                       0.0s

&#x20;=> => transferring dockerfile: 373B                                                                               0.0s

&#x20;=> \[frontend internal] load build definition from Dockerfile                                                      0.0s

&#x20;=> => transferring dockerfile: 330B                                                                               0.0s

&#x20;=> \[backend internal] load metadata for docker.io/library/python:3.12-slim                                        0.9s

&#x20;=> \[frontend internal] load metadata for docker.io/library/nginx:alpine                                           0.9s

&#x20;=> \[backend internal] load .dockerignore                                                                          0.0s

&#x20;=> => transferring context: 2B                                                                                    0.0s

&#x20;=> \[frontend internal] load .dockerignore                                                                         0.0s

&#x20;=> => transferring context: 68B                                                                                   0.0s

&#x20;=> \[backend 1/5] FROM docker.io/library/python:3.12-slim@sha256:4840d514c583a4d3d8cad3940e68dee6f0f6f3635c12fcd0  0.0s

&#x20;=> => resolve docker.io/library/python:3.12-slim@sha256:4840d514c583a4d3d8cad3940e68dee6f0f6f3635c12fcd0a3d74c21  0.0s

&#x20;=> \[backend internal] load build context                                                                          0.0s

&#x20;=> => transferring context: 63B                                                                                   0.0s

&#x20;=> \[frontend 1/5] FROM docker.io/library/nginx:alpine@sha256:785835ea65db4bba6f021acc0f3be91b0fef76a53fe41f8588c  0.0s

&#x20;=> => resolve docker.io/library/nginx:alpine@sha256:785835ea65db4bba6f021acc0f3be91b0fef76a53fe41f8588c6698d2aed  0.0s

&#x20;=> \[frontend internal] load build context                                                                         0.0s

&#x20;=> => transferring context: 120B                                                                                  0.0s

&#x20;=> CACHED \[backend 2/5] WORKDIR /app                                                                              0.0s

&#x20;=> CACHED \[backend 3/5] COPY requirements.txt .                                                                   0.0s

&#x20;=> CACHED \[backend 4/5] RUN pip install --no-cache-dir -r requirements.txt                                        0.0s

&#x20;=> CACHED \[backend 5/5] COPY app.py .                                                                             0.0s

&#x20;=> \[backend] exporting to image                                                                                   0.1s

&#x20;=> => exporting layers                                                                                            0.0s

&#x20;=> => exporting manifest sha256:7e1533649eef7972428696ebcc856f381b32099a5ff3cd1f09d09c86fa32f187                  0.0s

&#x20;=> => exporting config sha256:7010dce88581ea0bbc0b55bc05f13d018e037216621ab9bcaf5a302f5ce654e2                    0.0s

&#x20;=> => exporting attestation manifest sha256:916ebd107d7a2962c4975f4797938653e6949900058c62bc8dfe9287dd929b72      0.0s

&#x20;=> => exporting manifest list sha256:ac8805063d7bbab2ce5af57a81c51a18cd4f409b31f8d8dcfa6bd963a20de94c             0.0s

&#x20;=> => naming to docker.io/library/breach-checker-backend:latest                                                   0.0s

&#x20;=> => unpacking to docker.io/library/breach-checker-backend:latest                                                0.0s

&#x20;=> CACHED \[frontend 2/5] RUN apk add --no-cache nodejs npm \&\& npm install -g htmlhint                             0.0s

&#x20;=> CACHED \[frontend 3/5] COPY index.html style.css script.js /usr/share/nginx/html/                               0.0s

&#x20;=> CACHED \[frontend 4/5] COPY nginx.conf /etc/nginx/conf.d/default.conf                                           0.0s

&#x20;=> CACHED \[frontend 5/5] RUN htmlhint /usr/share/nginx/html/index.html                                            0.0s

&#x20;=> \[frontend] exporting to image                                                                                  0.1s

&#x20;=> => exporting layers                                                                                            0.0s

&#x20;=> => exporting manifest sha256:62ee4f7c96ecc025fd51f09862c2cd9e2bd186ded555d127a888d451d8cc1cc9                  0.0s

&#x20;=> => exporting config sha256:13d49eef066124189198c2ed1af2ca655eac95065b9274a8dfea5e2e6e070df2                    0.0s

&#x20;=> => exporting attestation manifest sha256:123aff9386311b74b8e577a4e7fca9c38302b78fb3dfd905b3e8b1eef671d3bc      0.0s

&#x20;=> => exporting manifest list sha256:9036ab74f5b6ceb7d67c8651d60f82ed061df6711f583906689323afd16d0f2a             0.0s

&#x20;=> => naming to docker.io/library/breach-checker-frontend:latest                                                  0.0s

&#x20;=> => unpacking to docker.io/library/breach-checker-frontend:latest                                               0.0s

&#x20;=> \[backend] resolving provenance for metadata file                                                               0.0s

&#x20;=> \[frontend] resolving provenance for metadata file                                                              0.0s

\[+] up 6/6

&#x20;✔ Image breach-checker-frontend       Built                                                                        2.0s

&#x20;✔ Image breach-checker-backend        Built                                                                        1.9s

&#x20;✔ Network breach-checker\_default      Created                                                                      0.0s

&#x20;✔ Container breach-checker-db-1       Healthy                                                                      6.0s

&#x20;✔ Container breach-checker-backend-1  Healthy                                                                     11.6s

&#x20;✔ Container breach-checker-frontend-1 Started   

PS C:\\Users\\Michal\\Desktop\\projekty\\Breach-checker> docker compose ps

NAME                        IMAGE                     COMMAND                  SERVICE    CREATED          STATUS                    PORTS

breach-checker-backend-1    breach-checker-backend    "python app.py"          backend    14 seconds ago   Up 7 seconds (healthy)    5000/tcp

breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         14 seconds ago   Up 13 seconds (healthy)   5432/tcp

breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   14 seconds ago   Up 1 second               0.0.0.0:8080->80/tcp, \[::]:8080->80/tcp









PS C:\\Users\\Michal\\Desktop\\projekty\\Breach-checker> docker compose exec db psql -U breach -d breachdb -c "INSERT INTO leaked\_hashes VALUES ('bc45408273d7e1914b741c3407b0b559764f7876');"

INSERT 0 1

PS C:\\Users\\Michal\\Desktop\\projekty\\Breach-checker> '{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'

{"found":true}

PS C:\\Users\\Michal\\Desktop\\projekty\\Breach-checker> docker compose down

\[+] down 4/4

&#x20;✔ Container breach-checker-frontend-1 Removed                                                                      0.4s

&#x20;✔ Container breach-checker-backend-1  Removed                                                                      3.2s

&#x20;✔ Container breach-checker-db-1       Removed                                                                      0.3s

&#x20;✔ Network breach-checker\_default      Removed                                                                      0.2s

PS C:\\Users\\Michal\\Desktop\\projekty\\Breach-checker> docker compose up -d

\[+] up 4/4

&#x20;✔ Network breach-checker\_default      Created                                                                      0.0s

&#x20;✔ Container breach-checker-db-1       Healthy                                                                      5.9s

&#x20;✔ Container breach-checker-backend-1  Healthy                                                                     11.5s

&#x20;✔ Container breach-checker-frontend-1 Started                                                                     11.7s

PS C:\\Users\\Michal\\Desktop\\projekty\\Breach-checker> '{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'

{"found":true}

PS C:\\Users\\Michal\\Desktop\\projekty\\Breach-checker> docker compose down -v

\[+] down 5/5

&#x20;✔ Container breach-checker-frontend-1 Removed                                                                      0.4s

&#x20;✔ Container breach-checker-backend-1  Removed                                                                      3.2s

&#x20;✔ Container breach-checker-db-1       Removed                                                                      0.2s

&#x20;✔ Volume breach-checker\_pgdata        Removed                                                                      0.0s

&#x20;✔ Network breach-checker\_default      Removed                                                                      0.3s

PS C:\\Users\\Michal\\Desktop\\projekty\\Breach-checker> docker compose up -d

\[+] up 5/5

&#x20;✔ Network breach-checker\_default      Created                                                                      0.0s

&#x20;✔ Volume breach-checker\_pgdata        Created                                                                      0.0s

&#x20;✔ Container breach-checker-db-1       Healthy                                                                      5.9s

&#x20;✔ Container breach-checker-backend-1  Healthy                                                                     11.5s

&#x20;✔ Container breach-checker-frontend-1 Started                                                                     11.7s

PS C:\\Users\\Michal\\Desktop\\projekty\\Breach-checker> '{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'

{"found":false}



dlaczego skrót przetrwał down, a nie przetrwał down -v?
Polecenie docker compose down usuwa tylko kontenery i domyślną sieć, ale pozostawia wolumeny nienaruszone. Dane bazy, w tym ręcznie dodany wpis, znajdowały się w wolumenie nazwanym pgdata, więc przetrwały. Polecenie z flagą -v (docker compose down -v) usuwa również wszystkie wolumeny projektu, co fizycznie kasuje z dysku całą bazę danych wraz z naszym skrótem.



dlaczego po down -v w bazie znów są 89 skróty z próbki, choć cała baza została skasowana?
Po skasowaniu wolumenu i ponownym uruchomieniu środowiska, Docker stworzył nowy, pusty wolumen. Gdy obraz postgres wykrył, że katalog z danymi jest pusty, potraktował to jako pierwsze uruchomienie i automatycznie wykonał skrypt inicjalizujący init.sql, który od nowa załadował 89 skrótów z oryginalnego pliku tekstowego.


Skalowanie: 
PS C:\\Users\\Michal\\Desktop\\projekty\\Breach-checker> docker compose ps

NAME                        IMAGE                     COMMAND                  SERVICE    CREATED          STATUS                    PORTS

breach-checker-backend-1    breach-checker-backend    "python app.py"          backend    10 minutes ago   Up 10 minutes (healthy)   5000/tcp

breach-checker-backend-2    breach-checker-backend    "python app.py"          backend    8 minutes ago    Up 8 minutes (healthy)    5000/tcp

breach-checker-backend-3    breach-checker-backend    "python app.py"          backend    8 minutes ago    Up 8 minutes (healthy)    5000/tcp

breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         10 minutes ago   Up 10 minutes (healthy)   5432/tcp

breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   10 minutes ago   Up 5 minutes              0.0.0.0:8080->80/tcp, \[::]:8080->80/tcp



PS C:\\Users\\Michal\\Desktop\\projekty\\Breach-checker> docker compose exec frontend nslookup backend

Server:         127.0.0.11

Address:        127.0.0.11:53



Non-authoritative answer:



Non-authoritative answer:

Name:   backend

Address: 172.20.0.6

Name:   backend

Address: 172.20.0.5

Name:   backend

Address: 172.20.0.3



Przed restartem:
backend-1  | 172.20.0.4 - - \[05/Nov/2026 18:11:18] "POST /check HTTP/1.1" 200 -

backend-1  | check: hash=19187...

backend-1  | 172.20.0.4 - - \[05/Nov/2026 18:11:19] "POST /check HTTP/1.1" 200 -

backend-1  | check: hash=19187...

backend-1  | 172.20.0.4 - - \[05/Nov/2026 18:11:19] "POST /check HTTP/1.1" 200 -

backend-1  | check: hash=19187...

backend-1  | 172.20.0.4 - - \[05/Nov/2026 18:11:19] "POST /check HTTP/1.1" 200 -

backend-1  | check: hash=19187...

backend-1  | 172.20.0.4 - - \[05/Nov/2026 18:11:19] "POST /check HTTP/1.1" 200 -


Po restarcie: 

backend-3  | 172.20.0.4 - - \[05/Nov/2026 18:19:20] "POST /check HTTP/1.1" 200 -

backend-3  | check: hash=1dfba...

backend-2  | 172.20.0.4 - - \[05/Nov/2026 18:19:21] "POST /check HTTP/1.1" 200 -

backend-2  | check: hash=1dfba...

backend-1  | 172.20.0.4 - - \[05/Nov/2026 18:19:20] "POST /check HTTP/1.1" 200 -

backend-1  | check: hash=1dfba...



dlaczego przed restartem frontendu ruch trafiał do jednej repliki?

Przed restartem frontendu ruch trafiał do jednej repliki, ponieważ Nginx rozwiązuje nazwę usługi backend na adresy IP tylko raz, w momencie swojego uruchomienia. Kiedy dodaliśmy kolejne repliki, działający już Nginx o nich nie wiedział, bo trzymał w pamięci tylko ten jeden adres, który pobrał na starcie. Dopiero restart frontendu wymusił na serwerze Nginx ponowne sprawdzenie w DNS Dockera, pod jakimi adresami kryje się backend, co pozwoliło mu zauważyć wszystkie trzy repliki.



dlaczego skalowanie byłoby niemożliwe, gdyby backend nadal miał ports: "5000:5000"?

Skalowanie byłoby niemożliwe z instrukcją ports: "5000:5000", ponieważ oznacza ona twarde przypisanie portu 5000 na komputerze do portu 5000 w kontenerze. Komputer ma tylko jeden taki port, więc pierwsza replika uruchomiłaby się normalnie, ale próba uruchomienia drugiej i trzeciej zakończyłaby się błędem, że port 5000 jest już zajęty.

