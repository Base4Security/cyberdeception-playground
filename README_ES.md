# Cyber Deception Playground

> **Versión en inglés (principal)**: [README.md](README.md)

Este proyecto busca difundir el punto de vista de un adversario y un defensor sobre las oportunidades de aplicación de actividades de ciberengaño. Permite desplegar un entorno productivo ficticio con su monitoreo, distintos niveles de engaño junto con una propuesta de atacante. El entorno simula una organización financiera ficticia llamada **Andesfinance** con servicios web vulnerables.

## Objetivo

Mostrar cómo se ve un entorno con:
- ✅ Múltiples actividades de engaño desplegadas
- ✅ Monitoreo de actividades 
- ✅ Un adversario aumentando su dificultad en la toma de decisiones.

## Arquitectura

```
┌────────────────────────────────────────────────────────────────────────────────┐
│                            "EXTERNAL" NETWORK                                    │
│                                                                                │
│  ┌─────────────────────────────────────────────────────────────────────────┐   │
│  │                        Attacker Container                               │   │
│  │                                                                         │   │
│  │  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────────┐  │   │
│  │  │  Scripting      │    │   Manual        │    │   Recon Tools       │  │   │
│  │  │  Attacks        │    │   Testing       │    │                     │  │   │
│  │  │                 │    │   (SSH/Web)     │    │   (nmap, etc.)      │  │   │
│  │  │   Port 5000     │    │                 │    │                     │  │   │
│  │  │                 │    │                 │    │                     │  │   │
│  │  └─────────────────┘    └─────────────────┘    └─────────────────────┘  │   │
│  └─────────────────────────────────────────────────────────────────────────┘   │
└────────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                                DMZ NETWORK                                      │
│                                                                                 │
│                   ┌───────────────────────────────────┐                         │
│                   │             Frontend              │                         │
│                   │                                   │                         │
│                   │          Employee Portal          │                         │
│                   │             Port 3000             │                         │
│                   └─────────────────────┬─────────────┘                         │
│                                         │                                       │
│                                         │ Backend Communication                 │
│                                         ▼                                       │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │                           SERVER NETWORK                                │    │
│  │                                                                         │    │
│  │  ┌───────────────┐    ┌──────────────┐    ┌─────────────────────────┐   │    │
│  │  │    Backend    │    │ SSH Honeypot │    │   Fake Activity         │   │    │
│  │  │               │    │              │    │   Generator             │   │    │
│  │  │ Financial API │    │  Port 22     │◄───│                         │   │    │
│  │  │   Port 3001   │    │              │    │                         │   │    │
│  │  └───────┬───────┘    └──────────────┘    └─────────────────────────┘   │    │
│  │          │                                                              │    │
│  │          │ Database Access                                              │    │
│  │          ▼                                                              │    │
│  │  ┌─────────────────────────────────────────────────────────────────┐    │    │
│  │  │                     DATABASE NETWORK                            │    │    │
│  │  │                                                                 │    │    │
│  │  │  ┌─────────────────────────────────────────────────────────┐    │    │    │
│  │  │  │                    MySQL Database                       │    │    │    │
│  │  │  │                                                         │    │    │    │
│  │  │  │              Financial Database                         │    │    │    │
│  │  │  │                Port 3306                                │    │    │    │
│  │  │  │                                                         │    │    │    │
│  │  │  └─────────────────────────────────────────────────────────┘    │    │    │
│  │  └─────────────────────────────────────────────────────────────────┘    │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                                                                 │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │                        MONITOR NETWORK                                  │    │
│  │                                                                         │    │
│  │  ┌──────────────┐    ┌──────────────┐    ┌─────────────────────────┐    │    │
│  │  │   Filebeat   │────│Elasticsearch │────│       Kibana            │    │    │
│  │  │ (Log Shipper)│    │  (Storage)   │    │  (Visualization)        │    │    │
│  │  │              │    │  Port 9200   │    │   Port 5601             │    │    │
│  │  └──────────────┘    └──────────────┘    └─────────────────────────┘    │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────────┘
```

## Stack Tecnológico

