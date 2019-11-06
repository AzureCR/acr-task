# ACR Task Examples

## Encoding acr-task.yaml

In some cases, you may not want to git clone a repo for each build. In this example you can simply create the acr task by encoding an acr-task.yaml as part of the task creation. 

The following sample takes the contents of `acr-task.yaml` and encodes it into the `-f` parameter using  `-`

- Create the task
  ```sh
  cat hello-world.yaml | \
    az acr task create \
    -n hello-world \
    -f - \
    -c /dev/null
  ```

- Run the task

  ```sh
  az acr task run -n hello-world
  ```

- Delete the task

  ```sh
  az acr task delete -n hello-world
  ```

## Using the `az cli` With Tasks

The az cli can be run as an image to execute a series of commands, such as promoting an image from one registry or repo to another using [acr import][acr-import]

- Create a task, with an encoded task.yaml

  ```sh
  cat az-cli.yaml | \
    az acr task create \
    -n az-cli \
    --assign-identity \
    -f - \
    -c /dev/null
  ```

- Assign the identity of the task, access to the registry
  Note: ACR_NAME should equal your registry name. eg:`demo42`

  ```sh
  az role assignment create \
    --role Contributor \
    --assignee-object-id $(az acr task show \
        -n az-cli \
        --query identity.principalId \
        -o tsv) \
    --assignee-principal-type ServicePrincipal \
    --scope $(az acr show \
      -n ${ACR_NAME} \
      --query id -o tsv)
  ```

- Run the task
  Note the output will only list the registries the identity of the task has access to
  
  ```sh
  az acr task run -n az-cli
  ```

- Delete the task

  ```sh
  az acr task delete -n az-cli
  ```
