# 🚀 Lab 02: Creación e Implementación de Plantillas ARM (Azure Resource Manager)

## 📋 Descripción del Laboratorio
Este laboratorio se centra en la aplicación del paradigma de **Infraestructura como Código (IaC)** en Microsoft Azure mediante plantillas ARM declarativas en formato JSON. El objetivo principal es desplegar recursos de forma automatizada, garantizando la idempotencia y el cumplimiento normativo.

---

## 🛠️ Desafíos Técnicos y Resolución (Troubleshooting Real)

Durante la ejecución de este laboratorio, nos enfrentamos y resolvimos escenarios reales de administración en la nube:

1. **Gestión de Entorno Local (Azure PowerShell):**
   - Configuración de la estación de trabajo local instalando y gestionando el módulo `Az` y sus dependencias (`Az.Resources`), adoptando el estándar corporativo frente al uso exclusivo de consolas web temporales.

2. **Restricciones por Azure Policies (Enterprise Governance):**
   - **Problema:** Los despliegues iniciales en regiones como `westeurope` y `eastus` fallaron debido a directivas de control de costes y capacidad aplicadas a la suscripción ("*Allowed resource deployment regions*").
   - **Auditoría:** Se investigó el cumplimiento normativo (*Compliance*) desde el Portal de Azure para identificar la lista blanca de regiones permitidas.
   - **Solución:** Se adaptó la arquitectura para desplegar los recursos en la región autorizada (`francecentral`) demostrando resiliencia y capacidad de adaptación a las políticas de seguridad de la organización.

---

## 🏗️ Arquitectura Desplegada

```mermaid
graph TD
    A[Estación Local / VS Code] -->|Azure PowerShell / ARM| B(Azure Resource Manager)
    B --> C{Resource Group: rg-az104-lab02-fc}
    C --> D[Storage Account: stg standard LRS]
    style C fill:#f9f,stroke:#333,stroke-width:2px
    style D fill:#bbf,stroke:#333,stroke-width:2px