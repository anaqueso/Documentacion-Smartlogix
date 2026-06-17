# SmartLogix

Sistema web de gestión logística desarrollado con arquitectura de microservicios. SmartLogix integra un **frontend en React** y un **backend en Spring Boot**, permitiendo administrar usuarios, inventario, pedidos, envíos, proveedores, bodegas, boletas y notificaciones desde una plataforma centralizada.

El sistema está orientado a mejorar la operación logística mediante control de inventario en línea, gestión de stock de venta y stock crítico, trazabilidad de pedidos y coordinación de envíos.

---

## Integrantes

- Catrina Corral
- Fernanda Manríquez
- Anais Aravena
---

## Descripción general del proyecto

SmartLogix permite que distintos tipos de usuarios interactúen con el sistema según su rol. Desde el frontend se puede iniciar sesión, registrar usuarios, gestionar productos, crear pedidos, revisar boletas, actualizar estados de envío, administrar proveedores y bodegas, además de visualizar notificaciones internas.

El backend expone los servicios mediante un **API Gateway**, administra la autenticación con **JWT**, registra los microservicios en **Eureka Server**, utiliza **Feign** para comunicación interna entre servicios, **RabbitMQ** para eventos de stock crítico y **PostgreSQL** como motor de base de datos.

---

## Tecnologías utilizadas

### Backend

- Java 21
- Spring Boot
- Spring Cloud Gateway MVC
- Spring Cloud Netflix Eureka
- Spring Security
- JWT
- Spring Data JPA
- PostgreSQL
- RabbitMQ
- OpenFeign
- Resilience4j Circuit Breaker
- Swagger / OpenAPI
- Maven
- Docker
- Docker Compose
- SonarQube

### Frontend

- React
- JavaScript
- Vite
- React Router DOM
- CSS por componente
- Fetch API
- LocalStorage para sesión y token JWT

---

## Arquitectura general

```text
Usuario / Navegador
        |
        v
Frontend React :5173
        |
        v
API Gateway :8080
        |
        +--> Servicio Usuarios :8081
        +--> Servicio Inventario :8082
        +--> Servicio Pedidos :8083
        +--> Servicio Envíos :8084
        +--> Servicio Notificaciones :8085
        |
        v
Eureka Server :8761
```

Flujos internos principales:

```text
Pedidos -----> Usuarios
Pedidos -----> Inventario
Pedidos -----> Envíos
Pedidos -----> Notificaciones

Inventario ---> RabbitMQ ---> Notificaciones

Envíos ------> Servicio externo Transportista simulado con httpbin.org
```

---

## Microservicios del backend

| Servicio | Puerto | Responsabilidad |
|---|---:|---|
| `apigateway` | 8080 | Punto de entrada del backend, validación JWT, autorización por rol y enrutamiento. |
| `eurekaserver` | 8761 | Registro y descubrimiento de microservicios. |
| `serviciousuarios` | 8081 | Registro, login, roles, validación de usuarios y generación de token JWT. |
| `servicioinventario` | 8082 | Gestión de productos, proveedores, bodegas, stock de venta, stock crítico y stock por bodega. |
| `serviciopedidos` | 8083 | Creación y consulta de pedidos, validación de usuario, validación de stock y generación de boleta. |
| `servicioenvios` | 8084 | Creación de envíos, seguimiento, actualización de estados y conexión con transportista externo simulado. |
| `servicionotificaciones` | 8085 | Gestión de notificaciones internas y consumo de eventos de stock crítico desde RabbitMQ. |

---

## Módulos del frontend

| Módulo | Funcionalidad |
|---|---|
| Login | Inicio de sesión con usuario y contraseña. Guarda token JWT y datos de sesión en `localStorage`. |
| Registro | Creación de usuarios con validaciones de campos, contraseña y dominio de correo. |
| Dashboard | Vista principal del sistema después del login. Redirige según el rol del usuario. |
| Navbar | Navegación superior, cierre de sesión, visualización de usuario y control de acceso por rol. |
| Inventario | Listado, creación, edición, eliminación y filtrado de productos por tipo de stock. |
| Pedidos | Creación de pedidos, listado de pedidos y consulta de boleta asociada. |
| Envíos | Listado de envíos, búsqueda por pedido y actualización de estado. |
| Proveedores | Listado y creación de proveedores. |
| Bodegas | Creación de bodegas, sucursales o tiendas y asignación de stock por producto. |
| Notificaciones | Consulta y envío de notificaciones internas por usuario o rol. |

