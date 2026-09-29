### 🛡️ Custom Challenge: Arquitectura Integral de Almacenamiento Seguro (SecOps, FinOps & HA)
**Objetivo:** Diseñar e implementar una infraestructura de almacenamiento unificada para una corporación aplicando controles Zero Trust, garantizando la inmutabilidad de la evidencia forense (WORM) y automatizando la optimización de costes.

#### 1. Resiliencia y Alta Disponibilidad (Core Architecture)
Para asegurar la continuidad del negocio frente a desastres regionales o ataques maliciosos, la cuenta base se aprovisionó con las máximas garantías de replicación y protección de datos:
*   **Redundancia (RA-GRS):** Almacenamiento con redundancia geográfica con acceso de lectura, garantizando un failover transparente y lectura ininterrumpida desde la región secundaria.
*   **Protección Anti-Ransomware:** Se habilitó la eliminación temporal (Soft Delete) con retención de 14 días y el control de versiones de blobs, asegurando la recuperación inmediata frente a sobrescrituras.

#### 2. Segmentación y Seguridad Zero Trust (Identidad y Red)
Se erradicaron los vectores de ataque tradicionales mediante la desactivación de métodos de autenticación heredados y el aislamiento perimetral:
*   **Identidad Exclusiva:** Se deshabilitó el acceso mediante claves de cuenta (Shared Key Access = Disabled), forzando el uso exclusivo de Microsoft Entra ID (RBAC).

![Configuración de Seguridad Zero Trust](./Images/storage-identity-hardening.png)

*   **Conectividad Controlada:** El acceso público fue denegado. Se configuraron *Service Endpoints* (`Microsoft.Storage`) para que la cuenta solo acepte tráfico proveniente de la red virtual autorizada (`vnet-management`).
*   *Nota de seguridad:* Ni siquiera los administradores globales pueden acceder a los datos desde fuera de la red de gestión (Error 403).

![Aislamiento de Red](./Images/storage-network-isolation.png)

#### 3. Bóveda Forense Inmutable (Compliance & WORM)
Los registros de auditoría y telemetría de seguridad requieren protección absoluta contra la manipulación:
*   **Bloqueo Legal (WORM):** Se implementó una directiva de retención basada en tiempo en el contenedor de auditoría (`soc-logs`), garantizando que la evidencia forense no pueda ser alterada ni eliminada (Write-Once, Read-Many) por ningún actor.

#### 4. Topología de Archivos Híbrida (Azure Files)
Se habilitó un espacio de trabajo centralizado para los equipos operativos sin la carga administrativa de mantener servidores IaaS:
*   **Recurso Compartido Seguro:** Despliegue de un File Share (`sec-tools`) optimizado para transacciones, accesible mediante el protocolo SMB 3.0 con cifrado en tránsito forzado.

#### 5. Optimización Financiera Automatizada (FinOps)
Para evitar el sobrecoste derivado de la retención a largo plazo de los registros forenses, se aplicó la automatización nativa del ciclo de vida:
*   **Tiering Automatizado:** Regla *Lifecycle Management* que evalúa los blobs del contenedor `soc-logs`. Si no han sido modificados en 30 días, se degradan al nivel Esporádico (Cool); a los 90 días, se transicionan de forma automática al nivel Archivo (Archive).

![Ciclo de Vida FinOps](./Images/storage-lifecycle-finops.png)

--------------------------------------------------------------------------------

*Laboratorio completado y recursos eliminados (FinOps). La arquitectura base cuenta ahora con segmentación estricta, protección contra ransomware y optimización de costes a largo plazo.*