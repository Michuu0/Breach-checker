1\.
PS C:\\Users\\Michal\\Desktop\\Breach-checker-main> curl.exe http://localhost:5000/health

{"status":"ok"}



PS C:\\Users\\Michal\\Desktop\\Breach-checker-main> docker logs backend

Wczytano 89 hashy z /app/data/leaked-sha1.txt

&#x20;\* Serving Flask app 'app'

&#x20;\* Debug mode: off

WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.

&#x20;\* Running on all addresses (0.0.0.0)

&#x20;\* Running on http://127.0.0.1:5000

&#x20;\* Running on http://172.19.0.2:5000

Press CTRL+C to quit

172.19.0.1 - - \[29/Oct/2026 22:51:00] "GET /health HTTP/1.1" 200 -

172.19.0.1 - - \[29/Oct/2026 22:51:03] "GET / HTTP/1.1" 404 -

172.19.0.1 - - \[29/Oct/2026 22:51:04] "GET / HTTP/1.1" 404 -

PS C:\\Users\\Michal\\Desktop\\Breach-checker-main>



2\.

PS C:\\Users\\Michal\\Desktop\\Breach-checker-main> docker run --rm --network breach-net healthcheck-probe

backend: OK

PS C:\\Users\\Michal\\Desktop\\Breach-checker-main> docker run --rm healthcheck-probe

backend: UNREACHABLE

