# Seguridad

En esta sección encontrarás notas sobre **seguridad de aplicaciones web**, escritas con foco en **TypeScript y Node.js** (Express y NestJS).

La idea es que cada vulnerabilidad se vea con código real: primero el ejemplo vulnerable, después el corregido, y finalmente las preguntas de repaso.

## Navegación

- [OWASP Top 10:2025](owasp-top-10/README.md) — Los diez riesgos de seguridad más críticos en aplicaciones web, aplicados a Express y NestJS

## Enfoque de las notas

- **Ejemplos Always**: cada concepto se muestra primero como código vulnerable y luego como código corregido
- **Express y NestJS**: los dos frameworks se cubren en la misma página cuando la defensa es la misma
- **TypeScript como herramienta de defensa**: los tipos no detienen al atacante, pero impiden errores como `catch (e: any)` o un `req.user` sin verificar
- **Tarjetas de pregunta y respuesta**: cada página termina con tarjetas para repasar sin mirar las secciones anteriores

## Referencias

- [OWASP Top 10:2025](https://owasp.org/Top10/2025/es/) — Documento oficial en español
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/) — Guías de referencia por tema
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/) — Estándar de verificación con requisitos concretos
- [Node.js Security Best Practices](https://nodejs.org/en/learn/getting-started/security-best-practices)
- [CWE](https://cwe.mitre.org/) — Enumeración de debilidades de software

> **Nota:** el OWASP Top 10 no es una lista de "todo lo que puede fallar". Es el subconjunto más frecuente o más impactante según datos de la comunidad. Una aplicación sin ninguno de estos diez problemas sigue siendo vulnerable.

---

> **Siguiente:** [OWASP Top 10:2025](owasp-top-10/README.md)