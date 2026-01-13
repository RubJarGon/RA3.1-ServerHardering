# Práctica 4: Protección DoS con mod_evasive

## 📖 Descripción
En esta práctica se ha configurado un servidor Apache con el módulo **mod_evasive** mediante Docker. El objetivo es proteger el servidor contra ataques de Denegación de Servicio (DoS), bloqueando automáticamente las IPs que superan un umbral de peticiones configurado.

## 🛠️ Tecnologías Utilizadas
* **Base:** Ubuntu 22.04
* **Módulo:** libapache2-mod-evasive
* **Herramienta de test:** Apache Bench (ab)

## 🚀 Despliegue del Contenedor

### 1. Construir la imagen
```bash
docker build -t apache-dos .
```

### 2. Ejecutar el contenedor
```bash
docker run -d -p 8080:80 --name mi-evasive apache-dos
```

## ✅ Verificación y Resultados
Para probar la efectividad del módulo, se ha utilizado la herramienta **Apache Bench** lanzando un ataque de 100 peticiones concurrentes.

```bash
ab -n 100 -c 10 http://localhost:8080/
```

### 📊 Informe de Apache Bench
Como se observa en la siguiente captura, el módulo ha detectado el exceso de tráfico y ha rechazado **94 de las 100 peticiones** (Non-2xx responses), devolviendo un código de estado 403:

![Informe de ataque Apache Bench](imagen_1.png)


