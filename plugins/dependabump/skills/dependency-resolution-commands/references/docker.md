# Docker

Dockerfiles and Compose files reference container images by tag or digest. There is no lockfile: the reference in the file is the version that builds and pulls resolve.

## Detect

Docker owns `Dockerfile`, `Dockerfile.*`, `*.Dockerfile`, `Containerfile`, and Compose files named `compose.yaml`, `docker-compose.yml`, and their `.override` variants; `.env` supplies Compose interpolation values. The first command lists those files; the second prints every image the merged Compose model references, across all profiles.

```bash
# List Dockerfiles and Compose files.
# Mutates: nothing.
rg --files --glob '{Dockerfile,Dockerfile.*,*.Dockerfile,*.dockerfile,Containerfile,Containerfile.*,compose*.yaml,compose*.yml,docker-compose*.yaml,docker-compose*.yml}'
```

```bash
# List every image in the merged Compose model.
# Mutates: nothing.
docker compose --profile '*' config --images
```

## Outdated

Docker has no outdated command; compare the referenced tag with the registry's tags. The Docker Hub command prints tags that contain `$PREFIX`, newest first, with push times (official images use `NAMESPACE=library`). For other registries, `skopeo list-tags` prints all tags without dates.

```bash
# List Docker Hub tags that contain a prefix, newest first.
# Mutates: nothing.
curl --silent "https://hub.docker.com/v2/namespaces/$NAMESPACE/repositories/$IMAGE/tags?page_size=100&ordering=last_updated&name=$PREFIX" | jq '.results[] | {name, last_updated, digest}'
```

```bash
# List the tags of a repository on any registry.
# Mutates: nothing.
skopeo list-tags "docker://$REGISTRY/$REPO"
```

## Inspect

The command reads a tag or digest from the registry without pulling and prints its digest and platform manifests; entries with platform `unknown/unknown` are attestations. Published versions with dates come from the "Outdated" Hub query. Images declare no dependency ranges.

```bash
# Show the digest and platforms of one tag.
# Mutates: nothing.
docker buildx imagetools inspect "$IMAGE:$TAG"
```

## Dependents

An image's dependents are the Dockerfile `FROM` and `COPY --from` lines and the Compose `image:` values that name it; the printed lines show the tag each one uses. `--hidden` includes `.env`.

```bash
# List every line that names an image.
# Mutates: nothing.
rg --line-number --hidden --fixed-strings "$IMAGE" --glob '{Dockerfile,Dockerfile.*,*.Dockerfile,*.dockerfile,Containerfile,Containerfile.*,compose*.yaml,compose*.yml,docker-compose*.yaml,docker-compose*.yml,.env}'
```

## Duplicates

A duplicate is one image repository referenced at several tags or digests. The "Detect" Compose listing and the command below print every reference; Docker has no collapse command. The agent collapses duplicates by replacing each other reference with the chosen one (see "Constrain").

```bash
# List every FROM and --from reference in Dockerfiles.
# Mutates: nothing.
rg --line-number --ignore-case 'from' --glob '{Dockerfile,Dockerfile.*,*.Dockerfile,*.dockerfile,Containerfile,Containerfile.*}'
```

## Constrain

A tag is the range: `postgres:17` follows every 17.x release, `postgres:17.6` follows rebuilds of 17.6, and `postgres:17.6@sha256:DIGEST` is exactly one image. Raise only the version in the tag to `$VERSION` and keep its granularity and any suffix (`17.6-bookworm` becomes `$VERSION-bookworm`) in each `FROM` line, `ARG` default, `image:` value, and `.env` variable that names it; a digest-pinned reference gets the new tag and the new digest that the "Inspect" command prints.

## Update

Docker has no update command; the "Constrain" edit is the update. A Dockerfile build pulls a missing base image itself; the command below pulls the current image of every Compose service that has no `build:` section.

```bash
# Pull the images of every Compose service.
# Mutates: the local image store.
docker compose --profile '*' pull --ignore-buildable
```

## Add

Docker has no add command. Add a `FROM $IMAGE:$TAG AS NAME` line to the Dockerfile or a service with `image: $IMAGE:$TAG` to the Compose file, then validate the merged model; an omitted tag means `latest`.

```bash
# Validate the merged Compose model.
# Mutates: nothing.
docker compose --profile '*' config --quiet
```

## Remove

Docker has no remove command. Delete the service from every Compose file that defines it, including override files, or delete the stage's `FROM ... AS NAME` block, then validate with the "Add" command; the command fails when a remaining file still references the removed service.

## Override

Compose overrides live in `compose.override.yaml` (merged by default) and in each extra `--file` layer; a later file's `image:` replaces the earlier one, and Compose `build.args` override `ARG` defaults used in `FROM` lines. To test whether an override entry is redundant, run the command below, delete the entry, run it again, and restore the entry when the printed images changed.

```bash
# Print the images of the merged Compose model.
# Mutates: nothing.
docker compose --profile '*' config --images
```

## Patch

Patches live in Dockerfile instructions that change the base image in place: a `RUN` that upgrades or pins a package, or a `COPY` that replaces a base-image file. To remove one, delete the instruction and validate with the "Add" command.

## Unused

Docker has no established unused-image detector.

## Deprecated

The endoflife.date API prints the support state of one release line; the product name of an image comes from the purl query. A deprecated Docker Hub image states it at the top of its README, which the third command prints.

```bash
# Find the endoflife.date product of a Docker Hub image.
# Mutates: nothing.
curl --silent https://endoflife.date/api/v1/identifiers/purl | jq --arg purl "pkg:docker/library/$IMAGE" '.result[] | select(.identifier == $purl)'
```

```bash
# Show the support state of one release line.
# Mutates: nothing.
curl --silent "https://endoflife.date/api/v1/products/$PRODUCT/releases/$RELEASE" | jq '.result | {name, isEol, eolFrom, isMaintained, latest}'
```

```bash
# Print the README of a Docker Hub repository.
# Mutates: nothing.
curl --silent "https://hub.docker.com/v2/namespaces/$NAMESPACE/repositories/$IMAGE" | jq --raw-output '.full_description'
```

## Hygiene

- Compose override files: stale when they repeat the base value or name a removed service.
- Compose `profiles`: stale when no `--profile` flag or `COMPOSE_PROFILES` value enables them.
- `.env` variables and `${VAR:-default}` defaults: stale when no image reference uses them.
- Compose `build.args` and `ARG` lines: stale when the other side has no matching name.
- `pull_policy`: stale when a debugging value such as `always` or `never` remains.
- Digest pins: stale when the tag's current digest from the "Inspect" command differs.
