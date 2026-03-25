🕵️‍♂️ # -3-SOC-Informe de incidentes: análisis de tráfico y descarga desde un sitio web de software falso.

🌎 Contexto
Trabajas como analista en un Centro de Operaciones de Seguridad (SOC). Alguien se comunica con tu equipo para informar que un compañero de trabajo descargó un archivo sospechoso después de buscar Google Authenticator. La persona que llama proporciona información similar a la que se encuentra en publicaciones de redes sociales en:

- https://www.linkedin.com/posts/unit42_2025-01-22-wednesday-a-malicious-ad-led-activity-7288213662329192450-ky3V/

- https://x.com/Unit42_Intel/status/1882448037030584611

Basándote en la información inicial de la persona que llamó, confirmas que hubo una infección. Recuperas una captura de paquetes (pcap) del tráfico asociado. Al revisar el tráfico, encuentras varios indicadores que coinciden con los detalles de una página de Github a la que se hace referencia en las publicaciones de redes sociales mencionadas anteriormente. Tras confirmar que se produjo una infección, comienzas a redactar un informe de incidentes.

El programa utilizado para analizar los paquetes fue Wireshark.🦈​

🌎​ Detalles del entorno

- Rango del segmento LAN: 10.1.17[.]0/24 (10.1.17[.]0 a 10.1.17[.]255)
- Dominio: bluemoontuesday[.]com
- Controlador de dominio de Active Directory (AD): 10.1.17[.]2 - WIN-GSH54QLW48D
- Nombre del entorno de AD: BLUEMOONTUESDAY
- Puerta de enlace del segmento LAN: 10.1.17[.]1
- Dirección de difusión del segmento LAN: 10.1.17[.]255

​🔎​ Hallazgos:

- Dirección IP infectada: 10.1.17.215
- Dirección MAC infectada: 00:d0:b7:26:4a:74
- Nombre de host infectado: DESKTOP-L8C5GSJ
- Nombre de cuenta de usuario de Windows infectado: shutchenson
- Sitio web falso para la descarga inicial del malware: authenticatoor.org
- Direcciones IP sospechosas: 5.252.153.241, 45.125.66.32, 45.125.66.252

⚠️​ Actividad: Un compañero de trabajo accedió a un sitio web con software malicioso (en este caso, un autenticador de Google falso), lo que confirmó la infección. Este archivo malicioso intenta realizar un ataque de comando y control (C2). En otras palabras, C2 es la infraestructura y el conjunto de técnicas que utilizan los atacantes para comunicarse de forma remota con los sistemas infectados dentro de una red objetivo y controlarlos. El archivo descargado es un medio para controlar remotamente nuestros sistemas.

🛜​ Comportamiento de la red: Primero, podemos confirmar el acceso a un sitio web falso, "authenticatoor.org", que incluso presenta errores tipográficos. Segundo, conexión inicial al sitio falso. Tercero, observamos conexiones persistentes con dos direcciones IP.

🧐 ​Proceso y filtros:
<img width="359" height="505" alt="image" src="https://github.com/user-attachments/assets/842ede30-59ba-4971-b94f-cdad80064ce4" />

<img width="524" height="131" alt="image" src="https://github.com/user-attachments/assets/7b2cc027-65a5-497f-a1a4-6283024b8c31" />

Con esta información podemos buscar coincidencias en el tráfico para identificar el paquete malicioso o sospechoso.

<img width="776" height="271" alt="image" src="https://github.com/user-attachments/assets/dd66550b-71fa-453b-9174-83a8fe56707e" />

Este paquete nos proporciona la dirección IP infectada, la dirección MAC infectada y la primera IP maliciosa.

<img width="685" height="91" alt="image" src="https://github.com/user-attachments/assets/87b38ca4-57e3-4968-8cdd-7840b64ec183" />

nbns && ip.addr == 10.1.17.215
- Con este filtro podemos averiguar el nombre de host infectado.

<img width="837" height="79" alt="image" src="https://github.com/user-attachments/assets/452e9142-b02d-4e92-94ef-2af7b64af713" />

Ahora buscamos el enlace malicioso, que hemos identificado como "authenticatoor.org".
- El atacante utiliza un dominio ".org" que no corresponde al propósito real de la página, ni al que aparenta tener.
- El atacante intenta ocultar el enlace duplicando una letra en la URL.

<img width="1600" height="119" alt="image" src="https://github.com/user-attachments/assets/9c74f19b-7a28-448a-80ac-fc6b226795f0" />

<img width="1057" height="149" alt="image" src="https://github.com/user-attachments/assets/e95d97fb-0314-41f0-9bd8-35d247c28185" />

La información de tráfico, que identifica varias direcciones IP, está disponible en el repositorio de GitHub. Posteriormente, se buscarán estas direcciones IP utilizando Wireshark.

<img width="1336" height="427" alt="image" src="https://github.com/user-attachments/assets/8dae9952-35b1-410e-b209-5098938d1925" />

<img width="1253" height="395" alt="image" src="https://github.com/user-attachments/assets/c983b257-6ee6-4e20-84cf-298ae616e061" />

😎​ Conclusión: Un compañero de trabajo buscó el autenticador oficial de Google y, sin darse cuenta, introdujo una URL maliciosa y descargó un archivo no oficial y malicioso, lo que inició la conexión con direcciones IP maliciosas que buscaban el control remoto de nuestros sistemas.

🛡️​​ Recomendaciones como profesional en ciberseguridad:
1) Contención
- Conservar la evidencia (por ejemplo, archivos .pcap y archivos descargados).
- Aislar el host infectado.
- Bloquear la comunicación con las IP maliciosas.

2) Investigación
- Realizar un análisis antivirus/EDR completo.
- Verificar mecanismos de persistencia.
- Revisar el historial del navegador y las cookies en el host .215.

3) Seguridad de credenciales
- Restablecer las credenciales de usuario.

4) Seguridad de red
- Monitorear patrones de tráfico similares.
- Implementar reglas de detección.

5) Refuerzo de seguridad
- Aplicar políticas de filtrado de salida.
- Implementar un proxy con inspección SSL.
- Segmentación de red (VLAN y subredes).
- Singular DNS.
- Capacitación de empleados.
- Permitir únicamente el acceso a sitios web y herramientas web oficiales de la empresa. 
