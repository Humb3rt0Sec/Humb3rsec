# Máquina ANALYST — Writeup

## 1. Objetivo
Analizar el incidente de seguridad del laboratorio mediante las evidencias PCAP y JSON proporcionadas.

IP del objetivo: 172.17.0.2

## 2. Reconocimiento
El escaneo identificó:
- Puerto 22/tcp: SSH (OpenSSH).
- Puerto 80/tcp: HTTP (Gunicorn).

La aplicación corresponde al laboratorio "El caso de Pinguinito — Lab Blue Team".

## 3. Evidencias
Archivos analizados:
- incidente_pinguino.pcap
- threat_intel_feed.json

Herramientas utilizadas: Nmap, Wireshark/tshark y terminal Linux.

## 4. Análisis HTTP
El filtro `http.request.method == "POST"` identificó dos solicitudes POST a `/reviews/upload.php`.

La primera fue rechazada con HTTP 403 y la segunda aceptada con HTTP 200. El archivo aceptado utilizó el nombre `image.jpg.php`, lo que sugiere una posible evasión de la validación de archivos.

## 5. Archivo subido
Se observaron solicitudes a `/uploads/`, `/images/uploads/`, `/media/uploads/` y `/reviews/uploads/`.

Posteriormente se observó una solicitud a `/reviews/uploads/image.jpg.php`, relacionada con la actividad posterior de la captura.

## 6. Conexión inversa
La captura mostró tráfico desde el servidor `10.10.2.15` hacia `198.51.100.23` por TCP 8080.

La sesión mostró ejecución inicial como `www-data` (UID 33), información del sistema Linux y lectura de `/etc/passwd`.

## 7. Usuario y restablecimiento de contraseña
En `/etc/passwd` se identificó al usuario `pinguinito`.

La sesión ejecutó `sudo -l` y mostró autorización para utilizar `/opt/scripts/reset_user_pass.sh`. Después se observó la ejecución del script para restablecer la contraseña de `pinguinito`.

## 8. Inteligencia de amenazas
Las direcciones `198.51.100.23` y `203.0.113.99 pertenecen a rangos reservados para documentación. No deben considerarse direcciones reales verificadas de atacantes.

## 9. Acceso SSH y privilegios
Se debe registrar el acceso SSH y los privilegios de acuerdo con los resultados reales obtenidos en el laboratorio. La escalada a root solo debe afirmarse si se verificó efectivamente.

## 10. Conclusión
La evidencia muestra una posible falla en la validación de archivos subidos, la aceptación de un archivo con extensión doble, actividad compatible con una shell inversa y el uso de un script autorizado para restablecer la contraseña de otro usuario.

El análisis del PCAP permitió reconstruir los principales pasos del incidente.


