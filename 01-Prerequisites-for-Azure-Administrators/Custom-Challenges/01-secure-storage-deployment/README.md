# 🛡️ Custom Challenge 01: Secure-by-Design Storage Deployment

## 📋 Objetivo del Reto
Diseñar e implementar una plantilla ARM personalizada aplicando el principio de **Seguridad desde el Diseño (Secure-by-Design)**. El objetivo es provisionar una cuenta de almacenamiento en Azure que nazca blindada contra configuraciones vulnerables mediante la automatización.

## 🔒 Controles de Ciberseguridad Implementados
- **Cifrado en Tránsito Forzado:** Configuración de la propiedad `supportsHttpsTrafficOnly: true` para rechazar cualquier petición HTTP no cifrada a nivel de plataforma.
- **Prevención de Exposición de Datos:** Implementación del parámetro booleano `allowPublicAccess` con valor predeterminado `false`, enlazado a la propiedad `allowBlobPublicAccess`, bloqueando el acceso anónimo de forma predeterminada.
- **Restricción de Cumplimiento (Compliance):** Uso de `allowedValues` en el parámetro de SKU (`environmentType`) para prevenir la creación de arquitecturas no autorizadas y controlar los costes.

## 🚀 Despliegue y Validación (PowerShell)
```powershell
New-AzResourceGroupDeployment `
  -ResourceGroupName "rg-az104-lab02-fc" `
  -Name "secure-storage-08-25-2026" `
  -TemplateFile azuredeploy.json `
  -storageAccountName "stgsec2026"