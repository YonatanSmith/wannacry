# 🦠 Cadena de Ataque del Ransomware WannaCry (Diagrama Mermaid)

Este diagrama ilustra las fases clave que utilizó el malware WannaCry durante su propagación y ataque en 2017.

```mermaid
flowchart LR
  WANNA CRY - CADENA DE ATAQUE
  %% ========================
  subgraph A [Acceso Inicial]
    direction TB
    A1["Explotación SMBv1 (EternalBlue)\nT1190 / MS17-010"]
  end

  subgraph B [Ejecución Remota]
    direction TB
    B1["Ejecución de código remoto vía SMB\nT1203"]
  end

  subgraph C [Persistencia]
    direction TB
    C1["Instalación de servicio / modificación del registro\nT1543 / T1060"]
  end

  subgraph D [Descubrimiento & Movimiento]
    direction TB
    D1["Escaneo de red (hosts con SMB)\nT1046"]
    D2["Movimiento lateral por SMB (EternalBlue)\nT1021.002"]
  end

  subgraph E [Impacto]
    direction TB
    E1["Cifrado masivo de ficheros (AES + RSA)\nT1486"]
    E2["Eliminación de Shadow Copies y restauración\nT1490"]
  end

  subgraph F [Extorsión]
    direction TB
    F1["Demanda de rescate en Bitcoin\nT1654"]
  end

  %% Flujo
  A1 --> B1
  B1 --> C1
  C1 --> D1
  D1 --> D2
  D2 --> E1
  E1 --> E2
  E2 --> F1

  %% Estilos
  classDef phase fill:#ffffff,stroke:#222,stroke-width:1px,font-size:14px,color:#000,padding:6px;
  class A1,B1,C1,D1,D2,E1,E2,F1 phase;

  style A fill:#f7c46b,stroke:#e07b00;
  style B fill:#c49bff,stroke:#8e44ad;
  style C fill:#a7d8ff,stroke:#1f78b4;
  style D fill:#b7f1b3,stroke:#27ae60;
  style E fill:#ffb3b3,stroke:#c0392b;
  style F fill:#ffd1a6,stroke:#d35400;

  linkStyle default stroke:#444,stroke-width:1.5px;
}
