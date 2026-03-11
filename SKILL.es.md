name: rapid-architect
version: 1.0.0
description: Arquitecto impulsado por IA para tareas de DevOps, automatizando el diseño de infraestructura, pipelines de despliegue y optimización de recursos en la nube
author: Equipo OpenClaw
tags: [devops, ia, automatización, infraestructura, nube, cicd]
dependencies: [docker, terraform, ansible, kubectl, awscli, gcloud]
environment_vars:
  - RAPID_ARCHITECT_API_KEY: Requerida para acceso al modelo de IA
  - CLOUD_PROVIDER: aws|gcp|azure (predeterminado: aws)
  - INFRA_MODE: basic|advanced|enterprise (predeterminado: basic)
---

# Habilidad Rapid Architect

## Propósito

Rapid Architect es un arquitecto de DevOps impulsado por IA que automatiza el diseño e implementación de soluciones de infraestructura. Se destaca en transformar requisitos de alto nivel en configuraciones de DevOps listas para producción, enfocándose en arquitecturas nativas de la nube, pipelines de CI/CD e infraestructura como código.

### Casos de Uso Reales

- **Migración de Microservicios**: Diseña automáticamente manifiestos de Kubernetes y mallas de servicios para migrar aplicaciones monolíticas a arquitectura de microservicios
- **Optimización de Costos Multi-Nube**: Analiza el uso actual de la nube en AWS, GCP y Azure para recomendar asignación óptima de recursos y estrategias de ahorro de costos
- **Pipelines de Despliegue sin Tiempo de Inactividad**: Crea flujos de trabajo GitOps con ArgoCD y Flux para despliegues blue-green
- **Infraestructura Mejorada en Seguridad**: Implementa configuraciones VPC compatibles con CIS con reglas de grupo de seguridad automatizadas y configuraciones WAF
- **Diseño de Arquitectura Serverless**: Genera plantillas CloudFormation/SAM para pilas API Gateway + Lambda + DynamoDB con roles IAM apropiados
- **Escalado de Orquestación de Contenedores**: Diseña políticas de escalado horizontal de pods y configuraciones de escalado de clúster para aplicaciones de alto tráfico

## Alcance

### Comandos Soportados

- `rapid-architect design [blueprint]` - Genera planos de infraestructura a partir de descripciones en lenguaje natural
- `rapid-architect deploy --provider=aws --env=prod` - Despliega la infraestructura generada al proveedor de nube especificado
- `rapid-architect optimize --target=cost --budget=5000` - Analiza y optimiza la infraestructura existente para costo/rendimiento
- `rapid-architect audit --compliance=cis --output=json` - Audita la infraestructura contra estándares de seguridad/cumplimiento
- `rapid-architect migrate --source=monolith --target=k8s` - Genera planes de migración y scripts
- `rapid-architect monitor --alerts=slack --threshold=80` - Configura dashboards de monitoreo y reglas de alerta

### Soporte de Entorno

- AWS (EC2, ECS, EKS, Lambda, S3, RDS)
- GCP (GCE, GKE, Cloud Functions, BigQuery)
- Azure (VMs, AKS, Functions, CosmosDB)
- Kubernetes (vanilla, EKS, GKE, AKS)
- Docker Compose y Swarm
- Terraform y CloudFormation

## Proceso de Trabajo

### Paso 1: Análisis de Requisitos
- Analiza la entrada en lenguaje natural para necesidades de infraestructura
- Identifica servicios de nube requeridos, requisitos de escalado y necesidades de cumplimiento
- Genera gráfico de dependencias y estimación de recursos

### Paso 2: Generación de Plano
- Crea plantillas Terraform/CloudFormation con nomenclatura apropiada de recursos
- Implementa mejores prácticas de seguridad (IAM de menor privilegio, almacenamiento encriptado)
- Agrega integraciones de monitoreo (CloudWatch, Stackdriver, Application Insights)

### Paso 3: Validación y Pruebas
- Ejecuta comandos `terraform validate` y `plan`
- Simula despliegue en entorno aislado
- Verifica cumplimiento con estándares elegidos (SOC2, HIPAA, PCI-DSS)

### Paso 4: Ejecución de Despliegue
- Aplica cambios de infraestructura con gestión de estado apropiada
- Configura pipelines de CI/CD para despliegues futuros
- Configura copias de seguridad automatizadas y recuperación de desastres

### Paso 5: Optimización y Monitoreo
- Implementa etiquetas de asignación de costos y alertas de presupuesto
- Configura políticas de escalado automático basadas en patrones de uso
- Establece dashboards de monitoreo con indicadores clave de rendimiento

