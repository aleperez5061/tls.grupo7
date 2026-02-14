# ================================
#  ETAPA 1: COMPILAR (MAVEN + JDK21)
# ================================
FROM maven:3.9.6-eclipse-temurin-21-alpine AS build
WORKDIR /app

# Copiamos solo el POM para aprovechar la caché
COPY pom.xml .
RUN mvn dependency:go-offline -B

# Copiamos el resto del código
COPY src ./src

# Compilamos y generamos el .jar con nombre definido en <finalName> (tls-app.jar)
RUN mvn clean package -DskipTests


# ================================
#  ETAPA 2: RUNTIME (JRE + USUARIO SEGURO)
# ================================
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app

# Creamos usuario seguro para ejecutar la app
RUN addgroup -S spring && adduser -S spring -G spring
USER spring:spring

# Copiamos el .jar generado
# Tu JAR final real se llama: tls-app.jar
COPY --from=build /app/target/tls-app.jar app.jar

# Render ignorará EXPOSE, pero lo dejamos por buenas prácticas
EXPOSE 8080

# Ejecutar la aplicación
ENTRYPOINT ["java", "-XX:+UseContainerSupport", "-XX:MaxRAMPercentage=75.0", "-jar", "app.jar"]
