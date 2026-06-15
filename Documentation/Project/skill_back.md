A continuación se muestra los resultados generados por la auditoria en el backend realizada por Copilot. La fecha de la revision es del 14 de Junio del 2026 a las 9:31 pm, por lo que otras revisiones del codigo podrian ofrecer resultados distintos de acuerdo al desarrollo del proyecto.

Puntualmente, el proyecto para ese momento no cuenta con documentación ni con las pruebas y tampoco esta desarrllado en su totalidad.

---

# Backend Review Report

## General Information
- **Scope:** Auditoría completa del proyecto
- **Files reviewed:** 25 archivos (9 controladores, 11 servicios, 5 entidades, 2 configuración, repositorios)

## Architecture Compliance

| # | File:Line | Violation | Severity | Details |
|---|---|---|---|---|
| 1 | `ProductGetService.java:32-50` | Duplicación de método en capa incorrecta | **MUST FIX** | El método `updateProduct()` está definido en `ProductGetService` pero debería existir únicamente en `ProductUpdateService`. Viola el patrón de capas y responsabilidad única. |
| 2 | `ProductCreateController.java:28-39`<br>`ProductUpdateController.java:28-37`<br>`UserCreateController.java:14-24`<br>`SupplierCreateController.java:23-28` | Violación del patrón de Request/Payload | **MUST FIX** | Los parámetros se reciben como `@RequestParam` individuales en lugar de usar clases `@RequestBody` estructuradas. Incumplimiento de regla: *"POST/PUT methods must use specific request classes con @RequestBody"*. |
| 3 | `AuthLoginController.java:40-48`<br>`UserChangeRoleController.java:27-31` | Violación del patrón de Request/Payload | **MUST FIX** | Los parámetros POST/PUT usan `@RequestParam` en lugar de `@RequestBody` con clases request estructuradas. |
| 4 | `SecurityConfig.java:17` | Seguridad deshabilitada globalmente | **MUST FIX** | `.permitAll()` permite acceso sin autenticación a todos los endpoints. Falta validación de tokens JWT. Todos los endpoints sensibles quedan expuestos. |
| 5 | `ActiveSessionService.java:1-35` | Arquitectura de sesión inadecuada | **MUST FIX** | Usa variable `private String activeToken` como singleton. No es thread-safe en aplicaciones web distribuidas. Las sesiones de usuario se sobrescriben entre múltiples clientes. Solución: usar `SecurityContext` de Spring o almacenamiento basado en tokens. |
| 6 | `AuthLoginController.java:50`<br>`AuthLogoutController.java:20` | Desacoplamiento de responsabilidades | **MUST FIX** | Los controladores de auth invocan directamente servicios de sesión. Debería delegarse a un servicio de auth centralizado. Violación de flujo: `controller` -> `service` -> `auth`. |

## Code Quality Findings

