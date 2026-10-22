FROM nginx:alpine
LABEL autor="Michał Trojan" data="2026-10-14"
RUN apk add --no-cache nodejs npm && npm install -g htmlhint
COPY . /usr/share/nginx/
RUN htmlhint /usr/share/nginx/html/index.html
RUN date > /usr/share/nginx/html/build.txt
EXPOSE 80
