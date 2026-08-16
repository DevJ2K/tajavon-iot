# tajavon-iot

Configuration repository for the **Inception-of-Things** project (part 3).

This repository holds the *desired state* of the application deployed in the
`dev` namespace of the k3d cluster. Argo CD watches it and reconciles the
cluster against whatever is committed here — nothing is ever applied by hand
with `kubectl`.

The infrastructure that builds the cluster and installs Argo CD lives in a
separate repository, under `p3/`:
<https://github.com/DevJ2K/inception-of-things>

## Contents

| File | Role |
|------|------|
| `deployment.yaml` | The application: `wil42/playground` image and container port `8888` |
| `service.yaml` | NodePort `30888`, mapped to host port `8888` by the k3d cluster |

## Deploying a new version

The image tag in `deployment.yaml` is the only thing to change. Committing and
pushing is the whole deployment procedure.

### 1. Update the image tag
```sh
sed -i 's|wil42/playground:v1|wil42/playground:v2|' deployment.yaml
# sed -i '' 's|wil42/playground:v1|wil42/playground:v2|' deployment.yaml (on macOS)
```

### 2. Commit and push
```sh
git add .
git commit -m "deploy v2"
git push
```

---

Argo CD polls this repository roughly every three minutes. To apply the change
immediately, press **Sync** in the Argo CD interface.

Verify from the cluster host:

```sh
curl http://localhost:8888/
# {"status":"ok", "message": "v2"}
```
