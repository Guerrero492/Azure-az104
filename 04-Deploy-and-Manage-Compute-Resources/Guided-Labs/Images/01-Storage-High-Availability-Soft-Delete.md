### 🌐 Custom Challenge: Almacenamiento de Alta Disponibilidad y Resiliencia para Web

**Objetivo:** Diseñar e implementar una cuenta de almacenamiento en Azure para servir contenido multimedia estático a nivel mundial, garantizando alta disponibilidad ante desastres regionales y aplicando políticas de resiliencia (Soft Delete y Versionado) para proteger los activos web contra borrados accidentales o sobrescrituras maliciosas.

#### 1. Arquitectura de Alta Disponibilidad (RA-GRS)
Para asegurar que los recursos de la página web estén siempre accesibles, incluso si un centro de datos completo falla, se ha configurado la replicación geográfica con acceso de lectura.
*   **Cuenta de Almacenamiento:** `jmpublicwebsite` (SKU Estándar).
*   **Redundancia:** Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS). Los datos se replican sincrónicamente en la región principal (`France Central`) y asincrónicamente en una región secundaria, permitiendo la lectura ininterrumpida desde la secundaria en caso de caída.

#### 2. Exposición Controlada (Acceso Anónimo Perimetral)
El diseño exige que los clientes a nivel mundial puedan cargar imágenes y vídeos sin autenticarse, limitando la exposición al mínimo indispensable.
*   **A nivel de cuenta:** Se modificó la directiva de seguridad para **Permitir el acceso anónimo al blob**.
*   **A nivel de contenedor (`public`):** Se restringió el acceso exclusivamente al nivel **Blob (acceso de lectura anónimo solo para blobs)**. Esto permite consumir un archivo si se conoce su URL exacta, pero bloquea cualquier intento de listar el contenido del directorio, protegiendo la estructura de datos frente a escaneos externos.

#### 3. Políticas de Resiliencia y Recuperación de Datos
Para blindar el repositorio contra errores humanos o ataques de integridad, se habilitaron dos capas nativas de protección de datos:
*   **Eliminación Temporal (Soft Delete):** Configurada con un período de retención de **21 días**. Los archivos eliminados pasan a un estado oculto recuperable en lugar de destruirse instantáneamente.
*   **Control de Versiones de Blobs:** Activado para mantener un historial inmutable de las modificaciones. Si un archivo es sobrescrito, la versión anterior permanece accesible para su restauración inmediata.

#### 4. Evidencias de Auditoría y Recuperación
*(Añadir aquí las capturas correspondientes a la auditoría de eliminación temporal y recuperación)*

*   **Evidencia 1: Validación de Eliminación Temporal**
    ![Soft Delete Validation](./Images/soft-delete-active.png)
    *Descripción: Filtro de blobs activos y eliminados mostrando el archivo temporalmente retenido con sus días de expiración restantes.*

*   **Evidencia 2: Restauración de Activos**
    ![Undelete Action](./Images/blob-recovery.png)
    *Descripción: Panel de propiedades del blob eliminado evidenciando el botón de recuperación para restaurar el servicio inmediatamente sin pérdida de datos.*