---

## Roles del sistema

| Rol | Acceso principal |
|---|---|
| `ADMIN` | Administración general: inventario, pedidos, envíos, proveedores, bodegas, boletas y notificaciones. |
| `VENDEDOR` | Gestión de inventario, pedidos, boletas y notificaciones. |
| `USER` | Creación y consulta de pedidos propios, boletas y notificaciones propias. |
| `PROVEEDOR` | Acceso a proveedores y apoyo al reabastecimiento. |

---

## Bases de datos

Cada microservicio principal utiliza una base de datos PostgreSQL independiente.

| Contenedor | Base de datos | Puerto local |
|---|---|---:|
| `db-usuarios` | `baseusuarios` | 5433 |
| `db-inventario` | `baseinventario` | 5434 |
| `db-pedidos` | `basepedidos` | 5435 |
| `db-envios` | `baseenvios` | 5436 |
| `db-notificaciones` | `basenotificaciones` | 5437 |
| `db-sonarqube` | `sonar` | interno |

Credenciales usadas para desarrollo local:

```env
SPRING_DATASOURCE_USERNAME=postgres
SPRING_DATASOURCE_PASSWORD=1234
```

---

## Requisitos previos

Antes de ejecutar el proyecto se debe contar con:

- Git
- Java 21
- Maven
- Docker
- Docker Compose
- Node.js
- npm

---

## Instalación y ejecución

El sistema debe ejecutarse levantando primero el backend y luego el frontend.

### 1. Clonar o descargar los repositorios

```bash
git clone <URL_REPOSITORIO_BACKEND>
git clone <URL_REPOSITORIO_FRONTEND>
```

---

### 2. Ejecutar backend con Docker Compose

Entrar a la carpeta raíz del backend donde se encuentra el archivo `docker-compose.yml`.

```bash
cd proyectoSmartLogix
```

Levantar contenedores:

```bash
docker compose up --build
```

Si tu versión de Docker usa el comando clásico:

```bash
docker-compose up --build
```

Ejecutar en segundo plano:

```bash
docker compose up --build -d
```

Detener contenedores:

```bash
docker compose down
```

Detener contenedores y eliminar volúmenes:

```bash
docker compose down -v
```

---

### 3. Ejecutar frontend

Entrar a la carpeta del frontend.

```bash
cd smartlogix_frontend_aravena_corral_manriquez-main
```

Instalar dependencias:

```bash
npm install
```

Ejecutar en modo desarrollo:

```bash
npm run dev
```

Abrir en el navegador la URL entregada por Vite, normalmente:

```text
http://localhost:5173
```

---

## URLs útiles

| Herramienta / Servicio | URL |
|---|---|
| Frontend React | `http://localhost:5173` |
| API Gateway | `http://localhost:8080` |
| Servicio Usuarios | `http://localhost:8081` |
| Servicio Inventario | `http://localhost:8082` |
| Servicio Pedidos | `http://localhost:8083` |
| Servicio Envíos | `http://localhost:8084` |
| Servicio Notificaciones | `http://localhost:8085` |
| Eureka Server | `http://localhost:8761` |
| RabbitMQ Management | `http://localhost:15672` |
| SonarQube | `http://localhost:9000` |

Credenciales por defecto de RabbitMQ:

```text
usuario: guest
contraseña: guest
```

---

## Swagger / OpenAPI

Después de levantar el backend, se puede revisar la documentación Swagger de cada microservicio en:

```text
http://localhost:8081/swagger-ui/index.html
http://localhost:8082/swagger-ui/index.html
http://localhost:8083/swagger-ui/index.html
http://localhost:8084/swagger-ui/index.html
http://localhost:8085/swagger-ui/index.html
```

---

## Endpoints principales

### Usuarios

| Método | Ruta | Descripción |
|---|---|---|
| POST | `/api/usuarios/registrar` | Registra un nuevo usuario. |
| POST | `/api/usuarios/login` | Inicia sesión y retorna token JWT. |
| GET | `/api/usuarios/rol/{rol}` | Lista usuarios por rol. |
| GET | `/api/usuarios/{username}` | Busca usuario por nombre de usuario. |

### Inventario

