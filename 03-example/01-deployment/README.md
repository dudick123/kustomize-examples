
# Examples

https://antonputra.com/kubernetes/kubernetes-kustomize-tutorial/

https://blog.stack-labs.com/code/kustomize-101/

## Steps

### Create the directory structure

Create a directory structure like the following:
```
.
├── README.md
├── dev
├── prod
└── templates
    ├── base
    │   ├── deployment.yaml
    │   └── kustomization.yaml
    └── environments
        ├── production
        │   ├── deployment.yaml
        │   ├── kustomization.yaml
        │   └── namespace.yaml
        └── staging
            └── kustomization.yaml
```

### Run kustomize binary/exec

Navigate to the root directory ```01-deployment```

Run the kustomze command to generate output ```kustomize <env_template_path> -o <output_path>```. What we baasically want to do is take the templates and output them to the files that we are actually going to use for the deployment. We are not going to run any ```kubectl apply -k environments/staging```. We want to ensure that all yaml is visible and reviewable. To do this, we need to direct the template to the folder that will be referenced by Argo CD. In this case, the ```manifests/dev``` and ```manifests/prod``` directories wouuld be referenced by the Argo CD application in the enviornmnet.

Let's run a tree over the ```manifests``` directory:

```
tree ./manifests 
./manifests
├── dev
└── prod
```

Let's generate the dev environment manifests first:
```
kustomize build ./templates/environments/dev -o ./manifests/dev/deployment.yaml
```

We should now have a directory tree like the following:
```
tree ./manifests
./manifests
├── dev
│   └── deployment.yaml
└── prod
```

Let's try the same thing for Prod.
```
kustomize build ./templates/environments/prod -o ./manifests/prod/
```

Here is the tree:
```
tree ./manifests                                                  
./manifests
├── dev
│   └── deployment.yaml
└── prod
    ├── apps_v1_deployment_production-nginx-deployment.yaml
    └── v1_namespace_production.yaml
```

When more than one files is generated, as is the case with prod, the file names carry a very specific pattern of using the elements in the yaml. The pattern looks something like this: ```<apiVersion>_<kind>_<name>.yaml```. Which is fine, becasue it is consistent.

There may be some other underlying capabilities in Kustomize that allow you to target specifc file names. Have not found anything yet.

### Final Directory Structure

```
.
├── README.md
├── manifests
│   ├── dev
│   │   └── deployment.yaml
│   └── prod
│       ├── apps_v1_deployment_production-nginx-deployment.yaml
│       └── v1_namespace_production.yaml
└── templates
    ├── base
    │   ├── deployment.yaml
    │   └── kustomization.yaml
    └── environments
        ├── dev
        │   └── kustomization.yaml
        └── prod
            ├── deployment.yaml
            ├── kustomization.yaml
            └── namespace.yaml
```