| # | File:Line | Finding | Severity | Details |
|---|---|---|---|---|
| 1 | Todos los controladores (`ProductCreateController`, `UserCreateController`, etc.) | Falta de documentación JavaDoc | **SHOULD FIX** | Ningún controlador contiene JavaDoc explicando propósito, parámetros ni tipos de retorno. Violación de regla: *"Every public interface, public service method, and controller endpoint must have proper JavaDoc"*. |
| 2 | Todos los servicios (`ProductCreateService`, `UserCreateService`, etc.) | Falta de documentación JavaDoc | **SHOULD FIX** | Los servicios carecen de JavaDoc para métodos públicos. Violación de estándar de documentación. |
| 3 | `ProductCreateController.java:47-57`<br>`ProductGetController.java:32-45`<br>`UserCreateController.java:31-39` | Construcción manual de respuestas con HashMap | **SHOULD FIX** | Todas las respuestas usan `HashMap<String, Object>` manualmente. Debería usarse clase DTO estructurada (e.g., `UserResponse`, `ProductResponse`). Violación: *"inline type declarations instead of structured classes"*. |
| 4 | Todos los servicios | Ausencia de logging (SLF4J) | **SHOULD FIX** | Ningún servicio implementa logging. Recomendación: `private static final Logger logger = LoggerFactory.getLogger(ClassName.class);` y registrar eventos clave. Violación de regla: *"Missing logger entries in alternative or error flows"*. |
| 5 | `ProductCreateService.java:53-71` | Lógica de validación confusa y defectuosa | **SHOULD FIX** | Los métodos `validateNameExists()` y `validateBatchExists()` lanzan excepciones dentro de try-catch y retornan boolean. La condición `if (validateNameExists(name) && validateBatchExists(batch))` es inalcanzable si falla cualquiera de los métodos. Refactorizar para claridad. |
| 6 | `PasswordHasher.java:10` | Typo en nombre de campo | **SHOULD FIX** | Campo `enconder` debería ser `encoder`. Problema de legibilidad. |
| 7 | `UserGetAllController.java:38-39` | Inconsistencia en nombres de respuesta | **SHOULD FIX** | Usa claves `"State"` y `"Role"` con mayúsculas (inconsistente con otros campos `"id"`, `"name"`, `"email"` en minúsculas). Falta coherencia en REST API design. |
| 8 | `ProductCreateController.java:28`<br>`UserCreateController.java:14`<br>`SupplierCreateController.java:23` | Parámetros sin validaciones de anotaciones | **SHOULD FIX** | Los `@RequestParam` carecen de validaciones (`@NotNull`, `@NotBlank`, `@Valid`, etc.). Valores pueden ser null o strings vacíos sin control. |
| 9 | `User.java:55` | Método `@PostLoad` con naming incorrecto | **SHOULD FIX** | El método se llama `UploadState()` con PascalCase. Debería ser `loadState()` o similar en camelCase. Violación de convención de nombres. |
| 10 | `User.java:22-24` | Diseño problemático con `@Transient` | **SHOULD FIX** | El campo `state` es `@Transient` mientras que `stateString` se persiste. Patrón frágil: conversión manual en `@PostLoad`. Mejor usar conversión automática con `@Convert(converter=...)`. |

## Security Findings

| # | File:Line | Issue | Severity | Details |
|---|---|---|---|---|
| 1 | `SecurityConfig.java:17-20` | Seguridad deshabilitada | **MUST FIX** | `csrf.disable() + .permitAll()` expone todos los endpoints sin autenticación. Falta validación de JWT tokens. Implementar filtro JWT que valide tokens en cada request. |
| 2 | `JwtService.java:13` | Hardcoded secret key en código | **MUST FIX** | `SECRET_KEY = "unerp-secret-key-development-unerp"` está hardcoded. Violación: *"omission of required contexts or hardcoded configuration strings/secrets in the code"*. Mover a `application.yaml` o variables de entorno. |
| 3 | `ActiveSessionService.java:1-35` | Almacenamiento de tokens inseguro | **MUST FIX** | Token guardado en campo `private String activeToken` (variable singleton no thread-safe). Riesgo: tokens de usuarios se mezclan. Solución: usar JWT sin estado (stateless) o almacenar en base de datos con sesión real. |
| 4 | `AuthLoginController.java:65`<br>`AuthLogoutController.java:20`<br>`UserCreateController.java:47` | Exposición de `e.getMessage()` en respuestas | **SHOULD FIX** | Se retorna directamente `e.getMessage()` en respuestas HTTP. Riesgo: información sensible de stack traces o mensajes internos se expone. Usar mensajes genéricos para cliente y loguear detalles internamente. |
| 5 | `AuthLoginService.java:32-35`<br>`UserCreateService.java:58-62` | Mensajes de error que revelan información | **SHOULD FIX** | Mensajes como `"El usuario no existe"` y `"Email ya registrado"` revelan si una cuenta existe. Usar mensaje genérico: `"Credenciales inválidas"` para login y evitar enumeration attacks. |
| 6 | `ProductCreateController.java:28`<br>`UserCreateController.java:14` | Parámetros sin validación | **SHOULD FIX** | `@RequestParam String email`, `@RequestParam String password` sin validaciones. Riesgo: inyección de caracteres especiales, overflow de strings. Agregar `@NotBlank`, `@Email`, `@Size` en clases Request. |
| 7 | `UserDeactivateService.java:38-41` | Validación de identidad débil | **SHOULD FIX** | Usa `jwtService.extractUserId()` sin validar integridad del token. Si `ActiveSessionService` no valida correctamente, se puede deactivar usuarios incorrectamente. Implementar validación adicional. |