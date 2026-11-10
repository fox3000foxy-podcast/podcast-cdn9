# podcast-cdnN (public) — coquille de déploiement d'un shard audio

Ce repo public **ne contient aucun fichier audio**. Il publie ~100 `.m4a`
(~250 Mo) sur GitHub Pages en les récupérant depuis le repo privé
`fox3000foxy-podcast/podcast-src` **uniquement dans le workspace éphémère
du runner**.

## Contenu (template unique, identique pour tous les shards)

- `.github/workflows/deploy-shard.yml` : checkout sparse du shard N
  (liste issue de `tracks.json`), assemblage `site/` (`files/*.m4a` +
  `feed.xml`), déploiement via `actions/deploy-pages`.
  Le numéro de shard vient du dispatch (`client_payload.shard`) ou de
  l'input manuel — **le même fichier sert tous les shards**.
- `.gitignore` : bloque tout `*.m4a` (garde-fou anti-commit audio).

## Créer un nouveau shard

```bash
rm -rf /tmp/newcdn && cp -r cdn-template /tmp/newcdn && cd /tmp/newcdn
git init -b master && git add . && git commit -m "cdnN: deploy shard N"
gh repo create fox3000foxy-podcast/podcast-cdnN --public --disable-wiki
git remote add origin https://github.com/fox3000foxy-podcast/podcast-cdnN.git
git push -u origin master
gh auth token | tr -d '\n' | gh secret set SRC_READ_TOKEN --repo fox3000foxy-podcast/podcast-cdnN
gh api repos/fox3000foxy-podcast/podcast-cdnN/pages -X POST -f build_type=workflow
# + DNS 4xA/4xAAAA podcast-cdnN -> Pages + CNAME une fois déployé
```

## Secrets requis

| Secret | Contenu |
|---|---|
| `SRC_READ_TOKEN` | PAT avec `Contents: read` sur `fox3000foxy-podcast/podcast-src` |