- **Frontend**: Portal de empleados de Andesfinance (Node.js/Express)
- **Backend**: API financiera de Andesfinance
- **MySQL**: Base de datos financiera con datos sensibles
- **SSH Honeypot**: Honeypot SSH personalizado
- **Fake Activity**: Generador de actividad falsa
- **Elastic Stack**: Filebeat + Elasticsearch + Kibana
- **Docker Compose**: Orquestación de contenedores

## Arquitectura de Seguridad

- **Frontend**: Accesible desde localhost (puerto 3000) - punto de entrada controlado
- **Backend**: Solo accesible internamente - protegido de acceso externo
- **MySQL**: Solo accesible internamente - base de datos aislada
- **SSH Honeypot**: Solo Accesible internamente desde la red de servidores
- **Elastic Stack**: Accesible desde localhost (Puerto 5601) - punto de entrada para monitoreo

## Estructura del Proyecto

```
/cyberdeception-playground/
├── docker-compose.yml          # Orquestación de servicios
├── frontend/                   # Aplicación web vulnerable
...
├── LICENSE                    # Licencia del proyecto
├── README.md                  # Documentación principal (inglés)
└── README_ES.md               # Este archivo
```

## Instalación y Uso

### Prerrequisitos

- Docker y Docker Compose instalados
- Al menos 4GB de RAM disponible
- Puertos 3000, 22, 5601, 9200 libres (backend y MySQL son internos)

### Niveles de Decepción

El sistema ahora soporta diferentes niveles de decepción cibernética:

- **None** - Sin engaño (servicios mínimos)
- **Basic** - Engaño básico (honeypots básicos)
- **Complete** - Engaño completa (todos los honeypots y señuelos)
- **Impossible** - Engaño máximo (técnicas avanzadas)

#### Comparativa: niveles vs actividades desplegadas

| Actividad / Componente | None | Basic | Complete | Impossible |
|------------------------|:----:|:-----:|:--------:|:----------:|
| **Señuelo SSH** (honeypot, captura intentos de conexión) | ❌ | ✅ | ✅ | ✅ |
| **Credenciales falsas** en base de usuarios del honeypot | ❌ | ✅ | ✅ | ✅ |
| **Archivos señuelo** (documentos falsos atractivos en frontend) | ❌ | ✅ | ✅ | ✅ |
| **Generador de actividad falsa** (rastros de uso diario sobre señuelo) | ❌ | ❌ | ✅ | ✅ |
| **API endpoints señuelo** (endpoints de decepción en backend) | ❌ | ❌ | ✅ | ✅ |
| **Columnas adicionales en DB** (estructura monitoreada en MySQL) | ❌ | ❌ | ✅ | ✅ |
| **Banners y servicios "instalados"** modificados | ❌ | ❌ | ❌ | ✅ |
| **Desinstalación forzada** de instalación reciente | ❌ | ❌ | ❌ | ✅ |
| **Ejecutables clave** especialmente modificados | ❌ | ❌ | ❌ | ✅ |

### 1. Clonar y Levantar el Entorno

```bash
# Clonar el repositorio
git clone https://github.com/Base4Security/cyberdeception-playground.git
cd cyberdeception-playground

# Levantar con nivel específico de decepción
./scripts/startup.sh <level>
./scripts\startup.bat <level>

# Verificar que todos los servicios estén corriendo
docker-compose ps
```

### 2. Verificar Servicios

```bash
# Verificar Kibana
curl http://localhost:5601

# Verificar Frontend (aplicación web)
curl http://localhost:3000

# Verificar que el contenedor atacante esté funcionando
docker exec attacker-tools whoami
```

### 3. Acceder a los Servicios

**Frontend Web Application (Andesfinance Portal):**
1. Abrir navegador en `http://localhost:3000`
2. Usar credenciales: `admin` / `admin123`
3. Explorar las vulnerabilidades del portal financiero

**Kibana Dashboard:**
1. Abrir navegador en `http://localhost:5601`
2. Explorar eventos en menu "Discover" 

## Simulación de Ataques

### Usar el Contenedor Atacante

