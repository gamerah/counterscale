# Counterscale - Gamerah

Fork de [benvinegar/counterscale](https://github.com/benvinegar/counterscale) con R2 desactivado (requiere tarjeta de crédito en Cloudflare). Los datos se retienen 90 dias en Analytics Engine.

## Requisitos previos

- Node v20+
- Cuenta de Cloudflare con Analytics Engine activado
- `npx wrangler login` ejecutado

## Secrets en Cloudflare

El Worker necesita estos secrets configurados:

```bash
npx wrangler secret put CF_ACCOUNT_ID --name counterscale
npx wrangler secret put CF_BEARER_TOKEN --name counterscale
npx wrangler secret put CF_PASSWORD_HASH --name counterscale
npx wrangler secret put CF_JWT_SECRET --name counterscale
```

- `CF_ACCOUNT_ID`: ID de la cuenta de Cloudflare
- `CF_BEARER_TOKEN`: API token con permisos `Account.Account Analytics`
- `CF_PASSWORD_HASH`: hash bcrypt de la contraseña del dashboard (generar con `node -e "require('bcryptjs').hash('PASSWORD', 10).then(console.log)"`)
- `CF_JWT_SECRET`: string aleatorio largo (generar con `openssl rand -hex 32`)

## Deploy

```bash
pnpm install
cd packages/server
pnpm build
npx wrangler deploy
```

El dashboard queda en https://counterscale.gamerah.workers.dev

## Tracking en el site

El script de tracking se carga en `Base.astro` del repo [gamerah-cms](https://github.com/gamerah/gamerah-cms):

```html
<script is:inline
  id="counterscale-script"
  src="https://counterscale.gamerah.workers.dev/tracker.js"
  data-site-id="gamerah"
></script>
```

## Cambios respecto al upstream

- Eliminado binding R2 y cron de rollups diarios en `packages/server/wrangler.json`
