# control-plane-config

This repository is a sketch of a control plane project.

A control plane project is a directory containining all of the configuration
needed to build a functioning control plane.

The project uses a `crossplane.yaml` like any `Configuration`. It can be built
as typical `xpkg` OCI image, or a special `ConfigurationSource` manifest.

This command automatically creates a `ConfigurationSource` in `sources/`:

```shell
crossplane xpkg build --type=source
```

You can then apply the `ConfigurationSource`. This makes the configuration
available to be installed on the control plane:

```shell
kubectl apply -f sources/
```

Finally, install the `Configuration`:

```yaml
apiVersion: pkg.crossplane.io/v1
kind: Configuration
metadata:
  name: example-control-plane
spec:
  type: Source
  sourceName: example-control-plane
  init:
    apiVersion: example.crossplane.io/v1
    kind: XInitConfig
    spec:
      vaultServer: http://vault.vault-system:8200
      irsaRoleARN: arn:aws:iam::123456789012:role/some-role
```

Note that the `Configuration` references a `sourceName`, not a `package`.

The `Configuration` also specifies an initialization XR. This XR is defined in
the `init-api/` directory. The `Configuration` uses this XR to intialize (and
parameterize) a `DeploymentRuntimeConfig`, `ProviderConfig`, etc.