El proyecto incluye un contenedor con herramientas de ataque para simular intrusiones contra los servicios de Andesfinance:

```bash

# Acceder directamente al contenedor atacante
docker exec -it attacker-tools /bin/bash

# Desde el contenedor atacante, se puede explotar una vulnerabilidad de command injection para explorar el frontend de Andesfinance
curl -X POST http://frontend:3000/diagnostics -H "Content-Type: application/json" -d '{"system_check": "ping", "target_host": "localhost | hostname"}'

curl -X POST http://frontend:3000/diagnostics -H "Content-Type: application/json" -d '{"system_check": "ping", "target_host": "localhost | sshpass -p admin ssh -o ConnectTimeout=5 -o StrictHostKeyChecking=no admin@172.22.0.2 whoami 2>/dev/null && echo SSH_SUCCESS:admin:admin@172.22.0.2"}'

# Para ejecutar una serie de ataques contra el fronend utiliza desde el contentedor atacante:
cd attack_scripts/
python3 main_attacker.py
```

## Visualización en Kibana

El dashboard incluye:

- **Eventos por Tipo**: Distribución de tipos de eventos
- **Actividad en Tiempo Real**: Timeline de actividad
- **IPs de Origen**: Top IPs que intentan acceder
- **Comandos Ejecutados**: Comandos más frecuentes
- **Logs Detallados**: Vista detallada de todos los eventos


# Análisis de niveles

## Observación de los diferentes niveles

### Nivel None
- Sin despliegue de engaño

### Nivel Basico

✅ Señuelo SSH: Captura intentos de conexión SSH
✅ Credenciales falsas en users db
✅ Archivos señuelo: Documentos falsos atractivos

### Nivel Completo

✅ Basico +
✅ Generador de actividad sobre señuelo: Rastros de patrones de uso diario
✅ Api endpoints señuelo: Api endpoint señuelo
✅ Cambios en estructura en base de datos: Columnas adicionales en DB especialmente monitoreadas

### Nivel Imposible

✅ Completo +
✅ Cambios en banners y servicios "instalados"
✅ Desinstalacion forzada de instalación reciente
✅ Cambios en ejecutables clave: Ejecutables especialmente modificados

## Configuración Avanzada

### Personalizar Actividad Falsa

Editar `fake-activity/app.py`:

```python
# Agregar nuevos usuarios
USERS = ["alice", "bob", "carol", "nuevo_usuario"]

# Agregar nuevos comandos
COMMANDS = [
    "ls -la",
    "cat /etc/passwd", 
    "nuevo_comando_interasante"
]
```

### Configurar Más Honeypots

Agregar al `docker-compose.yml`:

```yaml
services:
  ssh-honeypot-2:
    build: ./ssh-honeypot
    ports:
      - "2224:22"
    # ... resto de configuración
```

### Integrar con SIEM Externo

Modificar `filebeat/filebeat.yml`:

```yaml
output.elasticsearch:
  hosts: ["siem-externo:9200"]
  # ... configuración adicional
```

## Troubleshooting

### Logs de Debugging

```bash
# Ver logs de todos los servicios
docker-compose logs -f

# Ver logs de un servicio específico
docker-compose logs -f ssh-honeypot
docker-compose logs -f fake-activity
docker-compose logs -f filebeat
```

## Recursos Adicionales

- [A Practical Guide to Adversary Engagement](https://engage.mitre.org/learn-more-practical-guide)
- [Honeypot Best Practices](https://www.sans.org/white-papers/36607/)
- [Diseño de estrategias de ciberengaño a partir de inteligencia de ciberamenazas](https://www.researchgate.net/publication/394808490_Diseno_de_estrategias_de_ciberengano_a_partir_de_inteligencia_de_ciberamenazas)

## Contribuciones

¡Las contribuciones son bienvenidas!

1. Fork el proyecto
2. Crear una branch para tu feature
3. Commit tus cambios
4. Push a la branch
5. Abrir un Pull Request

## Licencia

Este proyecto está bajo la Licencia MIT. Ver [LICENSE](LICENSE) para más detalles. Historial de cambios: [CHANGELOG.md](CHANGELOG.md).

---
