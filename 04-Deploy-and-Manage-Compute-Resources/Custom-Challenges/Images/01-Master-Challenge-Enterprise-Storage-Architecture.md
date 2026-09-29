### 🏆 Custom Challenge: Arquitectura Integral de Almacenamiento Seguro (SecOps, FinOps & HA)

**Objetivo:** Diseñar e implementar una infraestructura de almacenamiento unificada para una corporación. La solución resuelve tres necesidades críticas: servir activos web con alta disponibilidad global, proporcionar un recurso compartido híbrido para herramientas internas, y establecer una bóveda forense inmutable para los registros del equipo de seguridad, aplicando estrictos controles Zero Trust y optimización de costes.

#### 1. Resumen Arquitectónico
*   **Alta Disponibilidad (HA):** Despliegue en modo RA-GRS para garantizar la continuidad de lectura frente a caídas regionales completas.
*   **Identidad y Zero Trust:** Acceso público de red cerrado y autenticación por claves maestras deshabilitada. Todo el tráfico se enruta obligatoriamente a través de una red virtual privada (VNet) y se autentica mediante Microsoft Entra ID (RBAC).
*   **Cumplimiento WORM:** Aplicación de directivas de inmutabilidad (Time-based retention) para evitar que ransomware o atacantes internos puedan sobrescribir evidencias forenses.
*   **FinOps:** Reglas de ciclo de vida (Lifecycle Management) para el almacenamiento en niveles (Tiering) automático, degradando datos fríos a la capa de Archivo.

---

#### 2. Guía de Reproducción (Runbook Operativo)

**Fase 1: Aprovisionamiento Base y Hardening de Identidad**
1. **Crear Grupo de Recursos:** Desplegar `rg-master-storage-secops` en la región principal.
   * *El porqué:* Establece el perímetro de seguridad y facturación para agrupar los componentes del proyecto.
2. **Crear Cuenta de Almacenamiento (Redundancia):** Seleccionar **RA-GRS**.
   * *El porqué:* La redundancia geográfica con acceso de lectura mantiene los datos vivos en una región secundaria si el centro de datos principal sufre un desastre.
3. **Bloquear Claves de Acceso:** En la pestaña Avanzado > Seguridad, desmarcar **Permitir el acceso a la clave de la cuenta de almacenamiento**.
   * *El porqué:* Las claves maestras otorgan control total. Al bloquearlas, forzamos el uso de identidades modernas (Entra ID / RBAC), aplicando el principio de privilegio mínimo y evitando el robo de credenciales estáticas.
4. **Habilitar Bóveda Anti-Ransomware:** En Protección de datos, activar la eliminación temporal (Soft Delete) a 14 días y el control de versiones.
   * *El porqué:* Permite restaurar datos de forma inmediata si un atacante logra sobrescribir o borrar información crítica.

**Fase 2: Estructuración y Aislamiento Perimetral**
1. **Crear Contenedor Privado:** Crear contenedor `soc-logs` con acceso anónimo deshabilitado.
   * *El porqué:* Será la bóveda lógica para almacenar los registros de seguridad sin exposición pública.
2. **Crear Recurso Compartido:** Crear un Azure File Share llamado `sec-tools`.
   * *El porqué:* Permite al equipo de seguridad montar herramientas en sus equipos como si fuera un disco local (ej. Z:\) usando el protocolo estándar SMB.
3. **Crear VNet y Service Endpoint:** Desplegar `vnet-management` y habilitar el punto de conexión `Microsoft.Storage` en su subred.
   * *El porqué:* Permite que el tráfico originado en esta red viaje hacia Azure Storage de forma optimizada y etiquetada por la red troncal de Microsoft.
4. **Cerrar Firewall de Storage:** En las opciones de Red, denegar el acceso público y permitir únicamente el tráfico desde `vnet-management`.
   * *El porqué:* Ejecuta el modelo Zero Trust. La cuenta se vuelve invisible para Internet; solo los equipos internos autorizados a nivel de red pueden comunicarse con ella.

**Fase 3: Inmutabilidad (WORM) y FinOps**
1. **Aplicar Retención WORM:** En el contenedor `soc-logs`, añadir una directiva de retención con un límite de tiempo.
   * *El porqué:* Convierte los registros en inmutables a nivel de infraestructura. Nadie puede borrarlos o alterarlos durante ese período, garantizando la validez forense de la evidencia.
2. **Crear Regla de Ciclo de Vida (FinOps):** Configurar una regla que mueva los blobs al nivel Esporádico a los 10/30 días, y al nivel Archivo a los 90 días.
   * *El porqué:* Retener evidencia legal durante años es costoso. Esta automatización traslada los registros antiguos a capas más baratas (hasta fracciones de céntimo por GB) sin intervención humana.

---

#### 3. Evidencias de Despliegue
*(Añadir aquí las capturas de pantalla de tu laboratorio)*
*   **Evidencia 1:** Configuración de VNet y bloqueo de IP pública.
*   **Evidencia 2:** Regla de ciclo de vida automatizada (FinOps) con condiciones de 10 y 90 días.