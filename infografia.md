# 💀 Infografía Técnica: Ransomware WannaCry

> **WannaCry (WanaCrypt0r 2.0)** — uno de los ataques de ransomware más devastadores de la historia, con propagación automática a través de redes Windows mediante la vulnerabilidad **EternalBlue (MS17-010)**.

---

## 🧩 Resumen general

| Característica | Detalle |
|----------------|----------|
| 🕒 **Año de aparición** | 2017 |
| 🧠 **Tipo de malware** | Ransomware con capacidad de gusano (worm) |
| 💻 **Sistemas afectados** | Microsoft Windows (XP → Server 2016) |
| 🧰 **Vulnerabilidad explotada** | SMBv1 - MS17-010 (EternalBlue) |
| 💣 **Método de propagación** | Automático por red (puerto TCP 445) |
| 🔐 **Algoritmos de cifrado** | AES-128 + RSA-2048 |
| 💰 **Rescate exigido** | 300–600 USD en Bitcoin |
| 📂 **Extensión de archivos cifrados** | `.WNCRY` o `.WNCRYT` |
| 🧱 **Detención global** | “Kill switch” activado por MalwareTech |
| 🌍 **Equipos afectados** | +230,000 en más de 150 países |

---

## ⚙️ Fases del ataque

1. **Infección inicial**  
   - El malware se ejecuta en un sistema Windows vulnerable.  
   - Aprovecha la falla SMBv1 (EternalBlue) para obtener ejecución remota.

2. **Propagación automática**  
   - Escanea direcciones IP en la red local.  
   - Explota la misma vulnerabilidad en otros equipos.  
   - Se replica sin interacción del usuario (comportamiento de gusano).

3. **Cifrado de archivos**  
   - Cifra documentos, imágenes, bases de datos, etc.  
   - Cambia extensiones a `.WNCRY`.  
   - Elimina copias de restauración (shadow copies).  
   - Muestra la nota de rescate:


