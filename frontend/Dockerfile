# ==============================================================================
# STAGE 1: Build (Сборка React/Vite приложения)
# ==============================================================================
FROM node:24.15.0-alpine AS builder

WORKDIR /app

COPY package.json package-lock.json* ./
RUN npm ci

COPY . .

# Vite встраивает (bake) переменные окружения VITE_* прямо в JavaScript код на этапе сборки.
# Чтобы передать их в контейнер во время сборки с CI/CD сервера, мы открываем ARG:
ARG VITE_UMS_BASE_URL
ARG VITE_TWITTER_BASE_URL

# Пробрасываем их из ARG в ENV, чтобы процесс vite сборки их увидел
ENV VITE_UMS_BASE_URL=$VITE_UMS_BASE_URL
ENV VITE_TWITTER_BASE_URL=$VITE_TWITTER_BASE_URL

# Собираем статику в папку /app/dist
RUN npm run build

# ==============================================================================
# STAGE 2: Runtime (Раздача статики через Nginx)
# ==============================================================================
FROM nginx:alpine

# Копируем наш кастомный конфиг Nginx для поддержки React Router
RUN rm /etc/nginx/conf.d/default.conf
COPY nginx.conf /etc/nginx/conf.d/

# Улучшение безопасности: скрываем версию Nginx от сканеров уязвимостей
RUN sed -i 's/keepalive_timeout/server_tokens off;\n    keepalive_timeout/g' /etc/nginx/nginx.conf

# Копируем собранные файлы из первого стейджа сборки
COPY --from=builder /app/dist /usr/share/nginx/html

# Nginx слушает 80 порт внутри контейнера
EXPOSE 80

# Запускаем Nginx на переднем плане
CMD ["nginx", "-g", "daemon off;"]
