::: {#header}
# Camel Kubernetes plugin
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::::::::::::::::::: {#preamble}
:::::::::::::::::::: sectionbody
::: paragraph
This plugin helps you to get started with running Camel applications on
Kubernetes. Please make sure to meet these prerequisites for running
Camel integrations on Kubernetes:
:::

::: ulist
- Install a Kubernetes command line tooling
  ([kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl))

- Connect to a running Kubernetes cluster where you want to run the
  Camel integration
:::

::: paragraph
You can connect to a remote Kubernetes cluster or set up a local
cluster. To set up a local Kubernetes cluster, you have a variety of
options.
:::

::: ulist
- [Kind](https://kind.sigs.k8s.io/docs/user/quick-start/)

- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
:::

::: paragraph
Camel JBang is able to interact with any of these Kubernetes platforms
(remote or local).
:::

::: paragraph
Running Camel routes on Kubernetes is quite simple with Camel JBang. In
fact, you can develop and test your Camel route locally with Camel JBang
and then promote the same source to running it as an integration on
Kubernetes.
:::

::: paragraph
The Camel JBang Kubernetes functionality is provided as a command
plugin. This means you need to enable the `kubernetes` plugin first to
use the subcommands in Camel JBang.
:::

:::: listingblock
::: content
``` highlight
camel plugin add kubernetes
```
:::
::::

::: paragraph
You should see the `kubernetes` plugin listed as an installed plugin.
:::

:::: listingblock
::: content
``` highlight
camel plugin get
```
:::
::::

:::: listingblock
::: content
``` highlight
 NAME        COMMAND     DEPENDENCY                                      DESCRIPTION
 kubernetes  kubernetes  org.apache.camel:camel-jbang-plugin-kubernetes  Run Camel applications on Kubernetes
```
:::
::::

::: paragraph
Now Camel JBang is able to run the subcommands offered by the plugin.
You can inspect the help page to see the list of available plugin
subcommands.
:::

:::: listingblock
::: content
``` highlight
camel kubernetes --help
```
:::
::::
::::::::::::::::::::
:::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::: sect1
## Kubernetes export {#_kubernetes_export}

::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The Kubernetes plugin works with the Camel JBang export functionality.
The project export generates a proper Maven/Gradle project following one
of the available runtime types Quarkus, SpringBoot or camel-main.
:::

::: paragraph
In case you export the project with the Kubernetes plugin the exported
project holds all information (e.g. sources, properties, dependencies,
etc.) and is ready to build, push and deploy the application to
Kubernetes, too. The export generates a Kubernetes manifest
(kubernetes.yml) that holds all resources (e.g. Deployment, Service,
ConfigMap) required to run the application on Kubernetes.
:::

::: paragraph
You can create a project export with the following command.
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export route.yaml --dir some/path/to/project
```
:::
::::

::: paragraph
The command receives one or more source files (e.g. Camel routes), and
performs the export. As a result, you will find the Maven/Gradle project
sources generated into the given project path.
:::

::: paragraph
The default runtime of the project is Quarkus. You can adjust the
runtime with an additional command option `--runtime=quarkus`.
:::

::: paragraph
If you want to run this application on Kubernetes, you need to build the
container image, push it to a registry and deploy the application to
Kubernetes.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | The Camel JBang Kubernetes plugin |
| Tip                               | provides a `run` command that     |
| :::                               | combines these steps (export,     |
|                                   | container image build, push,      |
|                                   | deploy) into a single command.    |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
You can now navigate to the generated project folder and build the
project artifacts for instance with this Maven command.
:::

:::: listingblock
::: content
``` highlight
./mvnw package -Dquarkus.container-image.build=true
```
:::
::::

::: paragraph
According to the runtime type (e.g., quarkus) defined for the export
this builds and creates a Quarkus application artifact JAR in the Maven
build output folder (e.g. `target/route-1.0-SNAPSHOT.jar`).
:::

::: paragraph
The option `-Dquarkus.container-image.build=true` also builds a
container image that is ready for deployment to Kubernetes. More
precisely, the exported project uses the very same tooling and options
as an arbitrary Quarkus/SpringBoot application would do. This means you
can easily customize the container image and all settings provided by
the runtime provider (e.g., Quarkus or SpringBoot) after the export.
:::

::: paragraph
The Kubernetes deployment resources are automatically generated with the
export, too.
:::

::: paragraph
You can find the Kubernetes manifest in
`src/main/kubernetes/kubernetes.yml`.
:::

::: paragraph
For instance with the option `-Dquarkus.kubernetes.deploy=true` uses
this manifest to trigger the Kubernetes deployment as part of the Maven
build.
:::

:::: listingblock
::: content
``` highlight
./mvnw package -Dquarkus.kubernetes.deploy=true
```
:::
::::

::: paragraph
You will see the Deployment on Kubernetes shortly after this command has
finished.
:::

::: paragraph
The Camel JBang Kubernetes export command provides several options to
customize the exported project.
:::

+-----------------+-----------------------------------------------------+
| Option          | Description                                         |
+=================+=====================================================+
| `-              | The trait profile to use for the deployment.        |
| -trait-profile` |                                                     |
+-----------------+-----------------------------------------------------+
| `--s            | The service account used to run the application.    |
| ervice-account` |                                                     |
+-----------------+-----------------------------------------------------+
| `--dependency`  | Adds dependency that should be included, use        |
|                 | \"camel:\" prefix for a Camel component,            |
|                 | \"mvn:org.my:app:1.0\" for a Maven dependency.      |
+-----------------+-----------------------------------------------------+
| `--             | Maven/Gradle build properties (syntax:              |
| build-property` | \--build-property=prop1=foo)                        |
+-----------------+-----------------------------------------------------+
| `--property`    | Add a runtime property or properties file from a    |
|                 | path, a config map or a secret (syntax:             |
|                 | \[my-key=my-value,file:/path/                       |
|                 | to/my-conf.properties,\[configmap,secret\]:name\]). |
+-----------------+-----------------------------------------------------+
| `--config`      | Add a runtime configuration from a ConfigMap or a   |
|                 | Secret (syntax: \[configmap,secret\]:name\[/key\],  |
|                 | where name represents the configmap/secret name and |
|                 | key optionally represents the configmap/secret key  |
|                 | to be filtered).                                    |
+-----------------+-----------------------------------------------------+
| `--resource`    | Add a runtime resource from a Configmap or a Secret |
|                 | (syntax:                                            |
|                 | \[configmap,secret\]:name\[/key\]\[@path\], where   |
|                 | name represents the configmap/secret name, key      |
|                 | optionally represents the configmap/secret key to   |
|                 | be filtered and path represents the destination     |
|                 | path).                                              |
+-----------------+-----------------------------------------------------+
| `--open-api`    | Add an OpenAPI spec (syntax:                        |
|                 | \[configmap,file\]:name).                           |
+-----------------+-----------------------------------------------------+
| `--env`         | Set an environment variable in the integration      |
|                 | container, for instance \"-e MY_VAR=my-value\".     |
+-----------------+-----------------------------------------------------+
| `--volume`      | Mount a volume into the integration container, for  |
|                 | instance \"-v pvcname:/container/path\".            |
+-----------------+-----------------------------------------------------+
| `--connect`     | A Service that the integration should bind to,      |
|                 | specified as                                        |
|                 | \[\[apigroup/\]version:\]kind:\[namespace/\]name.   |
+-----------------+-----------------------------------------------------+
| `--source`      | Add the source file to your integration, this is    |
|                 | added to the list of files listed as arguments of   |
|                 | the command.                                        |
+-----------------+-----------------------------------------------------+
| `--annotation`  | Add an annotation to the integration. Use name      |
|                 | values pairs like \"\--annotation                   |
|                 | my.company=hello\".                                 |
+-----------------+-----------------------------------------------------+
| `--label`       | Add a label to the integration. Use name values     |
|                 | pairs like \"\--label my.company=hello\".           |
+-----------------+-----------------------------------------------------+
| `--trait`       | Add a trait configuration to the integration. Use   |
|                 | name values pairs like \"\--trait                   |
|                 | trait.name.config=hello\".                          |
+-----------------+-----------------------------------------------------+
| `--image`       | An image built externally (for instance via CI/CD). |
|                 | Enabling it will skip the integration build phase.  |
+-----------------+-----------------------------------------------------+
| `--             | The image registry to hold the app container image. |
| image-registry` |                                                     |
+-----------------+-----------------------------------------------------+
| `--image-group` | The image registry group used to push images to.    |
+-----------------+-----------------------------------------------------+
| `-              | The image builder used to build the container image |
| -image-builder` | (e.g. docker, jib, podman, s2i).                    |
+-----------------+-----------------------------------------------------+
| `--             | List of target platforms. Each platform is defined  |
| image-platform` | using os and architecture (e.g. linux/amd64).       |
+-----------------+-----------------------------------------------------+
| `--base-image`  | The base image that is used to build the container  |
|                 | image from (default is                              |
|                 | eclipse-temurin:\<java-version\>).                  |
+-----------------+-----------------------------------------------------+
| `--r            | Optional Docker registry mirror where to pull       |
| egistry-mirror` | images from when building the container image.      |
+-----------------+-----------------------------------------------------+
| `               | The target cluster type. Special configurations may |
| --cluster-type` | be applied to different cluster types such as Kind  |
|                 | or Minikube.                                        |
+-----------------+-----------------------------------------------------+
| `--profile`     | The developer                                       |
|                 | [profile](manual::camel-jbang.html#_using_profiles) |
|                 | to use a specific configuration in configuration    |
|                 | files using the naming style                        |
|                 | `application-<profile>.properties`.                 |
+-----------------+-----------------------------------------------------+

::: paragraph
The Kubernetes plugin export command also inherits all options from the
arbitrary Camel JBang export command.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | See the possible options by       |
| Tip                               | running:                          |
| :::                               | `camel kubernetes export --help`  |
|                                   | for more details.                 |
+-----------------------------------+-----------------------------------+
:::

::::::::::::: sect2
### Kubernetes Container Health Probes {#_kubernetes_container_health_probes}

::: paragraph
The Observability Services component is set by default in the project
created by the export command, then the health path probes are prefixed
with the `/observe` endpoint, regardless if they are Quarkus or Spring
Boot.
:::

::: paragraph
These are the HTTP Container probe endpoints.
:::

::: paragraph
For Quarkus.
:::

:::: listingblock
::: content
``` highlight
/observe/health/live
/observe/health/ready
/observe/health/started
```
:::
::::

::: paragraph
For Spring Boot (there is no startup probe)
:::

:::: listingblock
::: content
``` highlight
/observe/health/liveness
/observe/health/readiness
```
:::
::::

::: paragraph
Note that these container probes are generated by jkube plugin, when you
run the `k8s:resource` to generate the manifests in the
`target/kubernetes/kubernetes.yml`.
:::

::: paragraph
You can export to a project and manually remove the
`camel-observability-services` artifact from the `pom.xml`.
:::
:::::::::::::
:::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Kubernetes manifest options {#_kubernetes_manifest_options}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The Kubernetes manifest (kubernetes.yml) describes all resources to
successfully run the application on Kubernetes. The manifest usually
holds the deployment, a service definition, config maps and much more.
:::

::: paragraph
You can use several options on the `export` command to customize this
manifest with the traits. The trait concept was born out of Camel K, and
the Camel K operator uses the traits to configure the Kubernetes
resources that are managed by an integration. You can use the same
options to also customize the Kubernetes manifest that is generated as
part of the project export.
:::

::: paragraph
The configuration of the traits is used by the given order:
:::

::: {.olist .arabic}
1.  Use the `--trait` command options values

2.  Any annotation starting with the prefix `trait.camel.apache.org/*`

3.  Any properties from the specific configuration
    `application-<profile>.properties` for the profile defined by the
    command option `--profile` with the prefix `camel.jbang.trait.*`

4.  Any properties from the default configuration
    `application.properties` with the prefix `camel.jbang.trait.*`
:::

::::::::::::::: sect2
### Container trait options {#_container_trait_options}

::: paragraph
The container specification is part of the Kubernetes Deployment
resource and describes the application container image, exposed ports
and health probes, for example.
:::

::: paragraph
The container trait is able to customize the container specification
with the following options:
:::

+-----------------+--------+--------------------------------------------+
| Property        | Type   | Description                                |
+=================+========+============================================+
| `               | `int`  | :::: content                               |
| container.port` |        | ::: paragraph                              |
|                 |        | To configure a different port exposed by   |
|                 |        | the container (default `8080`).            |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `conta          | `s     | :::: content                               |
| iner.port-name` | tring` | ::: paragraph                              |
|                 |        | To configure a different port name for the |
|                 |        | port exposed by the container. It defaults |
|                 |        | to `http` only when the `expose` parameter |
|                 |        | is true.                                   |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `containe       | `int`  | :::: content                               |
| r.service-port` |        | ::: paragraph                              |
|                 |        | To configure under which service port the  |
|                 |        | container port is to be exposed (default   |
|                 |        | `80`).                                     |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `container.ser  | `s     | :::: content                               |
| vice-port-name` | tring` | ::: paragraph                              |
|                 |        | To configure under which service port name |
|                 |        | the container port is to be exposed        |
|                 |        | (default `http`).                          |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `               | `s     | :::: content                               |
| container.name` | tring` | ::: paragraph                              |
|                 |        | The application container name.            |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `c              | `s     | :::: content                               |
| ontainer.image` | tring` | ::: paragraph                              |
|                 |        | The application container image to use for |
|                 |        | the Deployment.                            |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `container.ima  | `PullP | :::: content                               |
| ge-pull-policy` | olicy` | ::: paragraph                              |
|                 |        | The pull policy:                           |
|                 |        | Always\|Never\|IfNotPresent                |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `container.imag | `str   | :::: content                               |
| e-pull-secrets` | ing[]` | ::: paragraph                              |
|                 |        | The pull secrets for private registries    |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `contain        | `s     | :::: content                               |
| er.request-cpu` | tring` | ::: paragraph                              |
|                 |        | The minimum amount of CPU required.        |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `container.     | `s     | :::: content                               |
| request-memory` | tring` | ::: paragraph                              |
|                 |        | The minimum amount of memory required.     |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `conta          | `s     | :::: content                               |
| iner.limit-cpu` | tring` | ::: paragraph                              |
|                 |        | The maximum amount of CPU required.        |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `containe       | `s     | :::: content                               |
| r.limit-memory` | tring` | ::: paragraph                              |
|                 |        | The maximum amount of memory required.     |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+

::: paragraph
The syntax to specify container trait options is as follows:
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export Sample.java --trait container.[key]=[value]
```
:::
::::

::: paragraph
You may specify these options with the export command to customize the
container specification.
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export Sample.java --trait container.name=my-container --trait container.port=8088 --trait container.imagePullPolicy=IfNotPresent --trait container.request-cpu=0.005 --trait container.request-memory=100Mi
```
:::
::::

::: paragraph
This results in the following container specification in the Deployment
resource.
:::

:::: listingblock
::: content
``` highlight
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    camel.apache.org/integration: sample
  name: sample
spec:
  selector:
    matchLabels:
      camel.apache.org/integration: sample
  template:
    metadata:
      labels:
        camel.apache.org/integration: sample
    spec:
      containers:
      - image: quay.io/sample:1.0-SNAPSHOT #(1)
        imagePullPolicy: IfNotPresent #(2)
        name: my-container #(3)
        ports:
        - containerPort: 8088 #(4)
          name: http
          protocol: TCP
        resources:
          requests:
            memory: 100Mi
            cpu: '0.005'
```
:::
::::

::: {.colist .arabic}
1.  Container image running the application

2.  Customized image pull policy

3.  Custom container name

4.  Custom container port exposed
:::
:::::::::::::::

::::::::::::: sect2
### Labels and annotations {#_labels_and_annotations}

::: paragraph
You may need to add labels or annotations to the generated Kubernetes
resources. By default, the generated resources will have the label
`camel.apache.org/integration` set to the exported project name.
:::

::: paragraph
You can add labels and annotations with these options on the export
command:
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export Sample.java --annotation [key]=[value] --label [key]=[value]
```
:::
::::

::::: listingblock
::: title
Example
:::

::: content
``` highlight
camel kubernetes export Sample.java --annotation project.team=camel-experts
```
:::
:::::

:::: listingblock
::: content
``` highlight
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    project.team: camel-experts #(1)
  labels:
    camel.apache.org/integration: sample
  name: sample
spec:
  selector:
    matchLabels:
      camel.apache.org/integration: sample
  template:
    metadata:
      labels:
        camel.apache.org/integration: sample
    spec:
      containers:
      - image: quay.io/sample:1.0-SNAPSHOT
        name: sample
```
:::
::::

::: {.colist .arabic}
1.  Custom deployment annotation
:::
:::::::::::::

:::::::::::::::::: sect2
### Environment variables {#_environment_variables}

::: paragraph
The environment trait is there to set environment variables on the
container specification.
:::

::: paragraph
The environment trait provides the following configuration options:
:::

+-----------------+--------+--------------------------------------------+
| Property        | Type   | Description                                |
+=================+========+============================================+
| `en             | `[]s   | :::: content                               |
| vironment.vars` | tring` | ::: paragraph                              |
|                 |        | A list of environment variables to be      |
|                 |        | added to the integration container. The    |
|                 |        | syntax is KEY=VALUE, e.g.,                 |
|                 |        | `MY_VAR="my value"`. These take precedence |
|                 |        | over the previously defined environment    |
|                 |        | variables.                                 |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+

::: paragraph
The syntax to specify environment trait options is as follows:
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export Sample.java --trait environment.[key]=[value]
```
:::
::::

::: paragraph
There is also a shortcut option `--env` that you can use.
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export Sample.java --env [key]=[value]
```
:::
::::

::::: listingblock
::: title
Example
:::

::: content
``` highlight
camel kubernetes export Sample.java --trait environment.vars=MY_ENV=foo --env FOO_ENV=bar
```
:::
:::::

::: paragraph
This results in the following container specification in the Deployment
resource.
:::

:::: listingblock
::: content
``` highlight
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    camel.apache.org/integration: sample
  name: sample
spec:
  selector:
    matchLabels:
      camel.apache.org/integration: sample
  template:
    metadata:
      labels:
        camel.apache.org/integration: sample
    spec:
      containers:
      - image: quay.io/sample:1.0-SNAPSHOT
        name: sample
        env: #(1)
          - name: MY_ENV
            value: foo
          - name: FOO_ENV
            value: bar
```
:::
::::

::: {.colist .arabic}
1.  Environment variables set in the container specification
:::
::::::::::::::::::

:::::: sect2
### Service trait options {#_service_trait_options}

::: paragraph
The Service trait enhances the Kubernetes manifest with a Service
resource so that the application can be accessed by other components in
the same namespace. The service resource exposes the application with a
protocol (e.g., TCP/IP) on a given port and uses either `ClusterIP`,
`NodePort` or `LoadBalancer` type.
:::

::: paragraph
The Camel JBang plugin automatically inspects the Camel routes for
exposed Http services and adds the service resource when applicable.
This means when one of the Camel routes exposes a Http service (for
instance by using the `platform-http` component) the Kubernetes manifest
also creates a Kubernetes Service resource besides the arbitrary
Deployment.
:::

::: paragraph
You can customize the generated Kubernetes service resource with trait
options:
:::

+-----------------+--------+--------------------------------------------+
| Property        | Type   | Description                                |
+=================+========+============================================+
| `service.type`  | `s     | :::: content                               |
|                 | tring` | ::: paragraph                              |
|                 |        | The type of service to be used, either     |
|                 |        | \'ClusterIP\', \'NodePort\' or             |
|                 |        | \'LoadBalancer\'.                          |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `containe       | `int`  | :::: content                               |
| r.service-port` |        | ::: paragraph                              |
|                 |        | To configure under which service port the  |
|                 |        | container port is to be exposed (default   |
|                 |        | `80`).                                     |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `container.ser  | `s     | :::: content                               |
| vice-port-name` | tring` | ::: paragraph                              |
|                 |        | To configure under which service port name |
|                 |        | the container port is to be exposed        |
|                 |        | (default `http`).                          |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
::::::

::::::::: sect2
### Knative service trait options {#_knative_service_trait_options}

::: paragraph
[Knative serving](https://knative.dev/docs/serving/) defines a set of
resources on Kubernetes to handle Serverless workloads with automatic
scaling and scale-to-zero functionality.
:::

::: paragraph
When Knative serving is available on the target Kubernetes cluster, you
may want to use the Knative service resource instead of an arbitrary
Kubernetes service resource. The Knative service trait will create such
a resource as part of the Kubernetes manifest.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `knative-service` trait is    |
| Note                              | disabled by default, you need to  |
| :::                               | enable the Knative service trait  |
|                                   | with                              |
|                                   | `--tr                             |
|                                   | ait knative-service.enabled=true` |
|                                   | option. Otherwise, the Camel      |
|                                   | JBang export will always create   |
|                                   | an arbitrary Kubernetes service   |
|                                   | resource.                         |
+-----------------------------------+-----------------------------------+
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | If you enable the knative-service |
| Note                              | trait and deploys to minikube and |
| :::                               | uses the docker builder to build  |
|                                   | the container image in the        |
|                                   | minikube registry addon, it's     |
|                                   | likely the container image        |
|                                   | published won't contain the       |
|                                   | digest value, and                 |
|                                   | [knative-serving has a            |
|                                   | verification to check the image   |
|                                   | digest](https://knative.de        |
|                                   | v/docs/serving/configuration/depl |
|                                   | oyment/#skipping-tag-resolution), |
|                                   | so the pod will fail to start     |
|                                   | with the error message            |
|                                   | `fa                               |
|                                   | iled to resolve image to digest`, |
|                                   | so for development purposes the   |
|                                   | kubernetes plugin will skip this  |
|                                   | validation by adding the property |
|                                   | `registries-skippi                |
|                                   | ng-tag-resolving: localhost:5000` |
|                                   | to the                            |
|                                   | `configmap/config-deployment` in  |
|                                   | `knative-serving` namespace, if   |
|                                   | you want to disable this          |
|                                   | automatic setting, you can set    |
|                                   | the `--disable-auto` parameter to |
|                                   | disable the automatic cluster     |
|                                   | configuration, however disabling  |
|                                   | it won't set other automatic      |
|                                   | configuration for the cluster.    |
|                                   | You may also want to manually set |
|                                   | that property, so this plugin     |
|                                   | won't set it even with the        |
|                                   | automatic cluster detection.      |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
WARN: Currently knative-service doesn't work in OpenShift, work is
underway to fix it.
:::

::: paragraph
The trait offers following options for customization:
:::

+-----------------+--------+--------------------------------------------+
| Property        | Type   | Description                                |
+=================+========+============================================+
| `knative-s      | `bool` | :::: content                               |
| ervice.enabled` |        | ::: paragraph                              |
|                 |        | Can be used to enable or disable a trait.  |
|                 |        | All traits share this common property.     |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `knative-servi  | `      | :::: content                               |
| ce.annotations` | map[st | ::: paragraph                              |
|                 | ring]s | The annotations added to route. This can   |
|                 | tring` | be used to set knative service-specific    |
|                 |        | annotations CLI usage example: -t          |
|                 |        | \"knative-service.annotations.\'ha         |
|                 |        | proxy.router.openshift.io/balance\'=true\" |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `knative        | `s     | ::::: content                              |
| -service.class` | tring` | ::: paragraph                              |
|                 |        | Configures the Knative autoscaling class   |
|                 |        | property (e.g., to set                     |
|                 |        | `hpa.autoscaling.knative.dev` or           |
|                 |        | `kpa.autoscaling.knative.dev`              |
|                 |        | autoscaling).                              |
|                 |        | :::                                        |
|                 |        |                                            |
|                 |        | ::: paragraph                              |
|                 |        | Refer to the Knative documentation for     |
|                 |        | more information.                          |
|                 |        | :::                                        |
|                 |        | :::::                                      |
+-----------------+--------+--------------------------------------------+
| `knati          | `s     | ::::: content                              |
| ve-service.auto | tring` | ::: paragraph                              |
| scaling-metric` |        | Configures the Knative autoscaling metric  |
|                 |        | property (e.g., to set `concurrency` based |
|                 |        | or `cpu` based autoscaling).               |
|                 |        | :::                                        |
|                 |        |                                            |
|                 |        | ::: paragraph                              |
|                 |        | Refer to the Knative documentation for     |
|                 |        | more information.                          |
|                 |        | :::                                        |
|                 |        | :::::                                      |
+-----------------+--------+--------------------------------------------+
| `knati          | `int`  | ::::: content                              |
| ve-service.auto |        | ::: paragraph                              |
| scaling-target` |        | Sets the allowed concurrency level or CPU  |
|                 |        | percentage (depending on the autoscaling   |
|                 |        | metric) for each Pod.                      |
|                 |        | :::                                        |
|                 |        |                                            |
|                 |        | ::: paragraph                              |
|                 |        | Refer to the Knative documentation for     |
|                 |        | more information.                          |
|                 |        | :::                                        |
|                 |        | :::::                                      |
+-----------------+--------+--------------------------------------------+
| `knative-ser    | `int`  | ::::: content                              |
| vice.min-scale` |        | ::: paragraph                              |
|                 |        | The minimum number of Pods that should be  |
|                 |        | running at any time for the integration.   |
|                 |        | It's **zero** by default, meaning that the |
|                 |        | integration is scaled down to zero when    |
|                 |        | not used for a configured amount of time.  |
|                 |        | :::                                        |
|                 |        |                                            |
|                 |        | ::: paragraph                              |
|                 |        | Refer to the Knative documentation for     |
|                 |        | more information.                          |
|                 |        | :::                                        |
|                 |        | :::::                                      |
+-----------------+--------+--------------------------------------------+
| `knative-ser    | `int`  | ::::: content                              |
| vice.max-scale` |        | ::: paragraph                              |
|                 |        | An upper bound for the number of Pods that |
|                 |        | can be running in parallel for the         |
|                 |        | integration. Knative has its own cap value |
|                 |        | that depends on the installation.          |
|                 |        | :::                                        |
|                 |        |                                            |
|                 |        | ::: paragraph                              |
|                 |        | Refer to the Knative documentation for     |
|                 |        | more information.                          |
|                 |        | :::                                        |
|                 |        | :::::                                      |
+-----------------+--------+--------------------------------------------+
| `kna            | `s     | :::: content                               |
| tive-service.ro | tring` | ::: paragraph                              |
| llout-duration` |        | Enables to gradually shift traffic to the  |
|                 |        | latest Revision and sets the rollout       |
|                 |        | duration. It's disabled by default and     |
|                 |        | must be expressed as a Golang              |
|                 |        | `time.Duration` string representation,     |
|                 |        | rounded to a second precision.             |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `knative-serv   | `s     | ::::: content                              |
| ice.visibility` | tring` | ::: paragraph                              |
|                 |        | Setting `cluster-local`, Knative service   |
|                 |        | becomes a private service. Specifically,   |
|                 |        | this option applies the                    |
|                 |        | `networking.knative.dev/visibility` label  |
|                 |        | to Knative service.                        |
|                 |        | :::                                        |
|                 |        |                                            |
|                 |        | ::: paragraph                              |
|                 |        | Refer to the Knative documentation for     |
|                 |        | more information.                          |
|                 |        | :::                                        |
|                 |        | :::::                                      |
+-----------------+--------+--------------------------------------------+
| `kn             | `int`  | ::::: content                              |
| ative-service.t |        | ::: paragraph                              |
| imeout-seconds` |        | The maximum duration in seconds that the   |
|                 |        | request instance is allowed to respond to  |
|                 |        | a request. This field propagates to the    |
|                 |        | integration Pod's                          |
|                 |        | terminationGracePeriodSeconds.             |
|                 |        | :::                                        |
|                 |        |                                            |
|                 |        | ::: paragraph                              |
|                 |        | Refer to the Knative documentation for     |
|                 |        | more information.                          |
|                 |        | :::                                        |
|                 |        | :::::                                      |
+-----------------+--------+--------------------------------------------+
:::::::::

::::::::: sect2
### Connecting to Knative {#_connecting_to_knative}

::: paragraph
The previous section described how the exported Apache Camel application
can leverage the Knative service resource with auto-scaling as part of
the deployment to Kubernetes.
:::

::: paragraph
Apache Camel also provides a Knative component that makes you easily
interact with [Knative eventing](https://knative.dev/docs/eventing/) and
[Knative serving](https://knative.dev/docs/serving/).
:::

::: paragraph
The Knative component enables you to exchange data with the Knative
eventing broker and other Knative services deployed on Kubernetes. The
Camel JBang Kubernetes plugin provides some autoconfiguration options
when connecting with the Knative component. The export command assists
you in configuring both the Knative component and the Kubernetes
manifest for connecting to Knative resources on the Kubernetes cluster.
:::

::: paragraph
You can configure the Knative component with the Knative trait.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `knative` trait is disabled   |
| Note                              | by default, you need to enable    |
| :::                               | the Knative trait with            |
|                                   | `--trait knative.enabled=true`    |
|                                   | option.                           |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The trait offers the following options for customization:
:::

+-----------------+--------+--------------------------------------------+
| Property        | Type   | Description                                |
+=================+========+============================================+
| `k              | `bool` | :::: content                               |
| native.enabled` |        | ::: paragraph                              |
|                 |        | Can be used to enable or disable a trait.  |
|                 |        | (default: true)                            |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `knative        | `s     | :::: content                               |
| .configuration` | tring` | ::: paragraph                              |
|                 |        | Can be used to inject a Knative complete   |
|                 |        | configuration in JSON format               |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `knative        | `[]s   | ::::: content                              |
| .channel-sinks` | tring` | ::: paragraph                              |
|                 |        | List of channels used as destination of    |
|                 |        | camel routes. Can contain simple channel   |
|                 |        | names or full Camel URIs.                  |
|                 |        | :::                                        |
|                 |        |                                            |
|                 |        | ::: paragraph                              |
|                 |        | Refer to the Knative documentation for     |
|                 |        | more information.                          |
|                 |        | :::                                        |
|                 |        | :::::                                      |
+-----------------+--------+--------------------------------------------+
| `knative.c      | `[]s   | :::: content                               |
| hannel-sources` | tring` | ::: paragraph                              |
|                 |        | List of channels used as the source of     |
|                 |        | camel routes. Can contain simple channel   |
|                 |        | names or full Camel URIs.                  |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `knative.       | `[]s   | :::: content                               |
| endpoint-sinks` | tring` | ::: paragraph                              |
|                 |        | List of endpoints used as destination of   |
|                 |        | camel routes. Can contain simple endpoint  |
|                 |        | names or full Camel URIs.                  |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `knative.en     | `[]s   | :::: content                               |
| dpoint-sources` | tring` | ::: paragraph                              |
|                 |        | List of endpoints used as sources of camel |
|                 |        | routes. Can contain simple endpoint names  |
|                 |        | or full Camel URIs.                        |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `knati          | `[]s   | :::: content                               |
| ve.event-sinks` | tring` | ::: paragraph                              |
|                 |        | List of endpoints used as destination of   |
|                 |        | integration routes. Can contain simple     |
|                 |        | endpoint names or full Camel URIs.         |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `knative        | `[]s   | :::: content                               |
| .event-sources` | tring` | ::: paragraph                              |
|                 |        | List of event types that the integration   |
|                 |        | will be subscribed to. Can contain simple  |
|                 |        | event types or full Camel URIs (to use a   |
|                 |        | specific broker different from             |
|                 |        | \"default\").                              |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `knativ         | `bool` | :::: content                               |
| e.sink-binding` |        | ::: paragraph                              |
|                 |        | Allows binding the integration to a sink   |
|                 |        | via a Knative SinkBinding resource. This   |
|                 |        | can be used when the integration targets a |
|                 |        | single sink. It's enabled by default when  |
|                 |        | the integration targets a single sink      |
|                 |        | (except when the integration is owned by a |
|                 |        | Knative source).                           |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `k              | `[]s   | :::: content                               |
| native.filters` | tring` | ::: paragraph                              |
|                 |        | Sets filter attributes on the event stream |
|                 |        | (such as event type, source, subject and   |
|                 |        | so on). A list of key-value pairs that     |
|                 |        | represent filter attributes and its        |
|                 |        | values. The syntax is KEY=VALUE, e.g.,     |
|                 |        | source=\"my.source\". Filter attributes    |
|                 |        | get set on the Knative trigger that is     |
|                 |        | being created as part of this integration. |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `knative.fil    | `bool` | :::: content                               |
| ter-event-type` |        | ::: paragraph                              |
|                 |        | Enables the default filtering for the      |
|                 |        | Knative trigger using the event type If    |
|                 |        | this is true, the created Knative trigger  |
|                 |        | uses the event type as a filter on the     |
|                 |        | event stream when no other filter criteria |
|                 |        | is given. (default: true)                  |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
:::::::::

::::::::::::::::::::::: sect2
### Knative trigger {#_knative_trigger}

::: paragraph
The concept of a Knative trigger allows you to consume events from the
[Knative eventing](https://knative.dev/docs/eventing/) broker. In case
your Camel route uses the Knative component as a consumer you may need
to create a trigger in Kubernetes to connect your Camel application with
the Knative broker.
:::

::: paragraph
The Camel JBang Kubernetes plugin is able to automatically create this
trigger for you.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `knative` trait is disabled   |
| Note                              | by default, you need to enable    |
| :::                               | the Knative trait with            |
|                                   | `--trait knative.enabled=true`    |
|                                   | option.                           |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The following Camel route uses the Knative event component and
references a Knative broker by its name. The plugin inspects the code
and automatically generates the Knative trigger as part of the
Kubernetes manifest that is used to run the Camel application on
Kubernetes.
:::

:::: listingblock
::: content
``` highlight
- from:
    uri: knative:event/camel.evt.type?name=my-broker
    steps:
      - to: log:info
```
:::
::::

::: paragraph
The route consumes Knative events of type `camel.evt.type`. If you
export this route with the Camel JBang Kubernetes plugin, you will see a
Knative trigger being generated as part of the Kubernetes manifest
(kubernetes.yml).
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export knative-route.yaml
```
:::
::::

::: paragraph
The generated export project can be deployed to Kubernetes, and as part
of the deployment, the trigger is automatically created so the
application can start consuming events.
:::

::: paragraph
The generated trigger looks as follows:
:::

:::: listingblock
::: content
``` highlight
apiVersion: eventing.knative.dev/v1
kind: Trigger
metadata:
  name: my-broker-knative-route-camel-evt-type
spec:
  broker: my-broker
  filter:
    attributes:
      type: camel.evt.type
  subscriber:
    ref:
      apiVersion: v1
      kind: Service
      name: knative-route
    uri: /events/camel-evt-type
```
:::
::::

::: paragraph
The trigger uses a default filter on the event type CloudEvents
attribute and calls the Camel application via the exposed Kubernetes
service resource.
:::

::: paragraph
The Camel application is automatically configured to expose an Http
service so incoming events are handed over to the Camel route. You can
review the Knative service resource configuration that makes Camel
configure the Knative component. The configuration has been
automatically created in `src/main/resources/knative.json` in the
exported project.
:::

::: paragraph
Here is an example of the generated `knative.json` file:
:::

:::: listingblock
::: content
``` highlight
{
  "resources" : [ {
    "name" : "camel-event",
    "type" : "event",
    "endpointKind" : "source",
    "path" : "/events/camel-event",
    "objectApiVersion" : "eventing.knative.dev/v1",
    "objectKind" : "Broker",
    "objectName" : "my-broker",
    "reply" : false
  } ]
}
```
:::
::::

::: paragraph
The exported project has everything configured to run the application on
Kubernetes. Of course, you need Knative eventing installed on your
target cluster, and you need to have a Knative broker named `my-broker`
available in the target namespace.
:::

::: paragraph
Now you can just deploy the application using the Kubernetes manifest
and see the Camel route consuming events from the broker.
:::
:::::::::::::::::::::::

::::::::::::::::::::: sect2
### Knative channel subscription {#_knative_channel_subscription}

::: paragraph
Knative channels represent another form of producing and consuming
events from the Knative broker. Instead of using a trigger, you can
create a subscription for a Knative channel to consume events.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `knative` trait is disabled   |
| Note                              | by default, you need to enable    |
| :::                               | the Knative trait with            |
|                                   | `--trait knative.enabled=true`    |
|                                   | option.                           |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The Camel route that connects to a Knative channel in order to receive
events looks like this:
:::

:::: listingblock
::: content
``` highlight
- from:
    uri: knative:channel/my-channel
    steps:
      - to: log:info
```
:::
::::

::: paragraph
The Knative channel is referenced by its name. The Camel JBang
Kubernetes plugin will inspect your code to automatically create a
channel subscription as part of the Kubernetes manifest. You just need
to export the Camel route as usual.
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export knative-route.yaml
```
:::
::::

::: paragraph
The code inspection recognizes the Knative component that references the
Knative channel, and the subscription automatically becomes part of the
exported Kubernetes manifest.
:::

::: paragraph
Here is an example subscription that has been generated during the
export:
:::

:::: listingblock
::: content
``` highlight
apiVersion: messaging.knative.dev/v1
kind: Subscription
metadata:
  name: my-channel-knative-route
spec:
  channel:
    apiVersion: messaging.knative.dev/v1
    kind: Channel
    name: my-channel
  subscriber:
    ref:
      apiVersion: v1
      kind: Service
      name: knative-route
    uri: /channels/my-channel
```
:::
::::

::: paragraph
The subscription connects the Camel application with the channel, so
each event on the channel is sent to the Kubernetes service resource
that also has been created as part of the Kubernetes manifest.
:::

::: paragraph
The Camel Knative component uses a service resource configuration
internally to create the proper Http service. You can review the Knative
service resource configuration that makes Camel configure the Knative
component. The configuration has been automatically created in
`src/main/resources/knative.json` in the exported project.
:::

::: paragraph
Here is an example of the generated `knative.json` file:
:::

:::: listingblock
::: content
``` highlight
{
  "resources" : [ {
    "name" : "my-channel",
    "type" : "channel",
    "endpointKind" : "source",
    "path" : "/channels/my-channel",
    "objectApiVersion" : "messaging.knative.dev/v1",
    "objectKind" : "Channel",
    "objectName" : "my-channel",
    "reply" : false
  } ]
}
```
:::
::::

::: paragraph
Assuming that you have Knative eventing installed on your cluster and
that you have set up the Knative channel `my-channel` you can start
consuming events right away. The deployment of the exported project uses
the Kubernetes manifest to create all required resources, including the
Knative subscription.
:::
:::::::::::::::::::::

::::::::::::::::::::: sect2
### Knative sink binding {#_knative_sink_binding}

::: paragraph
When connecting to a Knative resource (Broker, Channel, Service) in
order to produce events for Knative eventing you probably want to use a
`SinkBinding` that resolves the URL to the Knative resource for you. The
sink binding is a Kubernetes resource that makes Knative eventing
automatically inject the resource URL into your Camel application on
startup. The Knative URL injection uses environment variables (`K_SINK`,
`K_CE_OVERRIDES`) on your deployment. The Knative eventing operator will
automatically resolve the Knative resource (e.g. a Knative broker URL)
and inject the value so your application does not need to know the
actual URL when deploying.
:::

::: paragraph
The Camel JBang Kubernetes plugin leverages the sink binding concept for
all routes that use the Knative component as an output.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `knative` trait is disabled   |
| Note                              | by default, you need to enable    |
| :::                               | the Knative trait with            |
|                                   | `--trait knative.enabled=true`    |
|                                   | option.                           |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The following route produces events on a Knative broker:
:::

:::: listingblock
::: content
``` highlight
- from:
    uri: timer:tick
    steps:
      - setBody:
          constant: Hello Camel !!!
      - to: knative:event/camel.evt.type?name=my-broker
```
:::
::::

::: paragraph
The route produces events of type `camel.evt.type` and pushes the events
to the broker named `my-broker`. At this point, the actual Knative
broker URL is unknown. The sink binding is going to resolve the URL and
inject its value at deployment time using the `K_SINK` environment
variable.
:::

::: paragraph
The Camel JBang Kubernetes plugin export automatically inspects such a
route and automatically creates the sink binding resource for us. The
sink binding is part of the exported Kubernetes manifest and is created
on the cluster as part of the deployment.
:::

::: paragraph
A sink binding resource created by the export command looks like
follows:
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export knative-route.yaml
```
:::
::::

:::: listingblock
::: content
``` highlight
apiVersion: sources.knative.dev/v1
kind: SinkBinding
metadata:
  finalizers:
    - sinkbindings.sources.knative.dev
  name: knative-route
spec:
  sink:
    ref:
      apiVersion: eventing.knative.dev/v1
      kind: Broker
      name: my-broker
  subject:
    apiVersion: apps/v1
    kind: Deployment
    name: knative-route
```
:::
::::

::: paragraph
In addition to creating the sink binding, the Camel JBang plugin also
takes care of configuring the Knative Camel component. The Knative
component uses a configuration file that you can find in
`src/main/resources/knative.json`. As you can see the configuration uses
the `K_SINK` injected property placeholder as a broker URL.
:::

:::: listingblock
::: content
``` highlight
{
  "resources" : [ {
    "name" : "camel-evt-type",
    "type" : "event",
    "endpointKind" : "sink",
    "url" : "{{k.sink}}",
    "objectApiVersion" : "eventing.knative.dev/v1",
    "objectKind" : "Broker",
    "objectName" : "my-broker",
    "reply" : false
  } ]
}
```
:::
::::

::: paragraph
As soon as the Kubernetes deployment for the exported project has
started the sink binding will inject the `K_SINK` environment variable
so that the Camel applicaiton is ready to send events to the Knative
broker.
:::

::: paragraph
The sink binding concept works for Knative Broker, Channel and Service
resources. You just reference the resource by its name in your Camel
route when sending data to the Knative component as an output of the
route (`to("knative:event|channel|endpoint/<resource-name>")`).
:::
:::::::::::::::::::::

:::::::::::::::: sect2
### Mount trait options {#_mount_trait_options}

::: paragraph
The mount trait is able to configure volume mounts on the Deployment
resource in order to inject data from Kubernetes resources such as
config maps or secrets.
:::

::: paragraph
There are also shortcut options like `--volume`, `--config` and
`--resource` for the mount trait. These options are described in more
detail in the next section. For now let's have a look into the pure
mount trait configuration options.
:::

::: paragraph
The mount trait provides the following configuration options:
:::

+-----------------+--------+--------------------------------------------+
| Property        | Type   | Description                                |
+=================+========+============================================+
| `mount.configs` | `[]s   | :::: content                               |
|                 | tring` | ::: paragraph                              |
|                 |        | A list of configuration pointing to        |
|                 |        | configmap/secret. The configurations are   |
|                 |        | expected to be UTF-8 resources as they are |
|                 |        | processed by runtime Camel Context and     |
|                 |        | tried to be parsed as property files. They |
|                 |        | are also made available on the classpath   |
|                 |        | to ease their usage directly from the      |
|                 |        | Route. Syntax:                             |
|                 |        | \[configmap\|secret\]:name\[/key\], where  |
|                 |        | name represents the resource name and key  |
|                 |        | optionally represents the resource key to  |
|                 |        | be filtered                                |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `m              | `[]s   | :::: content                               |
| ount.resources` | tring` | ::: paragraph                              |
|                 |        | A list of resources (text or binary        |
|                 |        | content) pointing to a configmap/secret.   |
|                 |        | The resources are expected to be any       |
|                 |        | resource type (text or binary content).    |
|                 |        | The destination path can be either a       |
|                 |        | default location or any path specified by  |
|                 |        | the user. Syntax:                          |
|                 |        | \[                                         |
|                 |        | configmap\|secret\]:name\[/key\]\[@path\], |
|                 |        | where name represents the resource name,   |
|                 |        | key optionally represents the resource key |
|                 |        | to be filtered and path represents the     |
|                 |        | destination path                           |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `mount.volumes` | `[]s   | :::: content                               |
|                 | tring` | ::: paragraph                              |
|                 |        | A list of Persistent Volume Claims to be   |
|                 |        | mounted. Syntax:                           |
|                 |        | \[pvcname:/container/path\]                |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+

::: paragraph
The syntax to specify mount trait options is as follows:
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export Sample.java --trait mount.[key]=[value]
```
:::
::::

::::: listingblock
::: title
Example
:::

::: content
``` highlight
camel kubernetes export Sample.java --trait mount.configs=configmap:my-data --trait mount.volumes=my-pvc:/container/path
```
:::
:::::

::: paragraph
This results in the following container specification in the Deployment
resource.
:::

:::: listingblock
::: content
``` highlight
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    camel.apache.org/integration: sample
  name: sample
spec:
  selector:
    matchLabels:
      camel.apache.org/integration: sample
  template:
    metadata:
      labels:
        camel.apache.org/integration: sample
    spec:
      containers:
        - image: quay.io/sample:1.0-SNAPSHOT
          name: sample
          volumeMounts:
            - mountPath: /etc/camel/conf.d/_configmaps/my-data #(1)
              name: my-data
              readOnly: true
            - mountPath: /container/path #(2)
              name: my-pvc
              readOnly: false
      volumes:
        - name: my-data #(3)
          configMap:
            name: my-data
        - name: my-pvc #(4)
          persistentVolumeClaim:
            claimName: my-pvc
```
:::
::::

::: {.colist .arabic}
1.  The config map `my-data` mounted into the container with default
    mount path for configurations

2.  The volume mounted into the container with given path

3.  The config map reference as volume spec

4.  The persistent volume claim `my-pvc`
:::
::::::::::::::::

::::::::::::::: sect2
### ConfigMaps, volumes and secrets {#_configmaps_volumes_and_secrets}

::: paragraph
In the previous section, we have seen how to mount volumes, configs, and
resources into the container.
:::

::: paragraph
The Kubernetes export command provides some shortcut options for adding
configmaps and secrets as volume mounts. The syntax is as follows:
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export Sample.java --config [key]=[value] --resource [key]=[value] --volume [key]=[value]
```
:::
::::

::: paragraph
The options expect the following syntax:
:::

+-------------------+--------------------------------------------------+
| Option            | Syntax                                           |
+===================+==================================================+
| `config`          | :::: content                                     |
|                   | ::: paragraph                                    |
|                   | Add a runtime configuration from a ConfigMap or  |
|                   | a Secret (syntax:                                |
|                   | \[configmap\|secret\]:name\[/key\], where name   |
|                   | represents the configmap or secret name and key  |
|                   | optionally represents the configmap or secret    |
|                   | key to be filtered).                             |
|                   | :::                                              |
|                   | ::::                                             |
+-------------------+--------------------------------------------------+
| `resource`        | :::: content                                     |
|                   | ::: paragraph                                    |
|                   | Add a runtime resource from a Configmap or a     |
|                   | Secret (syntax:                                  |
|                   | \[configmap\|secret\]:name\[/key\]\[@path\],     |
|                   | where name represents the configmap or secret    |
|                   | name, key optionally represents the configmap or |
|                   | secret key to be filtered and the path           |
|                   | represents the destination path).                |
|                   | :::                                              |
|                   | ::::                                             |
+-------------------+--------------------------------------------------+
| `volume`          | :::: content                                     |
|                   | ::: paragraph                                    |
|                   | Mount a volume into the integration container,   |
|                   | for instance \"\--volume                         |
|                   | pvcname:/container/path\".                       |
|                   | :::                                              |
|                   | ::::                                             |
+-------------------+--------------------------------------------------+

::::: listingblock
::: title
Example
:::

::: content
``` highlight
camel kubernetes export Sample.java --config secret:my-credentials  --resource configmap:my-data --volume my-pvc:/container/path
```
:::
:::::

:::: listingblock
::: content
``` highlight
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    camel.apache.org/integration: sample
  name: sample
spec:
  selector:
    matchLabels:
      camel.apache.org/integration: sample
  template:
    metadata:
      labels:
        camel.apache.org/integration: sample
    spec:
      containers:
        - image: quay.io/sample:1.0-SNAPSHOT
          name: sample
          volumeMounts:
            - mountPath: /etc/camel/conf.d/_secrets/my-credentials
              name: my-credentials #(1)
              readOnly: true
            - mountPath: /etc/camel/resources.d/_configmaps/my-data
              name: my-data #(2)
              readOnly: true
            - mountPath: /container/path
              name: my-pvc #(3)
              readOnly: false
      volumes:
        - name: my-credentials #(4)
          secret:
            secretName: my-credentials
        - name: my-data #(5)
          configMap:
            name: my-data
        - name: my-pvc #(6)
          persistentVolumeClaim:
            claimName: my-pvc
```
:::
::::

::: {.colist .arabic}
1.  The secret configuration volume mount

2.  The config map resource volume mount

3.  The volume mount

4.  The secret configuration volume

5.  The config map resource volume

6.  The persistent volume claim volume
:::

::: paragraph
The trait volume mounts follow some best practices in specifying the
mount paths in the container. Configurations and resources, secrets, and
configmaps do use different paths in the container. The Camel
application is automatically configured to read these paths as resource
folders, so you can use the mounted data in the Camel routes via
classpath reference, for instance.
:::
:::::::::::::::

::::::::::::::: sect2
### Ingress trait options {#_ingress_trait_options}

::: paragraph
The ingress trait enhances the Kubernetes manifest with an Ingress
resource to expose the application to the outside world. This requires
the presence in the Kubernetes manifest of a Service Resource.
:::

::: paragraph
The ingress trait provides the following configuration options:
:::

+-----------------+--------+--------------------------------------------+
| Property        | Type   | Description                                |
+=================+========+============================================+
| `i              | `bool` | :::: content                               |
| ngress.enabled` |        | ::: paragraph                              |
|                 |        | Can be used to enable or disable a trait.  |
|                 |        | All traits share this common property      |
|                 |        | (default `false`).                         |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `ingre          | `      | :::: content                               |
| ss.annotations` | map[st | ::: paragraph                              |
|                 | ring]s | The annotations added to the ingress. This |
|                 | tring` | can be used to set controller-specific     |
|                 |        | annotations, e.g., when using the [NGINX   |
|                 |        | Ingress                                    |
|                 |        | controller](https://github.com/kub         |
|                 |        | ernetes/ingress-nginx/blob/main/docs/user- |
|                 |        | guide/nginx-configuration/annotations.md). |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `ingress.host`  | `s     | :::: content                               |
|                 | tring` | ::: paragraph                              |
|                 |        | To configure the host exposed by the       |
|                 |        | ingress.                                   |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `ingress.path`  | `s     | :::: content                               |
|                 | tring` | ::: paragraph                              |
|                 |        | To configure the path exposed by the       |
|                 |        | ingress (default `/`).                     |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `in             | `s     | :::: content                               |
| gress.pathType` | tring` | ::: paragraph                              |
|                 |        | To configure the [path                     |
|                 |        | type](https://kubernetes.io/docs/concept   |
|                 |        | s/services-networking/ingress/#path-types) |
|                 |        | exposed by the ingress. One of Exact,      |
|                 |        | Prefix, ImplementationSpecific (default to |
|                 |        | Prefix).                                   |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `ingress.auto`  | `bool` | :::: content                               |
|                 |        | ::: paragraph                              |
|                 |        | To automatically add an Ingress Resource   |
|                 |        | whenever the route uses an HTTP endpoint   |
|                 |        | consumer (default `true`).                 |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `ing            | `[]s   | :::: content                               |
| ress.tls-hosts` | tring` | ::: paragraph                              |
|                 |        | To configure specific hosts exposed        |
|                 |        | through SNI TLS extension by the ingess.   |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `ingress.t      | `s     | :::: content                               |
| ls-secret-name` | tring` | ::: paragraph                              |
|                 |        | To indicate the secret containing the TLS  |
|                 |        | private key and certificate to use for     |
|                 |        | TLS.                                       |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+

::: paragraph
The syntax to specify container trait options is as follows:
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export Sample.java --trait ingress.[key]=[value]
```
:::
::::

::: paragraph
You may specify these options with the export command to customize the
Ingress Resource specification.
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export Sample.java --trait ingress.enabled=true --trait ingress.host=example.com --trait ingress.path=/sample(/|$)(.*) --trait ingress.pathType=ImplementationSpecific --trait ingress.annotations=nginx.ingress.kubernetes.io/rewrite-target=/\$2 --trait ingress.annotations=nginx.ingress.kubernetes.io/use-regex=true
```
:::
::::

::: paragraph
This results in the following container specification in the Ingress
resource.
:::

:::: listingblock
::: content
``` highlight
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations: #(1)
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    nginx.ingress.kubernetes.io/use-regex: "true"
  labels:
    app.kubernetes.io/name: sample
  name: sample
spec:
  ingressClassName: nginx
  rules:
  - host: example.com
    http:
      paths:
      - backend:
          service:
            name: route-service
            port:
              name: http #(2)
        path: /sample(/|$)(.*) #(3)
        pathType: ImplementationSpecific #(4)
```
:::
::::

::: {.colist .arabic}
1.  Custom annotations configuration for ingress behavior

2.  Service port name

3.  Custom ingress backend path

4.  Custom ingress backend path type
:::
:::::::::::::::

:::::::::::::::: sect2
### Route trait options {#_route_trait_options}

::: paragraph
The Route trait enhances the Kubernetes manifest with a Route resource
to expose the application to the outside world. This requires the
presence in the Kubernetes manifest of a Service Resource.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | You need to enable the OpenShift  |
| Note                              | profile trait with                |
| :::                               | `--trait-profile=openshift`       |
|                                   | option.                           |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The Route trait provides the following configuration options:
:::

+-----------------+--------+--------------------------------------------+
| Property        | Type   | Description                                |
+=================+========+============================================+
| `route.enabled` | `bool` | :::: content                               |
|                 |        | ::: paragraph                              |
|                 |        | Can be used to enable or disable a trait.  |
|                 |        | All traits share this common property      |
|                 |        | (default `false`).                         |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `rou            | `      | :::: content                               |
| te.annotations` | map[st | ::: paragraph                              |
|                 | ring]s | The annotations added to the route. This   |
|                 | tring` | can be used to set [openshift route        |
|                 |        | specific                                   |
|                 |        | annotations](https://docs.opens            |
|                 |        | hift.com/container-platform/4.16/networkin |
|                 |        | g/routes/route-configuration.html#nw-route |
|                 |        | -specific-annotations_route-configuration) |
|                 |        | options.                                   |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `route.host`    | `s     | :::: content                               |
|                 | tring` | ::: paragraph                              |
|                 |        | To configure the host exposed by the       |
|                 |        | route.                                     |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `route.t        | `s     | :::: content                               |
| ls-termination` | tring` | ::: paragraph                              |
|                 |        | The TLS termination type, like `edge`,     |
|                 |        | `passthrough` or `reencrypt`. Refer to the |
|                 |        | OpenShift route documentation for          |
|                 |        | additional information.                    |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `route.t        | `s     | :::: content                               |
| ls-certificate` | tring` | ::: paragraph                              |
|                 |        | The TLS certificate contents or file       |
|                 |        | (`file:absolute.path`). Refer to the       |
|                 |        | OpenShift route documentation for          |
|                 |        | additional information.                    |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `route.tls-key` | `s     | :::: content                               |
|                 | tring` | ::: paragraph                              |
|                 |        | The TLS certificate key contents or file   |
|                 |        | (`file:absolute.path`). Refer to the       |
|                 |        | OpenShift route documentation for          |
|                 |        | additional information.                    |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `route.tls-     | `s     | :::: content                               |
| ca-certificate` | tring` | ::: paragraph                              |
|                 |        | The TLS CA certificate contents or file    |
|                 |        | (`file:absolute.path`). Refer to the       |
|                 |        | OpenShift route documentation for          |
|                 |        | additional information.                    |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `route.t        | `s     | :::: content                               |
| ls-destination- | tring` | ::: paragraph                              |
| ca-certificate` |        | The destination CA contents or file        |
|                 |        | (`file:absolute.path`). The destination CA |
|                 |        | certificate provides the contents of the   |
|                 |        | CA certificate of the final destination.   |
|                 |        | When using reencrypt termination, this     |
|                 |        | file should be provided to have routers    |
|                 |        | use it for health checks on the secure     |
|                 |        | connection. If this field is not           |
|                 |        | specified, the router may provide its own  |
|                 |        | destination CA and perform hostname        |
|                 |        | validation using the short service name    |
|                 |        | (service.namespace.svc), which allows      |
|                 |        | infrastructure generated certificates to   |
|                 |        | automatically verify. Refer to the         |
|                 |        | OpenShift route documentation for          |
|                 |        | additional information.                    |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+
| `route.tls-ins  | `s     | :::: content                               |
| ecure-edge-term | tring` | ::: paragraph                              |
| ination-policy` |        | To configure how to deal with insecure     |
|                 |        | traffic, e.g. `Allow`, `Disable` or        |
|                 |        | `Redirect` traffic. Refer to the OpenShift |
|                 |        | route documentation for additional         |
|                 |        | information.                               |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+

::: paragraph
The syntax to specify container trait options is as follows:
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export Sample.java --trait route.[key]=[value]
```
:::
::::

::: paragraph
You may specify these options with the export command to customize the
Route Resource specification.
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export Sample.java --trait-profile=openshift -t route.enabled=true --trait route.host=example.com -t route.tls-termination=edge -t route.tls-certificate=file:/tmp/tls.crt -t route.tls-key=file:/tmp/tls.key
```
:::
::::

::: paragraph
This results in the following container specification in the Route
resource.
:::

:::: listingblock
::: content
``` highlight
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: route-service
spec:
  host: example.com #(1)
  port:
    targetPort: http #(2)
  tls:
    certificate: | #(3)
      ...
    key: | #(4)
      ...
    termination: edge #(5)
  to: #(6)
    kind: Service
    name: route-service
```
:::
::::

::: {.colist .arabic}
1.  Custom route host

2.  Service port name

3.  Custom route TLS certificate content

4.  Custom route TLS certificate key content

5.  Custom route TLS termination

6.  Service Resource reference
:::
::::::::::::::::

:::::::::::::::::: sect2
### OpenApi specifications {#_openapi_specifications}

::: paragraph
You can mount OpenAPI specifications to the application container with
this trait.
:::

::: paragraph
The openapi trait provides the following configuration options:
:::

+-----------------+--------+--------------------------------------------+
| Property        | Type   | Description                                |
+=================+========+============================================+
| `open           | `[]s   | :::: content                               |
| api.configmaps` | tring` | ::: paragraph                              |
|                 |        | The configmaps holding the spec of the     |
|                 |        | OpenAPI                                    |
|                 |        | :::                                        |
|                 |        | ::::                                       |
+-----------------+--------+--------------------------------------------+

::: paragraph
The syntax to specify openapi trait options is as follows:
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export Sample.java --trait openapi.[key]=[value]
```
:::
::::

::::: listingblock
::: title
Example
:::

::: content
``` highlight
camel kubernetes export Sample.java --trait openapi.configmaps=configmap:my-spec
```
:::
:::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | There is also a shortcut option   |
| Tip                               | `--                               |
| :::                               | open-api=configmap:my-configmap`. |
+-----------------------------------+-----------------------------------+
:::

:::: listingblock
::: content
``` highlight
camel kubernetes export Sample.java --open-api configmap:[name-of-configmap]
```
:::
::::

::: paragraph
This results in the following container specification in the Deployment
resource.
:::

:::: listingblock
::: content
``` highlight
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    camel.apache.org/integration: sample
  name: sample
spec:
  selector:
    matchLabels:
      camel.apache.org/integration: sample
  template:
    metadata:
      labels:
        camel.apache.org/integration: sample
    spec:
      containers:
      - image: quay.io/sample:1.0-SNAPSHOT
        name: sample
        volumeMounts:
            - mountPath: /etc/camel/resources.d/_configmaps/my-spec
              name: my-spec #(1)
              readOnly: true
      volumes:
        - name: my-spec #(2)
          configMap:
            name: my-spec
```
:::
::::

::: {.colist .arabic}
1.  OpenAPI specification volume mount

2.  Volume referencing the config map holding the OpenAPI specification
:::
::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::: sect1
## Deploy to OpenShift {#_deploy_to_openshift}

:::::::: sectionbody
::: paragraph
By default, the Kubernetes manifest is suited for plain Kubernetes
platforms. In case you are targeting OpenShift as a platform you may
want to leverage special resources such as Route, ImageStream or
BuildConfig.
:::

::: paragraph
You can set the `cluster-type=openshift` option on the export command to
tell the Kubernetes plugin to create a Kubernetes manifest specifically
suited for OpenShift.
:::

::: paragraph
Also, the default image builder is S2I for OpenShift clusters. This
means by setting the cluster type, you will automatically switch from
default Jib to S2I. Of course, you can tell the plugin to use Jib with
`--image-builder=jib` option. The image may then get pushed to an
external registry (docker.io or quay.io) so OpenShift can pull as part
of the deployment in the cluster.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | When using S2I, you may need to   |
| Tip                               | explicitly set the                |
| :::                               | `--image-group` option to the     |
|                                   | project/namespace name in the     |
|                                   | OpenShift cluster. This is        |
|                                   | because S2I will push the         |
|                                   | container image to an image       |
|                                   | repository that uses the          |
|                                   | OpenShift project/namespace name  |
|                                   | as part of the image coordinates  |
|                                   | in the registry:                  |
|                                   | `image-regis                      |
|                                   | try.openshift-image-registry.svc: |
|                                   | 5000/<project name>/<name>:<tag>` |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
When using S2I as an image build option, the Kubernetes manifest also
contains an ImageStream and BuildConfig resource. Both resources are
automatically added/removed when creating/deleting the deployment with
the Camel Kubernetes JBang plugin.
:::
::::::::
:::::::::

:::::::::::::::::::::::: sect1
## Kubernetes run {#_kubernetes_run}

::::::::::::::::::::::: sectionbody
::: paragraph
The run command combines several steps into one single command. The
command performs a project export to a temporary folder, builds the
project artifacts as well as the container images, pushes the image to
an image registry and finally performs the deployment to Kubernetes
using the generated Kubernetes manifest (`kubernetes.yml`).
:::

:::: listingblock
::: content
``` highlight
camel kubernetes run route.yaml --image-registry=kind
```
:::
::::

::: paragraph
When connecting to a local Kubernetes cluster, you may need to specify
the image registry where the application container image gets pushed to.
The run command is able to automatically configure the local registry
when using predefined names such as `kind` or `minikube`, there is a
detection mechanism to set some properties accordingly to the cluster
type, currently it can auto detect Openshift and Minikube. If you want
to disable the cluster detection, you have to set the `--disable-auto`
cli parameter.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | When running minikube, the        |
| Note                              | easiest way to push the image to  |
| :::                               | the minikube container registry,  |
|                                   | is to enable the registry addon   |
|                                   | in minikube and to run            |
|                                   | `eval $(minikube docker-env)`.    |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Use the `--image-group` or the `--image` option to customize the
container image.
:::

:::: listingblock
::: content
``` highlight
camel kubernetes run route.yaml --image-registry=kind --image-group camel-experts
```
:::
::::

::: paragraph
The command above builds and pushes the container image:
`localhost:5001/camel-experts/route:1.0-SNAPSHOT`.
:::

:::: listingblock
::: content
``` highlight
camel kubernetes run route.yaml --image quay.io/camel-experts/demo-app:1.0
```
:::
::::

::: paragraph
The `--image` option forces the container image group, name, version as
well as the image registry.
:::

:::: sect2
### Customize the Kubernetes manifest {#_customize_the_kubernetes_manifest}

::: paragraph
The `run` command provides the same options to customize the Kubernetes
manifest as the `export` command. You may want to add environment
variables, mount secrets and configmaps, adjust the exposed service and
many other things with trait options as described in the export command
section.
:::
::::

:::::::: sect2
### Auto reload with `--dev` option {#_auto_reload_with_dev_option}

::: paragraph
The `--dev` option runs the application on Kubernetes and automatically
adds a file watcher to listen for changes on the Camel route source
files. In case the sources get changed, the process will automatically
perform a rebuild and redeployment. The command constantly prints the
logs to the output, so you may see the changes directly being applied to
the Kubernetes deployment.
:::

:::: listingblock
::: content
``` highlight
camel kubernetes run route.yaml --image-registry=kind --dev
```
:::
::::

::: paragraph
You need to terminate the process to stop the dev mode. This
automatically removes the Kubernetes deployment from the cluster on
shutdown.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | On macOS hosts, the file watch    |
| Note                              | mechanism is known to be much     |
| :::                               | slower and less stable compared   |
|                                   | to using the `--dev` option on    |
|                                   | other operating systems like      |
|                                   | Linux. This is due to limited     |
|                                   | native file operations on macOS   |
|                                   | for Java processes.               |
+-----------------------------------+-----------------------------------+
:::
::::::::
:::::::::::::::::::::::
::::::::::::::::::::::::

::::::::: sect1
## Show logs {#_show_logs}

:::::::: sectionbody
::: paragraph
To inspect the log output of a running deployment call:
:::

:::: listingblock
::: content
``` highlight
camel kubernetes logs --name=route
```
:::
::::

::: paragraph
The command connects to the running integration Pod on the cluster and
streams the log output. Just terminate the process to stop printing the
logs.
:::

::: paragraph
The `--name` option should point to a previously exported project
(either via `run` or `export` command).
:::
::::::::
:::::::::

::::::::::: sect1
## Delete deployments {#_delete_deployments}

:::::::::: sectionbody
::: paragraph
Of course, you may also delete a deployment from the cluster.
:::

:::: listingblock
::: content
``` highlight
camel kubernetes delete --name=route
```
:::
::::

::: paragraph
Use the `--name` option to select a previously exported project (either
via `run` or `export` command). The delete operation will remove all
resources defined in the Kubernetes manifest.
:::

::: paragraph
If you used the `--cluster-type` option to create your project (either
via `run` or `export` command) you will need to use it as well for the
delete operation.
:::

:::: listingblock
::: content
``` highlight
camel kubernetes delete --name=route --cluster-type=openshift
```
:::
::::
::::::::::
:::::::::::

::::::::::::: sect1
## Minikube deployment tips and troubleshooting {#_minikube_deployment_tips_and_troubleshooting}

:::::::::::: sectionbody
::: paragraph
To run a local Kubernetes cluster with Minikube for development
purposes. Here are some tips from users that have been using this.
:::

::: paragraph
The following steps have been known to be working with Camel 4.8.1+:
:::

::: {.olist .arabic}
1.  `minikube start --addons registry --driver=docker`

2.  `eval $(minikube -p minikube docker-env)`

3.  `camel kubernetes run demo.camel.yaml --cluster-type=minikube --build-property=quarkus.kubernetes.image-pull-policy=Never`
:::

::: paragraph
Most important
`--build-property=quarkus.kubernetes.image-pull-policy=Never` without
that it is not working, which is not clear from the current Minikube
docs.
:::

::: paragraph
The following steps have been known to be working with Camel 4.9+:
:::

::: {.olist .arabic}
1.  `minikube start --addons registry --driver=docker`

2.  `eval $(minikube -p minikube docker-env)`

3.  `camel kubernetes run demo.camel.yaml --cluster-type=minikube --build-property=quarkus.kubernetes.image-pull-policy=Never --trait container.image-push=true --image-registry '$(kubectl -n kube-system get service registry -o jsonpath='{.spec.clusterIP}')' --image-builder=docker`
:::

::: paragraph
The following steps have been known to be working with Camel 4.10+:
:::

::: {.olist .arabic}
1.  `minikube start --addons registry --driver=docker`

2.  `eval $(minikube -p minikube docker-env)`

3.  `camel kubernetes run demo.camel.yaml --cluster-type=minikube --build-property=quarkus.kubernetes.image-pull-policy=Never --image-registry "$(kubectl -n kube-system get service registry -o jsonpath='{.spec.clusterIP}')" --image-builder=docker`
:::

::: paragraph
Note that Docker multi-platform build is used. It requires to have
followed these [Docker
requirements](https://docs.docker.com/build/building/multi-platform/#build-multi-platform-images).
:::
::::::::::::
:::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
