# api-gateway

**API Gateway** (Spring Cloud Gateway) del sistema SPSRT (UNIR, MISSI): único punto de
entrada (puerto 8080), enruta a los microservicios vía Eureka y valida el **JWT**.

Parte del sistema **SPSRT — Sistema de Planificación y Seguimiento de Recursos Técnicos**.
El stack completo se orquesta desde el repo
[`orquestacion`](https://github.com/tfm-missi-2026/orquestacion).

## Datos del servicio

|               |                                                                           |
| ------------- | ------------------------------------------------------------------------- |
| Puerto        | **8080**                                                                  |
| Base de datos | — (no usa)                                                                |
| Paquete base  | `pe.unir.tfm.srp.gateway`                                                 |
| Stack         | Java 25 · Spring Boot 3.5.14 · Spring Cloud Gateway (WebFlux) · JWT HS256 |

## Cómo se levanta

El gateway **no tiene sentido aislado**: necesita Eureka y al menos un microservicio para
enrutar. Por eso se levanta como parte del **stack completo** desde el repo
[`orquestacion`](https://github.com/tfm-missi-2026/orquestacion):

```bash
# dentro del repo orquestacion
cp .env.example .env
docker compose up -d --build
```

Para desarrollo local del propio gateway (con un Eureka ya corriendo), puede ejecutarse con:

```bash
mvn spring-boot:run
```

## Ruteo (prefijo → microservicio)

| Prefijo                                                                                                        | Destino             |
| -------------------------------------------------------------------------------------------------------------- | ------------------- |
| `/api/auth/**`, `/api/usuarios/**`, `/api/roles/**`, `/api/modulos/**`, `/api/catalogo/**`                     | `ms-administracion` |
| `/api/proyectos/**`, `/api/subproyectos/**`, `/api/tareas/**`                                                  | `ms-proyectos`      |
| `/api/asignaciones/**`, `/api/actividades/**`, `/api/bitacora/**`, `/api/linea-base/**`, `/api/variaciones/**` | `ms-seguimiento`    |

Público (sin JWT): `/api/auth/**` (login) y `/actuator/**`. El resto exige token válido.

## URLs útiles (con el stack completo levantado)

- Eureka dashboard — http://localhost:8761
- API Gateway — http://localhost:8080 (`/actuator/health`)
- Swagger — ms-administracion `:8081` · ms-proyectos `:8082` · ms-seguimiento `:8083` (`/swagger-ui.html`)

## Requisitos

- **Docker Desktop 24+** (vía el repo `orquestacion`), **o** Java 25 + Maven 3.9+ para
  correr el gateway en local.
