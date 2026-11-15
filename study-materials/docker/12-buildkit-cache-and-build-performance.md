# 12 — BuildKit, Cache and Build Performance

## Why build performance matters

A container build is part of the developer feedback loop and the release pipeline. Slow builds delay testing and make frequent delivery harder. Docker BuildKit provides modern build features, including improved caching, parallelism, and secure build mounts.

## 1. BuildKit basics

Recent Docker installations use BuildKit by default in many workflows. Buildx provides a higher-level interface for building images across platforms and using advanced build features.

```bash
docker buildx version
docker buildx ls
docker buildx build -t sample-api:dev --load .
```

`--load` loads the result into the local Docker image store for local testing when using a builder that exports to a separate store.

## 2. Cache-friendly Dockerfiles

Copy stable dependency manifests before frequently changing source files. This helps Docker reuse dependency installation layers when application code changes.

For example:
- Copy project files and restore dependencies.
- Copy application source.
- Build/publish the application.

Use cache mounts for package managers when supported and useful. Measure whether they improve your actual build.

## 3. Multi-platform images

Some development machines use ARM processors while production may use x86-64, or vice versa. Buildx can target multiple platforms when the builder supports it.

```bash
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t <registry>/<image>:<tag> \
  --push .
```

Do not assume every base image or native dependency supports both architectures. Test the exact target platforms.

## 4. Cache in CI

A pipeline can import/export a registry or GitHub Actions cache so repeated builds can reuse layers. Keep access controlled and avoid treating cache contents as trusted release artifacts without validation.

## 5. Build secrets

If a build step genuinely needs access to a private package feed, use BuildKit secret mounts rather than writing credentials into Dockerfile instructions or image layers. The build command and Dockerfile need to use the supported secret mount syntax. Prefer a build design that keeps credentials out of the final image and logs.

## Practice exercise

Build a small API twice and compare the time and output of the builds. Modify only application source and note which layers are reused. Then test an appropriate platform build and verify the resulting image on that platform.

## Self-check

- What does BuildKit add to the Docker build process?
- Why does Dockerfile instruction order affect cache reuse?
- What is a multi-platform image?
- Why are build caches not the same as released artifacts?
- How should a build authenticate to a private package feed?

## Official references

- Build overview: https://docs.docker.com/build/
- Buildx: https://docs.docker.com/build/buildx/
- Cache optimisation: https://docs.docker.com/build/cache/optimize/
- Build secrets: https://docs.docker.com/build/building/secrets/