## Reglas de Oro

1. **Nunca despliegues en producción sin validación de dry-run** - Siempre ejecuta `terraform plan` antes de `apply`
2. **Implementa acceso de menor privilegio** - Genera políticas IAM que otorguen permisos mínimos requeridos
3. **Encripta datos en reposo y en tránsito** - Usa claves KMS para encriptación de almacenamiento y TLS 1.3 para todas las comunicaciones
4. **Controla versiones de todo el código de infraestructura** - Almacena estados de Terraform en S3/GCS con bloqueo habilitado
5. **Monitorea todo, alerta en anomalías** - Implementa logging comprehensivo y configura integraciones de PagerDuty
6. **Optimización de costos sobre crecimiento de características** - Prioriza uso eficiente de recursos sobre capacidades innecesarias
7. **Redes de cero confianza** - Implementa segmentación de red con grupos de seguridad y reglas de firewall
8. **Infraestructura inmutable** - Diseña para aplicaciones contenerizadas, sin estado con actualizaciones rodantes

## Ejemplos

### Ejemplo 1: Diseñando una Arquitectura de Microservicios

**Entrada:**
```
rapid-architect design \"Crea una arquitectura de microservicios para una plataforma de e-commerce con servicio de autenticación de usuario, catálogo de productos, gestión de pedidos y procesamiento de pagos. Usa AWS EKS, PostgreSQL RDS, caché Redis e implementa despliegues blue-green\"
```

**Salida:**
```yaml
# Generated infrastructure.yaml
version: '3.8'
services:
  auth-service:
    image: auth-service:latest
    environment:
      - DB_HOST=postgres.rds.amazonaws.com
      - REDIS_URL=redis.elasticache.amazonaws.com
    deploy:
      replicas: 3
      resources:
        limits:
          memory: 512M
        reservations:
          memory: 256M
    networks:
      - microservices-net

  product-service:
    image: product-service:latest
    environment:
      - DB_HOST=postgres.rds.amazonaws.com
    deploy:
      replicas: 5
    networks:
      - microservices-net

networks:
  microservices-net:
    driver: overlay

# Generated terraform/main.tf
resource \"aws_eks_cluster\" \"microservices\" {
  name     = \"microservices-cluster\"
  role_arn = aws_iam_role.eks_cluster.arn
  vpc_config {
    subnet_ids = aws_subnet.private[*].id
  }
}

resource \"aws_db_instance\" \"postgres\" {
  allocated_storage    = 20
  engine               = \"postgres\"
  engine_version       = \"13.7\"
  instance_class       = \"db.t3.micro\"
  name                 = \"microservices_db\"
  username             = \"admin\"
  password             = var.db_password
  parameter_group_name = \"default.postgres13\"
  skip_final_snapshot  = true
  vpc_security_group_ids = [aws_security_group.rds.id]
}
```

### Ejemplo 2: Análisis de Optimización de Costos

**Entrada:**
```
rapid-architect optimize --target=cost --budget=3000 --provider=aws --region=us-east-1
```

**Salida:**
```
=== Informe de Optimización de Costos ===
Costo Mensual Actual: $4,250
Presupuesto Objetivo: $3,000
Potencial de Optimización: 29%

Recomendaciones:
1. Redimensionar instancias EC2: t3.large → t3.medium (Ahorro $450/mes)
2. Usar instancias Spot para entornos de desarrollo (Ahorro $300/mes)
3. Implementar S3 Intelligent Tiering (Ahorro $200/mes)
4. Instancias Reservadas para cargas de trabajo de producción (Ahorro $600/mes)

Generado terraform/optimization.tf:
resource \"aws_instance\" \"web_server\" {
  ami           = \"ami-0c55b159cbfafe1d0\"
  instance_type = \"t3.medium\"  # Cambiado de t3.large
  # ... otra configuración
}
```

### Ejemplo 3: Auditoría de Seguridad

**Entrada:**
```
rapid-architect audit --compliance=cis --output=json --provider=aws
```

