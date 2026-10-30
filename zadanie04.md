1.
PS C:\Users\Michal\Desktop\Breach-checker-main> curl.exe http://localhost:5000/health
{"status":"ok"}

PS C:\Users\Michal\Desktop\Breach-checker-main> docker logs backend
Wczytano 89 hashy z /app/data/leaked-sha1.txt
 * Serving Flask app 'app'
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://172.19.0.2:5000
Press CTRL+C to quit
172.19.0.1 - - [29/Oct/2026 22:51:00] "GET /health HTTP/1.1" 200 -
172.19.0.1 - - [29/Oct/2026 22:51:03] "GET / HTTP/1.1" 404 -
172.19.0.1 - - [29/Oct/2026 22:51:04] "GET / HTTP/1.1" 404 -
PS C:\Users\Michal\Desktop\Breach-checker-main>

2.
PS C:\Users\Michal\Desktop\Breach-checker-main> docker run --rm --network breach-net healthcheck-probe
backend: OK

PS C:\Users\Michal\Desktop\Breach-checker-main> docker run --rm healthcheck-probe
backend: UNREACHABLE

PS C:\Users\Michal\Desktop\Breach-checker-main> docker stop backend
backend
PS C:\Users\Michal\Desktop\Breach-checker-main> docker run --rm --network breach-net healthcheck-probe
backend: UNREACHABLE

Bez --network breach-net kontener nie znajduje nazwy backend, ponieważ nie widzi sieci prywatnej dockera

3.
Po restarcie 
PS C:\Users\Michal\Desktop\Breach-checker-main> docker exec backend cat /tmp/notatka.txt
cat: /tmp/notatka.txt: No such file or directory


Po rm + run

PS C:\Users\Michal\Desktop\Breach-checker-main> docker rm -f backend
backend
PS C:\Users\Michal\Desktop\Breach-checker-main> docker run -d --name backend --network breach-net -p 5000:5000 -v ${PWD}/data:/app/data breach-backend
103ced89268967868e5c5d3d1bdd44288c4b04e4125539295ca92e36be907317
PS C:\Users\Michal\Desktop\Breach-checker-main> docker exec backend cat /tmp/notatka.txt
cat: /tmp/notatka.txt: No such file or directory

Zawartość pliku historia.txt po dwóch uruchomieniach:
PS C:\Users\Michal\Desktop\Breach-checker-main> docker run --rm -v breach-dane:/dane alpine sh -c "date >> /dane/historia.txt; cat /dane/historia.txt"
Thu Oct 29 23:04:45 UTC 2026
Thu Oct 29 23:04:52 UTC 2026


Plik w /tmp przetrwał restart kontenera, ponieważ restart zachowuje tymczasową warstwę zapisywalną (container layer). Usunięcie kontenera (docker rm) całkowicie niszczy tę warstwę wraz ze wszystkimi plikami utworzonymi wewnątrz procesu.

Plik historia.txt przetrwał usunięcie obu kontenerów, ponieważ został zapisany w osobnym wolumenie Dockera (breach-dane), którego cykl życia jest niezależny od cyklu życia konkretnych kontenerów.

Lista wycieków trzymana w kontenerze nie przetrwałaby polecenia docker rm, gdyby znajdowała się wyłącznie w jego zapisywalnej warstwie. Przepada ona wraz z kontenerem, chyba że jest zamontowana z wolumenu lub katalogu hosta (tak jak za pomocą -v ${PWD}/data:/app/data).
