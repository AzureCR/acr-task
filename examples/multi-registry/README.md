# Managing multiple registries

This example shows how you can leverage the Docker CLI and reuse the Docker socket to pull an image from one Azure Container Registry, retag it, and push the newly tagged image to a different Azure Container Registry.

We use the `values.yaml` file which has some safe defaults to provide the registry names and usernames, and pass in `--set` as a way to provide sensitive password information.

## Local build

```sh
docker run --rm  -v $(pwd):/workspace --workdir /workspace -v /var/run/docker.sock:/var/run/docker.sock acb exec --homevol $(pwd) --steps examples/multi-registry/acb.yaml --values examples/multi-registry/values.yaml --id ehotinger --set "regOnePw=foo" --set "regTwoPw=bar"
```

## Remote build

TODO: not supported yet.