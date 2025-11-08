# 📌 Mapeo MITRE ATT&CK & Detección sugerida — WannaCry

> Este documento relaciona las etapas clave del ataque WannaCry con las técnicas MITRE ATT&CK correspondientes y sugiere controles, reglas y consultas que puedes implementar en EDR, SIEM, IDS/IPS y herramientas de monitoreo.

---

## 🧭 Resumen rápido
- **Nombre del caso:** WannaCry (WanaCrypt0r 2.0)  
- **Vectores principales:** Exploit SMBv1 (MS17-010 / EternalBlue), movimiento lateral SMB, cifrado masivo (AES/RSA), inhibición de recuperación (Shadow Copies), nota de rescate.  
- **Objetivo del documento:** Proveer técnicas MITRE mapeadas + ideas de detección y respuestas operativas.

---

## 🔗 Tabla: Etapa → MITRE → Detección sugerida

| Etapa del ataque | MITRE (Táctica) | Técnica MITRE | Detección sugerida |
|------------------|------------------|---------------:|--------------------|
| **Explotación remota (EternalBlue)** | Initial Access | **T1190 - Exploit Public-Facing Application** | - IDS/IPS: firma EternalBlue. <br>- Alerta en SIEM por tráfico SMB (TCP/445) desde IP externas o patrones de exploit. <br>- Nmap `smb-vuln-ms17-010` scan results como IOC. |
| **Ejecución remota / ejecución del payload** | Execution | **T1203 - Exploitation for Client Execution** | - Creación de procesos anómalos por servicios de red (p. ej. procesos hijos de `svchost.exe` con ejecución inusual). <br>- EDR: alertar por ejecución desde directorios temporales (`%TEMP%`, `%APPDATA%`). |
| **Movimiento lateral (SMB)** | Lateral Movement | **T1021.002 - SMB/Windows Admin Shares** | - Detección de picos de conexiones SMB desde una sola estación. <br>- Alertas por cuentas que acceden a >N shares en corto periodo. <br>- Monitor de creación de sesiones SMB (EventID 5140 / 4624 correlado con uso de servicio de red). |
| **Instalación de loader/backdoor** | Persistence | **T1543 - Create or Modify System Process** | - Modificaciones de servicios, nuevas tareas programadas o servicios con nombres aleatorios. <br>- EDR: hashes de binarios nuevos ejecutándose como servicio. |
| **Cifrado masivo de archivos** | Impact | **T1486 - Data Encrypted for Impact** | - Patrón de acceso/WRITE masivo a archivos (miles de operaciones de I/O en corto tiempo). <br>- Cambios masivos de extensiones (p.ej `.WNCRY`). <br>- Alertas EDR por comportamiento de "ransomware" (modelo ML o reglas heurísticas). |
| **Inhibición de recuperación** | Impact | **T1490 - Inhibit System Recovery** | - Eventos que borran shadow copies (`vssadmin delete shadows`), cambios en servicios VSS y llamadas a `wbadmin`. <br>- Alertas en logs de PowerShell por comandos que manipulan VSS. |
| **Extorsión / demanda de rescate** | Impact | **T1489 - Data Destruction / Ransom Demand** | - Detección de archivos-nota de rescate (`*@Please_Read_Me@.txt`, patrones de texto conocido). <br>- Monitor conexiones a dominios/URLs usados por campañas (si están disponibles). |

---

## 🛠️ Controles y sensores recomendados (por tecnología)

### EDR / Endpoint
- Reglas para detectar:
  - Creación de procesos desde `%TEMP%`, `%APPDATA%`, o rutas no habituales.  
  - Acceso masivo en poco tiempo a archivos del usuario (alto ratio lectura/escritura).  
  - Eliminación de shadow copies (`vssadmin.exe delete shadows /all /quiet`).
- Bloqueo preventivo de binarios no firmados que intenten ejecutar con privilegios.

### SIEM (excepciones y consultas)
- **Detección: múltiples accesos SMB**
  - Splunk (ejemplo):
    ```splunk
    index=windows sourcetype=WinEventLog:Security (EventCode=5140 OR EventCode=4624)
    | stats count by src_ip, dest_share
    | where count > 50
    ```
- **Detección: creación masiva de archivos con nueva extensión**
  - Elastic/ELK (pseudoconsulta):
    ```sql
    SELECT host, count(*) as writes
    FROM file_events
    WHERE event.type = 'creation' AND file.extension = 'WNCRY'
    GROUP BY host
    HAVING writes > 10
    ```
- **Detección: comandos de VSS/admin**
  - Kibana/ELK:
    ```kql
    process.name: "vssadmin.exe" AND process.args: "delete shadows"
    OR process.name: "wbadmin.exe" AND process.args: "*delete*"
    ```

### Network IDS / IPS
- Firmas específicas para **EternalBlue / DoublePulsar** (actualizadas).  
- Alertas por escaneo masivo a puerto TCP/445 (thresholds configurables).  
- Bloqueo de tráfico SMB entrante desde Internet en perímetro.

### Firewalls / Perímetro
- Bloquear **TCP/445** a/desde Internet.