| Método | Ruta | Descripción |
|---|---|---|
| POST | `/api/inventario` | Crea un producto. |
| GET | `/api/inventario` | Lista productos. |
| GET | `/api/inventario/{id}` | Obtiene producto por ID. |
| GET | `/api/inventario/stock/{tipoStock}` | Filtra productos por tipo de stock. |
| GET | `/api/inventario/stock/critico/alertas` | Lista productos con stock bajo o crítico. |
| PUT | `/api/inventario/{id}` | Actualiza producto. |
| DELETE | `/api/inventario/{id}` | Elimina producto. |
| PUT | `/api/inventario/{id}/descontar` | Descuenta stock de un producto. |

### Pedidos y boletas

| Método | Ruta | Descripción |
|---|---|---|
| POST | `/api/pedidos` | Crea un pedido, valida usuario, valida stock y genera trazabilidad. |
| GET | `/api/pedidos` | Lista pedidos. |
| GET | `/api/pedidos/{id}` | Obtiene pedido por ID. |
| GET | `/api/boletas/pedido/{pedidoId}` | Obtiene la boleta asociada a un pedido. |

### Envíos

| Método | Ruta | Descripción |
|---|---|---|
| POST | `/api/envios` | Crea un envío asociado a un pedido. |
| GET | `/api/envios` | Lista envíos. |
| GET | `/api/envios/{id}` | Obtiene envío por ID. |
| GET | `/api/envios/pedido/{pedidoId}` | Busca envío por ID de pedido. |
| PUT | `/api/envios/{id}/estado` | Actualiza el estado de un envío. |

Estados permitidos:

```text
PENDIENTE
EN_CAMINO
ENTREGADO
CANCELADO
SERVICIO_NO_DISPONIBLE
```

### Proveedores y bodegas

| Método | Ruta | Descripción |
|---|---|---|
| POST | `/api/proveedores` | Crea un proveedor. |
| GET | `/api/proveedores` | Lista proveedores. |
| GET | `/api/proveedores/{id}` | Obtiene proveedor por ID. |
| DELETE | `/api/proveedores/{id}` | Elimina proveedor. |
| POST | `/api/bodegas` | Crea una bodega, tienda o sucursal. |
| GET | `/api/bodegas` | Lista bodegas. |
| GET | `/api/bodegas/{id}` | Obtiene bodega por ID. |
| DELETE | `/api/bodegas/{id}` | Elimina bodega. |

### Notificaciones

| Método | Ruta | Descripción |
|---|---|---|
| POST | `/api/notificaciones` | Crea una notificación. |
| POST | `/api/notificaciones/enviar-rol` | Envía notificación a usuarios de un rol. |
| POST | `/api/notificaciones/enviar-usuario` | Envía notificación a un usuario específico. |
| GET | `/api/notificaciones` | Lista notificaciones. |
| GET | `/api/notificaciones/{id}` | Obtiene notificación por ID. |
| GET | `/api/notificaciones/usuario/{usuarioId}` | Lista notificaciones por usuario. |
| DELETE | `/api/notificaciones/{id}` | Elimina notificación. |

---

## Autenticación

El sistema utiliza JWT para proteger las rutas privadas.

Rutas públicas:

```text
POST /api/usuarios/login
POST /api/usuarios/registrar
```

El resto de solicitudes protegidas deben enviar el token en el encabezado:

```http
Authorization: Bearer <token>
```

Ejemplo de login:

```http
POST /api/usuarios/login
Content-Type: application/json
```

```json
{
  "username": "vendedor1",
  "password": "12345678"
}
```

Respuesta esperada:

```json
{
  "token": "eyJ...",
  "username": "vendedor1",
  "correo": "vendedor1@duocuc.cl",
  "rol": "VENDEDOR"
}
```

---

## Flujo principal del sistema

1. El usuario ingresa al frontend.
2. Inicia sesión o registra una nueva cuenta.
3. El backend valida credenciales y genera un token JWT.
4. El frontend guarda la sesión en `localStorage`.
5. El usuario accede a los módulos permitidos según su rol.
6. El frontend consume los servicios mediante el API Gateway.
7. Pedidos valida usuario, producto y stock antes de crear el pedido.
8. Inventario descuenta unidades al generar un pedido.
9. Pedidos genera una boleta asociada.
10. Pedidos solicita la creación del envío.
11. Envíos consulta el transportista externo simulado.
12. Si el transportista falla, Resilience4j ejecuta un fallback.
13. Si el stock queda bajo el mínimo, Inventario publica un evento en RabbitMQ.
14. Notificaciones consume el evento y genera avisos internos.

