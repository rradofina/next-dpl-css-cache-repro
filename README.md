# CSS asset URLs keep the previous deployment id after a cached webpack build

When `NEXT_DEPLOYMENT_ID` changes between two `next build --webpack` runs that share `.next/cache`,
the CSS emitted by the second build still references assets with the first build's `?dpl=` value.
The HTML of the second build carries the new id.

## Steps

```bash
npm install
NEXT_DEPLOYMENT_ID=dpl_AAA npx next build --webpack
grep -rhoE 'dot\.[a-z0-9]+\.svg\?dpl=[a-zA-Z_]+' .next/static/css   # dpl=dpl_AAA

# keep .next/cache, change only the deployment id
NEXT_DEPLOYMENT_ID=dpl_BBB npx next build --webpack
grep -rhoE 'dot\.[a-z0-9]+\.svg\?dpl=[a-zA-Z_]+' .next/static/css   # still dpl=dpl_AAA

# control: cold cache
rm -rf .next
NEXT_DEPLOYMENT_ID=dpl_BBB npx next build --webpack
grep -rhoE 'dot\.[a-z0-9]+\.svg\?dpl=[a-zA-Z_]+' .next/static/css   # dpl=dpl_BBB
```

Reproduced on 16.3.6 and 16.4.0-canary.52 (webpack builds).
