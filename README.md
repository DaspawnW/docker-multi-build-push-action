# Build and push in parallel to GitHub Container Registry (GHCR) and public Elastic Container Registry (public ECR)

Inputs:

| Name               | Required                | Descrption                                                     |
|--------------------|-------------------------|----------------------------------------------------------------|
| docker-tag         | true                    | Docker tag to push                                             |
| platforms          | false                   | Supported platforms for multiarch (defaults to `linux/amd64`)  |
| ghcr-enabled       | true                    | Enable or disable GHCR push                                    |
| ghcr-token         | if ghcr-enabled == true | Sets the GITHUB_TOKEN secret                                   |
| ecr-enabled        | true                    | Enable or disable public ECR push                              |
| ecr-role-to-assume | if ecr-enabled == true  | Role to assume for public ecr push                             |
| ecr-repository-url | if ecr.enabled == true  | ECR Repository URI                                             |

Example:

```yaml
on: [ push ]

jobs:
  hello_world_job:
    runs-on: ubuntu-latest
    name: A job to say hello
    permissions:
      id-token: write # required for public ecr auth
      contents: read  # required for checkout
      packages: write # required for ghcr push
    steps:
      - uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1    
    
      - uses: daspawnw/docker-multi-build-push-action@master
        with:
          docker-tag: "latest"
          ghcr-enabled: "true"
          ghcr-token: "${{ secrets.GITHUB_TOKEN }}"
          ecr-enabled: "true"
          ecr-role-to-assume: "arn:aws:iam::123456789012:role/assumed-role-for-push"
          ecr-repository-url: "public.ecr.aws/daspawnw/vault-crd"
```