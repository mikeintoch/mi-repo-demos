# Red Hat Connectivity Link (Kuadrant) - GitOps Demo Automatizada

Este repositorio contiene la automatización completa para desplegar una demo de **Red Hat Connectivity Link (RHCL)** (basado en el proyecto open-source **Kuadrant**) sobre **OpenShift**. 

La arquitectura utiliza **OpenShift Service Mesh v3 (Istio)** como base de red, exponiendo una API de pruebas que corre internamente en el puerto `8080`, protegida de extremo a extremo mediante políticas de tráfico avanzadas (**Rate Limiting**), cifrado en el borde (**TLS Edge**) y redirección automática de **HTTP a HTTPS**.

---

## 🏗️ Estructura del Repositorio

El proyecto está organizado de manera modular por componentes tecnológicos y utiliza **Argo CD Sync Waves** para garantizar que los operadores e infraestructura se instalen en el orden secuencial correcto:

```text
mi-repo-demos/
├── bootstrap/
│   └── rhcl-demo-app.yaml         # Aplicación raíz de Argo CD (Disparador)
└── charts/
    └── rhcl-demo/
        ├── Chart.yaml             # Metadatos del Helm Chart
        ├── values.yaml            # Variables globales y personalizables
        └── templates/
            ├── 01-service-mesh/
            │   ├── 01-operator-subscription.yaml  # Wave 1: Operador Service Mesh 3.x
            │   └── 02-istio-controlplane.yaml     # Wave 2: Plano de Control (Istio CR)
            └── 02-kuadrant/
                ├── 00-operator-subscription.yaml  # Wave 3: Operador de Kuadrant
                ├── 01-kuadrant-core.yaml          # Wave 4: Motor Central de Kuadrant
                ├── 02-gateway.yaml                # Wave 5: Gateway API (Puerto 80)
                ├── 03-api-deployment.yaml         # Wave 6: Contenedor API (Puerto 8080)
                ├── 04-api-service.yaml            # Wave 6: Servicio Mapeador (80 -> 8080)
                ├── 05-api-httproute.yaml          # Wave 6: Enrutamiento de Gateway API
                ├── 06-api-route.yaml              # Wave 6: OpenShift Route (TLS Edge + Redirect)
                └── 07-api-ratelimit.yaml          # Wave 6: Política de Rate Limit (RHCL)