# Apache Knox Chart

[Apache Knox](https://knox.apache.org/) is an Application Gateway for interacting with the REST APIs and UIs of Apache Hadoop deployments. This chart is primarily intended to access HDFS from outside a Kubernetes cluster using simple REST APIs. 

This chart uses uses docker image [pfisterer/apache-knox-docker](https://github.com/pfisterer/apache-knox-docker). 

## Installing the Chart (requires an existing Hadoop deployment)

Make sure that your Hadoop installation has WebHDFS enabled (by setting `hdfs.webhdfs.enabled` to `true` when using [stable/hadoop]([../hadoop](https://github.com/helm/charts/tree/master/stable/hadoop))).

Add repository to helm

```bash
helm repo add pfisterer-knox https://pfisterer.github.io/apache-knox-helm/
helm repo update
```

To install the chart with the release name `knox`:

```
$ helm install --name knox pfisterer-knox/apache-knox-helm
```

For correctly proxying to your HDFS instance, set `knox.hadoop.nameNodeUrl`, `knox.hadoop.resourceManagerUrl`, and `knox.hadoop.webHdfsUrl`. Example: 

```
$ helm install --name knox pfisterer-knox/apache-knox-helm \
		--set "knox.hadoop.nameNodeUrl=hdfs://your-namenode-svc:9000/"  \
		--set "knox.hadoop.resourceManagerUrl=http://your-resource-mgr-svc:8088/ws" \
		--set "knox.hadoop.webHdfsUrl=http://your-namenode-svc:50070/webhdfs"
```

Afterwards, you should be able to access YARN-UI (`/yarn`), WEBHDFS (`/webhdfs`), and WEBHDFS UI (`/hdfs`).

## Configuration

The following table lists the configurable parameters of the Apache Knox chart and their default values.

| Parameter                        | Description                               | Default                                                                           |
| -------------------------------- | ----------------------------------------- | --------------------------------------------------------------------------------- |
| `knox.image.repository`          | Docker image for Apache Knox              | [farberg/apache-knox-docker](https://hub.docker.com/r/farberg/apache-knox-docker) |
| `knox.image.tag`                 | Docker image tag                          | `1.6.1`                                                                           |
| `knox.image.pullPolicy`          | Pull policy for the images                | `IfNotPresent`                                                                    |
| `knox.servicetype`               | Type of service exposure for Apache Knox  | `ClusterIP`                                                                       |
| `knox.hadoop.nameNodeUrl`        | URL to Hadoop's name node                 | `hdfs://nn:9000/webhdfs`                                                          |
| `knox.hadoop.resourceManagerUrl` | URL to Hadoop's Resource Manager          | `http://rm:8088/ws`                                                               |
| `knox.hadoop.webHdfsUrl`         | URL to Hadoop's webhdfs                   | `http://nn:9870/webhdfs`                                                          |
| `knox.hadoop.hdfsUIUrl`          | URL to Hadoop's hdfs web UI               | `http://nn:9870/`                                                                 |
| `knox.hadoop.yarnUIUrl`          | URL to YARN's web UI                      | `http://yarn-ui:8088/`                                                            |
| `knox.users.admin.pw`            | Password for user `admin`                 | `admin-password`                                                                  |
| `knox.users.root.pw`             | Password for user `root`                  | `root-password`                                                                   |
| `knox.users.sam.pw`              | Password for user `sam`                   | `sam-password`                                                                    |
| `knox.users.tom.pw`              | Password for user `tom`                   | `tom-password`                                                                    |
| `knox.gateway.logLevel`          | Log4j log level for the gateway component | `DEBUG`                                                                           |
| `knox.ldap.logLevel`             | Log4j log level for the LDAP server       | `INFO`                                                                            |
| `ingress.enabled`                | If true, Ingress will be created          | `false`                                                                           |
| `ingress.annotations`            | Ingress annotations                       | `{}`                                                                              |
| `ingress.labels`                 | Ingress labels                            | `{}`                                                                              |
| `ingress.path`                   | Ingress service path                      | `/`                                                                               |
| `ingress.hosts`                  | Ingress hostnames                         | `[]`                                                                              |
| `ingress.tls`                    | Ingress TLS configuration (YAML)          | `[]`                                                                              |

## Related Charts

- The [Hadoop Chart](https://github.com/helm/charts/tree/master/stable/hadoop)

## Development

Help is always appreciated. Please create pull requests.

### Open Issues

- Enable the readiness probe in <templates/knox-dep.yaml> (requires authentication)
- Support additional services (not just HDFS)

### Upload a new version of the chart

```bash
helm lint
helm package .
mv apache-knox-helm-*.tgz docs/
helm repo index docs/ --url https://pfisterer.github.io/apache-knox-helm/
git add docs/
git commit -a -m "Updated helm repository"
git push origin master
```

## Changes

Version 0.1.11
- Change from deprecated `networking.k8s.io/v1beta1` to `networking.k8s.io/v1`

Version 0.1.10
- Update to Apache Knox 1.6.1

Version 0.1.9
- Grant access to any user (root, admin, sam, tom)

Version 0.1.8
- Add user `root` and option to change its password

Version 0.1.7
- Update to Apache Knox 1.5.0
