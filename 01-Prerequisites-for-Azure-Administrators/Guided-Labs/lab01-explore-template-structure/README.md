# 📄 Lab 01: Exploración de la estructura de plantillas de ARM

## 🎯 Objetivo de la Unidad
Aprender la anatomía básica y la sintaxis declarativa de una plantilla de Azure Resource Manager (ARM) en formato JSON, comprendiendo el propósito de cada sección clave antes de desplegar recursos en la nube.

## 🧠 Conceptos Aprendidos (AZ-104)

### Sintaxis Declarativa vs. Imperativa
* **Imperativa (CLI / PowerShell):** Especifica la secuencia paso a paso de comandos a ejecutar para crear un recurso.
* **Declarativa (Plantillas ARM / Bicep):** Define el estado final deseado de la infraestructura sin preocuparse por la secuencia de comandos subyacente.

---

### 🦴 Anatomía de la Plantilla ARM (`azuredeploy.json`)

| Sección | Requerido | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `$schema` | **Sí** | Ubicación del archivo de esquema JSON que define la versión del lenguaje de la plantilla. | Validación de sintaxis e intellisense en VS Code. |
| `contentVersion` | **Sí** | Versión de la plantilla (ej. `1.0.0.0`). Útil para controlar cambios en el tiempo. | Control de versiones interno. |
| `parameters` | No | Valores que se pasan a la plantilla durante el despliegue para hacerla reutilizable en distintos entornos. | Nombre del entorno (`dev`, `prod`), credenciales. |
| `variables` | No | Valores calculados o fijos internamente que simplifican las expresiones de la plantilla. | Nombres complejos concatenados. |
| `resources` | **Sí** | **(Crítico para el examen)** Array donde se declaran los recursos físicos o lógicos de Azure que se van a crear. | Cuentas de almacenamiento, VNets, Máquinas Virtuales. |
| `outputs` | No | Valores que devuelve Azure tras completar el despliegue con éxito. | IP pública asignada, FQDN de un recurso. |

---

## 🛠️ Archivos en esta carpeta
* `azuredeploy.json`: Esqueleto básico de la plantilla declarativa ARM.