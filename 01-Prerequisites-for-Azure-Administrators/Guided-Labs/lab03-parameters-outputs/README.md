# 🚀 Lab 03: Plantillas ARM Flexibles con Parámetros y Salidas

## 📋 Objetivo
Evolucionar la plantilla estática de infraestructura hacia un modelo dinámico y reutilizable mediante el uso de parámetros, validaciones de datos y extracción de salidas (*outputs*), aplicando las mejores prácticas de Azure Resource Manager (ARM).

## 🛠️ Conceptos y Resoluciones Técnicas
- **Parametrización Dinámica:** Inyección de valores en tiempo de ejecución (`storageName`, `storageSKU`) para permitir la reutilización de la plantilla en múltiples entornos de despliegue (Dev, Test, Prod).
- **Security & Compliance (Validación de Entradas):** Uso de restricciones de longitud (`minLength`, `maxLength`) y listas blancas de cumplimiento (`allowedValues`) para prevenir errores de configuración (*misconfigurations*) bloqueando recursos no autorizados antes de que toquen la nube.
- **Control de Errores Práctico:** Demostración del rechazo de la API de Azure al intentar inyectar una SKU no autorizada (`Basic`), validando la integridad del código.
- **Extracción de Estado (Outputs):** Recuperación dinámica de los *Endpoints* generados por Azure para interconectar arquitecturas mediante flujos CI/CD.

## 🚀 Comando de Despliegue Exitoso
```powershell
New-AzResourceGroupDeployment `
  -ResourceGroupName "rg-az104-lab02-fc" `
  -Name "addOutputs-08-25-2026" `
  -TemplateFile azuredeploy.json `
  -storageName "stgparam2026" `
  -storageSKU "Standard_LRS"