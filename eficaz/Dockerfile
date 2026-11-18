# syntax=docker/dockerfile:1
# Imagem da Calculadora RTC — regime regular (Receita Federal, MIT), construída pela Eficaz.
# Contexto do build = checkout da tag rtc-<versão> (código da Receita intacto).

FROM eclipse-temurin:21-jdk AS build
WORKDIR /src
COPY . .
# 1) Flyway gera o SQLite somente leitura (caminho relativo ./calculadora/db).
# 2) mvnw package gera target/api-regime-geral.jar (testes ficam no CI da Receita).
RUN chmod +x mvnw \
 && mkdir -p calculadora/db \
 && ./mvnw -B -ntp flyway:migrate -Dflyway.configFiles=./flyway/flyway-pro.conf \
 && ./mvnw -B -ntp clean package -DskipTests \
 && test -s calculadora/db/calculadora-pro.db \
 && test -s target/api-regime-geral.jar

FROM eclipse-temurin:21-jre
ARG REVISION=desconhecido
ARG VERSION=desconhecida
LABEL org.opencontainers.image.title="rtc-regular" \
      org.opencontainers.image.description="Calculadora de Tributos da Reforma (CBS/IBS/IS) — regime regular, perfil offline" \
      org.opencontainers.image.source="https://github.com/carlosallef/calculadora-rtc-regime-regular" \
      org.opencontainers.image.licenses="MIT" \
      org.opencontainers.image.revision="${REVISION}" \
      org.opencontainers.image.version="${VERSION}"
RUN apt-get update \
 && apt-get install -y --no-install-recommends curl \
 && rm -rf /var/lib/apt/lists/* \
 && useradd --system --uid 10001 --home /app rtc
WORKDIR /app
COPY --from=build /src/target/api-regime-geral.jar app.jar
COPY --from=build /src/calculadora/db/calculadora-pro.db calculadora/db/calculadora-pro.db
COPY --from=build /src/LICENSE LICENSE
USER rtc
ENV TZ=America/Sao_Paulo \
    JAVA_OPTS="-Xmx640m -XX:+ExitOnOutOfMemoryError"
EXPOSE 8080 9101
HEALTHCHECK --interval=30s --timeout=5s --start-period=90s --retries=3 \
  CMD curl -fsS http://localhost:9101/health || exit 1
# O jar roda de /app, que contém ./calculadora/db.
ENTRYPOINT ["sh", "-c", "exec java $JAVA_OPTS -Dspring.profiles.active=offline -jar app.jar"]
