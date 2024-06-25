# Fortio as ContainerApp

To deploy, target your space and run:
```
tanzu deploy --from-build pre-built

# OR, if you're from the repo root:
tanzu deploy --from-build tanzu-demo/pre-built
```

The package includes an HTTPRoute, so your space should use the mutli-cloud
ingress trait.

## Usage

Once you've deployed your app, and you have a URL you can reach, the main
endpoint of use is `fortio.your.domain/fortio`. This will show a webpage with
various tools to use.

For more info, consult the [main README](../README.md).

## Building

You do not need to build this app yourself. The pre-built directory holds all
you need.

However, if you need to rebuild it, you could do that.

The app was built from the root of this repo following this guide: https://docs.vmware.com/en/VMware-Tanzu-Platform/services/create-manage-apps-tanzu-platform-k8s/how-to-build-and-deploy-from-source.html

*Note:* follow Option 2 to build and deploy separately.

### Special Modifications

The fortio app needs a command to run. However, building from `tanzu build`
doesn't seed that correctly. ContainerApp also does not yet support
`spec.args`.

To get around this, after building, you need to pull the imgpkg bundle from the
package resource. Find the package yaml at:
```
tanzu-demo/pre-built/apps.tanzu.vmware.com.ContainerApp/fortio/kubernetes-carvel-package/output/package.yml
```

Then, copy out the bundle to your machine:
```
imgpkg pull -b <your bundle> -o tanzu-demo/hack
```

Then, you must add a custom overlay into your bundle
```
cp tanzu-demo/fortio-deployment-overlay.yml tanzu-demo/hack/config
```

After that, you can rebuild and push your bundle:
```
imgpkg push -b us.gcr.io/daisy-284300/ck/app-fortio -f tanzu-demo/hack
```

Take the resulting image digest, and put it back into the package yaml.
```
tanzu-demo/pre-built/apps.tanzu.vmware.com.ContainerApp/fortio/kubernetes-carvel-package/output/package.yml
```

You can now deploy your built app.


