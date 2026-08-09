# @lifetrack/contracts

Fuente única de verdad de los contratos gRPC (`.proto`) de LifeTrack OS. Reemplaza la
copia manual de `.proto` que hoy vive duplicada en cada microservicio y en `api-gateway`.

## Contenido

```
proto/
  auth.proto
  user.proto
  vault.proto
  finance.proto
  rehab.proto
```

## Uso en un microservicio o en api-gateway

1. Instalar el paquete:

```bash
pnpm add @lifetrack/contracts
```

2. Apuntar `protoPath` al archivo dentro de `node_modules` en vez del archivo local:

```ts
// antes
protoPath: join(process.cwd(), 'src/proto/auth.proto'),

// después
protoPath: join(
  require.resolve('@lifetrack/contracts/package.json'),
  '..',
  'proto/auth.proto',
),
```

3. Borrar la copia local en `src/proto/*.proto` del servicio que migre.

## Publicar una nueva versión

Este paquete se publica público en npmjs.org, bajo la org `lifetrack`. No requiere
ningún token en los proyectos que lo consumen (paquete público, sin auth para
instalar) — el token solo hace falta para publicar.

Todo el trabajo vive en una sola rama (`develop`) — no hay `main`/tags/PRs por ahora.
Flujo:

```bash
# editar el/los .proto, luego bumpear versión
npm version patch   # o minor / major según el cambio
git push origin develop
```

El push a `develop` dispara el job de Jenkins (Pipeline simple, no Multibranch —
no hace falta con una sola rama activa). El job publica solo si la versión de
`package.json` todavía no existe en el registry, así evita republicar sin querer
cuando alguien pushea sin bumpear versión (ver `Jenkinsfile`). Después, en cada
servicio que consuma el paquete:

```bash
pnpm add @lifetrack/contracts@latest
```

## Cuándo bumpear qué

- **patch**: fix de un typo, comentario, campo agregado que es retrocompatible (nuevo
  field opcional al final de un message).
- **minor**: nuevo RPC o nuevo message, sin romper contratos existentes.
- **major**: se borra o renombra un campo/RPC existente — requiere coordinar el deploy
  de todos los servicios que lo consumen.
