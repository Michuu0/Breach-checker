zad1

build-frontend się nie powiódł: https://github.com/Michuu0/Breach-checker/actions/runs/45820048121/job/139909667902#step:5:133

> [lint 5/5] RUN htmlhint index.html:
0.209 
0.224    /site/index.html
0.224       L16 |        <h1>Breach Checker
0.224                    ^ Tag must be paired, missing: [ </h1> ], start tag match failed [ <h1> ] on line 16. (tag-pair)
0.224 
0.226 Scanned 1 files, found 1 errors in 1 files (19 ms)
------


Build się powiódł: https://github.com/Michuu0/Breach-checker/actions/runs/45820359570


W tym zielonym uruchomieniu weryfikacja sprawdza oba obrazy produkcyjne (backend i frontend) pobrane z rejestru GHCR za pomocą docker compose pull, dzięki rozszerzeniu pliku docker-compose.ci.yml. We wcześniejszej wersji pipeline'u z rejestru pobierany był tylko backend, a frontend budował się na runnerze ze źródeł lokalnych.


zad2
Verify nie przeszło i promote skipped; https://github.com/Michuu0/Breach-checker/actions/runs/45824886322

naprawiono nasłuchiwanie na 0.0.0.0 i działa: https://github.com/Michuu0/Breach-checker/actions/runs/45825528313

Odpowiedź: Po nieudanym uruchomieniu tag latest w rejestrze nadal wskazywał na poprzednią, w pełni działającą i zweryfikowaną wersję obrazu, ponieważ pominięcie joba promote uniemożliwiło przesunięcie tej etykiety na zepsuty obraz. Jest to kluczowe dla każdego, kto pobiera obraz domyślnym poleceniem docker pull (co odpowiada :latest), ponieważ chroni to środowiska produkcyjne przed automatycznym wdrożeniem niedziałającego kontenera. Dzięki temu etykieta latest oznacza wyłącznie obrazy, które pomyślnie przeszły pełną weryfikację integracyjną.

zad3

PS C:\Users\Michal\Desktop\WRTLZ\Breach-checker> docker logout ghcr.io
Removing login credentials for ghcr.io
PS C:\Users\Michal\Desktop\WRTLZ\Breach-checker> docker pull ghcr.io/michuu0/breach-checker-backend:latest
latest: Pulling from michuu0/breach-checker-backend
ff7b983c586b: Pulling fs layer
ff7b983c586b: Pull complete
30cf67d7101c: Pull complete
83a14706aa6d: Download complete
fb767d20bc6a: Pull complete
e3e428093f5e: Pull complete
54889f3f1f53: Pull complete
b92fc41182e2: Pull complete
21d64f3c5378: Pull complete
Digest: sha256:464a401da35dd06b46c1d8c8f5c825566d315f32a94c37c5ffb1d0d92329b92f
Status: Downloaded newer image for ghcr.io/michuu0/breach-checker-backend:latest
ghcr.io/michuu0/breach-checker-backend:latest
PS C:\Users\Michal\Desktop\WRTLZ\Breach-checker> docker pull ghcr.io/michuu0/breach-checker-backend:85cecc1b361630857f135add3eec9a7aadb18092
85cecc1b361630857f135add3eec9a7aadb18092: Pulling from michuu0/breach-checker-backend
Digest: sha256:464a401da35dd06b46c1d8c8f5c825566d315f32a94c37c5ffb1d0d92329b92f
Status: Downloaded newer image for ghcr.io/michuu0/breach-checker-backend:85cecc1b361630857f135add3eec9a7aadb18092
ghcr.io/michuu0/breach-checker-backend:85cecc1b361630857f135add3eec9a7aadb18092
PS C:\Users\Michal\Desktop\WRTLZ\Breach-checker> docker images --digests ghcr.io/michuu0/breach-checker-backend
REPOSITORY                               TAG                                        DIGEST          
                                                          IMAGE ID       CREATED             SIZE   
ghcr.io/michuu0/breach-checker-backend   85cecc1b361630857f135add3eec9a7aadb18092   sha256:464a401da35dd06b46c1d8c8f5c825566d315f32a94c37c5ffb1d0d92329b92f   464a401da35d   About an hour ago   212MB  
ghcr.io/michuu0/breach-checker-backend   latest                                     sha256:464a401da35dd06b46c1d8c8f5c825566d315f32a94c37c5ffb1d0d92329b92f   464a401da35d   About an hour ago   212MB  
PS C:\Users\Michal\Desktop\WRTLZ\Breach-checker> docker push ghcr.io/michuu0/breach-checker-backend:latest
The push refers to repository [ghcr.io/michuu0/breach-checker-backend]
30cf67d7101c: Layer already exists
e3e428093f5e: Layer already exists                                                                  
286d8c291daa: Layer already exists
83a14706aa6d: Layer already exists
fb767d20bc6a: Layer already exists
21d64f3c5378: Layer already exists
54889f3f1f53: Layer already exists
b92fc41182e2: Layer already exists
ff7b983c586b: Layer already exists
latest: digest: sha256:464a401da35dd06b46c1d8c8f5c825566d315f32a94c37c5ffb1d0d92329b92f size: 2199  
PS C:\Users\Michal\Desktop\WRTLZ\Breach-checker>