---

## Patrones y componentes aplicados

### API Gateway

Centraliza el acceso al backend, valida tokens JWT, aplica autorización por rol y enruta las peticiones hacia los microservicios correspondientes.

### Service Discovery con Eureka

Permite que los microservicios se registren y sean descubiertos mediante nombres lógicos, evitando depender directamente de URLs fijas entre servicios.

### Comunicación con Feign

Pedidos se comunica con usuarios, inventario, envíos y notificaciones mediante clientes Feign.

### RabbitMQ

Inventario publica eventos cuando un producto alcanza stock crítico. Notificaciones consume estos eventos desde la cola:

```text
stock.critico.queue
```

### Factory Method

El microservicio de envíos crea envíos de tipo:

```text
NORMAL
EXPRESS
```

Cada tipo define transportista, código de seguimiento, días estimados y fecha estimada de entrega.

### Circuit Breaker

El microservicio de envíos utiliza Resilience4j para manejar fallas del transportista externo simulado. Cuando ocurre una falla, se activa un fallback y el envío queda con estado:

```text
SERVICIO_NO_DISPONIBLE
```

---

## Estructura general esperada

```text
SmartLogix/
│
├── backend/
│   └── proyectoSmartLogix/
│       ├── apigateway/
│       ├── eurekaserver/
│       ├── servicioenvios/
│       ├── servicioinventario/
│       ├── servicionotificaciones/
│       ├── serviciopedidos/
│       ├── serviciousuarios/
│       ├── docker-compose.yml
│       └── pom.xml
│
└── frontend/
    └── smartlogix_frontend_aravena_corral_manriquez-main/
        ├── componentes/
        ├── pages/
        ├── App.jsx
        ├── main.jsx
        ├── package.json
        └── vite.config.js
```

---

## Scripts del frontend

```bash
npm run dev
```

Ejecuta el frontend en modo desarrollo.

```bash
npm run build
```

Genera la versión de producción.

```bash
npm run preview
```

Permite previsualizar la versión generada para producción.

```bash
npm run lint
```

Ejecuta revisión de código con ESLint, si se encuentra configurado.

---

## Comandos útiles del backend

Compilar todo el proyecto:

```bash
mvn clean install
```

Ejecutar pruebas:

```bash
mvn test
```

Ejecutar un microservicio individual:

```bash
cd servicioinventario
mvn spring-boot:run
```

Ejecutar pruebas de un módulo específico:

```bash
cd serviciopedidos
mvn test
```

---

## Pruebas

El backend incluye pruebas unitarias para servicios como usuarios, inventario, pedidos y envíos.

Las pruebas cubren casos como:

- Registro y búsqueda de usuarios.
- Normalización de tipo de stock.
- Descuento de stock.
- Validación de stock insuficiente.
- Creación de pedidos.
- Validación de usuario inexistente.
- Validación de producto inexistente.
- Actualización de estado de envío.
- Fallback cuando el transportista externo no está disponible.

---

## Consideraciones importantes

- El backend debe estar levantado antes de usar el frontend.
- El frontend consume el backend mediante el API Gateway en `http://localhost:8080`.
- El frontend se ejecuta normalmente en `http://localhost:5173`.
- No se debe subir `node_modules` al repositorio.
- No se deben subir carpetas `target` generadas por Maven.
- Para reinstalar dependencias del frontend basta con ejecutar `npm install`.
- Para limpiar completamente los contenedores y datos locales se puede usar `docker compose down -v`.
- El servicio de transportista no está implementado como microservicio propio; se simula mediante `https://httpbin.org`.
- RabbitMQ conecta principalmente el flujo de stock crítico entre Inventario y Notificaciones.
- Los microservicios se registran automáticamente en Eureka.

---

## Estilo visual

El frontend utiliza una interfaz oscura con tonos morados y rosados, manteniendo consistencia visual entre formularios, tablas, botones, tarjetas, navegación y paneles de notificaciones.

---

## Licencia

Proyecto desarrollado con fines académicos para la evaluación de SmartLogix.
