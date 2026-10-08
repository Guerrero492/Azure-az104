# AZ-104 Master Challenge: Arquitectura Híbrida Segura y Escalable

Este repositorio documenta el diseño y despliegue de una arquitectura de tres capas en Microsoft Azure, diseñada desde cero aplicando principios avanzados de **SecOps (Zero Trust)**, **FinOps (Optimización de costes)** y **Alta Disponibilidad**. El entorno integra servicios PaaS, CaaS e IaaS para construir una solución corporativa robusta y resiliente.

| Capa | Servicio Azure | Foco Principal | Estrategia Implementada |
| :--- | :--- | :--- | :--- |
| **Frontend** | App Service | Seguridad Perimetral | TLS/HTTPS Only (Cifrado forzado) |
| **Backend** | Container Instances | Zero Trust | VNet Privada delegada, Sin IP Pública |
| **Computación** | VMSS | FinOps & Escalabilidad | Autoescalado paramétrico basado en CPU |

## Fase 1: Frontend PaaS Seguro (Azure App Service)
El punto de entrada de la aplicación se aloja en un entorno de plataforma como servicio (PaaS) bastionado para garantizar comunicaciones cifradas de extremo a extremo, bloqueando tráfico vulnerable por diseño.

*   **Implementación:** Aprovisionamiento de aplicación web en plan Standard (S1) preparado para intercambios de ranuras (*deployment slots*) sin tiempo de inactividad.
*   **Bastionado SecOps:** Activación estricta de la directiva `HTTPS Only` para rechazar peticiones HTTP no seguras, mitigando los riesgos de interceptación de credenciales o de sesión.
*   **Evidencia:** `![Frontend HTTPS Bastionado](frontend-swap-https.png)`

## Fase 2: Backend CaaS Aislado (Azure Container Instances)
Para proteger la lógica de negocio y las futuras conexiones a bases de datos, el microservicio de backend se despliega aplicando un modelo de confianza cero (Zero Trust), eliminando por completo su exposición a la red pública.

*   **Implementación:** Despliegue de un contenedor ágil sobre infraestructura sin servidor (CaaS).
*   **Aislamiento de Red:** Inyección directa del contenedor mediante delegación de subred (`Microsoft.ContainerInstance/containerGroups`) dentro de una Virtual Network (VNet) corporativa dedicada.
*   **SecOps:** Asignación exclusiva de direccionamiento IP privado (rango `10.0.x.x`) sin un FQDN expuesto, garantizando que el servicio sea indetectable e inaccesible desde el exterior del perímetro de Azure.
*   **Evidencia:** `![Backend Aislado Zero Trust](backend-aci-private.png)`

## Fase 3: Procesamiento IaaS Elástico (Virtual Machine Scale Sets)
Las cargas de trabajo asíncronas de mayor exigencia de cómputo se absorben mediante un clúster IaaS orquestado de manera elástica, protegiendo el presupuesto operativo al adaptar los recursos a la demanda real.

*   **Implementación:** Configuración de un clúster de servidores Linux (Ubuntu 22.04 LTS) en modo de orquestación flexible, balanceado internamente (Internal Load Balancer) para no comprometer el aislamiento perimetral.
*   **Políticas FinOps (Autoescalado):**
    *   *Scale-Out (Aumento):* Despliegue automático de 1 instancia adicional si el consumo medio de CPU supera el 75% durante una ventana de 5 minutos.
    *   *Scale-In (Reducción):* Destrucción automática de 1 instancia si la CPU desciende por debajo del 30% durante 5 minutos, garantizando la liberación de recursos ociosos.
*   **Evidencia:** `![Reglas de Autoescalado VMSS](iaas-vmss-autoscale.png)`

## Resolución de Incidentes: Límites de Capacidad y Políticas IaaS
Durante el aprovisionamiento de la capa IaaS, la API de Azure Resources (ARM) devolvió un bloqueo de validación (`disallowed by Azure`) asociado a restricciones de política de ubicación.

*   **Diagnóstico:** Restricción dinámica de cuota de vCPU aplicada por Microsoft en centros de datos con alta densidad de demanda (común en suscripciones controladas o de desarrollo como *Azure for Students*).
*   **Solución Corporativa:** En un escenario de producción, este incidente de despliegue se resuelve escalando un ticket de soporte oficial (Request Quota Increase) para ampliar el límite de núcleos en la región `West Europe` o `France Central`, o bien distribuyendo las instancias del Scale Set a través de múltiples Zonas de Disponibilidad en regiones emparejadas (*paired regions*) pre-aprobadas para la suscripción.