**Salida:**
```json
{
  \"audit_id\": \"cis-aws-2024-001\",
  \"compliance_level\": \"CIS AWS Foundations Benchmark v1.5\",
  \"total_checks\": 45,
  \"passed\": 38,
  \"failed\": 7,
  \"critical_failures\": 2,
  \"recommendations\": [
    {
      \"check_id\": \"1.3\",
      \"severity\": \"HIGH\",
      \"description\": \"Asegura que las credenciales no utilizadas por 90 días o más estén deshabilitadas\",
      \"resource\": \"iam_user.stale_user\",
      \"remediation\": \"aws iam delete-access-key --user-name stale_user --access-key-id AKIOLDKEY123\"
    },
    {
      \"check_id\": \"2.1.1\",
      \"severity\": \"MEDIUM\",
      \"description\": \"Asegura que la política de bucket S3 permita solo solicitudes HTTPS\",
      \"resource\": \"aws_s3_bucket.public_bucket\",
      \"remediation\": \"Agregar condición a la política de bucket: {\\"StringEquals\\": {\\"aws:SecureTransport\\": \\"true\\"}}\"
    }
  ]
}
```

## Comandos de Reversión

### Reversión de Infraestructura
- `terraform destroy -target=aws_instance.web_server` - Remover recurso específico
- `kubectl rollout undo deployment/auth-service --to-revision=2` - Revertir despliegue de Kubernetes
- `aws cloudformation delete-stack --stack-name microservices-stack` - Eliminar pila completa de CloudFormation

### Reversión de Configuración
- `git revert HEAD~3..HEAD` - Revertir últimos 3 commits de infraestructura
- `ansible-playbook rollback.yml -i inventory/prod` - Ejecutar playbook de reversión
- `kubectl apply -f backup-configmap.yaml` - Restaurar configuración anterior

### Recuperación de Estado
- `terraform state mv aws_instance.old aws_instance.new` - Renombrar recursos en estado
- `kubectl get configmap app-config -o yaml > backup.yaml` - Hacer backup de configuración actual
- `aws s3 cp s3://terraform-state-backup/state.tfstate .terraform/terraform.tfstate` - Restaurar estado desde backup

## Dependencias y Requisitos

### Requisitos del Sistema
- Docker Engine 20.10+
- Terraform 1.5.0+
- Ansible 2.15+
- kubectl 1.27+
- Python 3.9+ con bibliotecas boto3, kubernetes y terraform-python

### Acceso a API
- Herramientas CLI de proveedor de nube configuradas con permisos apropiados
- Clave API de modelo de IA para sugerencias de arquitectura
- Integraciones de servicios de monitoreo (Datadog, New Relic, Prometheus)

### Requisitos de Red
- Acceso HTTPS saliente a APIs de proveedor de nube
- VPN o conexión directa para recursos de nube privada
- Resolución DNS para descubrimiento de servicios internos

## Pasos de Verificación

1. **Validación de Sintaxis**: Ejecuta `terraform validate` y `terraform fmt` en código generado
2. **Revisión de Plan**: Ejecuta `terraform plan` y revisa cambios propuestos
3. **Estimación de Costos**: Usa herramienta `infracost` para estimar costos de despliegue
4. **Escaneo de Seguridad**: Ejecuta `checkov` o `terrascan` para violaciones de política
5. **Pruebas de Integración**: Despliega a entorno de staging y ejecuta pruebas automatizadas
6. **Benchmarking de Rendimiento**: Usa `terraformer` para establecer línea base de infraestructura actual
7. **Auditoría de Cumplimiento**: Genera reportes usando políticas de `cloud-custodian`

## Solución de Problemas

### Problemas Comunes

**Problema: Errores de bloqueo de estado de Terraform**
```
Error: Error acquiring the state lock
```
**Solución:** Fuerza desbloqueo con `terraform force-unlock LOCK_ID` después de verificar que no hay otros procesos ejecutándose

**Problema: Servidor API de Kubernetes inalcanzable**
```
Unable to connect to the server: dial tcp: lookup api.k8s.cluster.local on 8.8.8.8:53: no such host
```
**Solución:** Actualiza kubeconfig con endpoint correcto de clúster y autentica con `aws eks update-kubeconfig`

**Problema: Limitación de tasa de proveedor de nube**
```
Error: Request rate exceeded
```
**Solución:** Implementa retroceso exponencial en scripts de despliegue o usa flags `--max-retries=10`

**Problema: Sobrepasos de costos a pesar de optimización**
```
Factura mensual: $5,200 (Objetivo: $3,000)
```
**Solución:** Habilita alertas de AWS Budgets e implementa `aws-nuke` para limpieza de recursos no utilizados

**Problema: Fallos de despliegue en CI/CD**
```
Pipeline failed: Resource creation timeout
```
**Solución:** Incrementa valores de timeout en configuraciones de Terraform e implementa lógica de reintento con provisionadores `local-exec`

**Problema: Configuraciones erróneas de grupo de seguridad**
```
Error: Invalid security group rules
```
**Solución:** Usa `aws ec2 describe-security-groups` para auditar reglas y regenera con bloques CIDR apropiados