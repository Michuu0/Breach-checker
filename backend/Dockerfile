FROM python:3.12-slim
WORKDIR /app

# 1. Skopiuj do obrazu plik z zależnościami
COPY requirements.txt .

# 2. Zainstaluj wymagane pakiety Pythona (Flask i Flask-CORS)
RUN pip install --no-cache-dir -r requirements.txt

# 3. Skopiuj kod aplikacji do katalogu roboczego
COPY app.py .

EXPOSE 5000
CMD ["python", "app.py"]