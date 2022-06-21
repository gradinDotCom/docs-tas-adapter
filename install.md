# Installing Application Service Adapter

This topic describes how to install the Application Service Adapter for VMware Tanzu Application Platform system:

* [Install the package repository](#install-package-repo)
* [Configure the installation settings](#configure-installation-settings)
* [Install the Application Service Adapter](#install-adapter)
* [Configure DNS for the Application Service Adapter](#configure-dns)

----

After you have completed the steps in [Installing Prerequisites](install-prerequisites.md), set the Kubernetes context to the cluster where you have installed kpack and Contour.

## <a id="install-package-repo"></a>Install the package repository

To install Application Service Adapter:

1. Create a namespace called `tas-adapter-install` for deploying Application Service Adapter to your cluster.

    ```bash
    kubectl create ns tas-adapter-install
    ```

1. Create an image pull secret to store your Tanzu Network credentials. These are required so that the cluster can pull images from the Tanzu Network registry.

    ```bash
    tanzu secret registry add tanzunet-registry \
      --username "TANZU-NET-USER" --password "TANZU-NET-PASSWORD" \
      --server registry.tanzu.vmware.com \
      --export-to-all-namespaces --yes \
      --namespace tas-adapter-install
    ```

   Where `TANZU-NET-USER` and `TANZU-NET-PASSWORD` are your credentials for Tanzu Network.

1. Add the Application Service Adapter package repository to the cluster.

    ```bash
    tanzu package repository add tas-adapter-repository \
      --url registry.tanzu.vmware.com/app-service-adapter/tas-adapter-package-repo:0.7.0 \
      --namespace tas-adapter-install
    ```
1. Verify that the package repository contains the Application Service Adapter package.

    ```bash
    tanzu package available list \
      --namespace tas-adapter-install
    ```

   The output includes the Application Service Adapter package:

    ```bash
    NAME                                          DISPLAY-NAME                 SHORT-DESCRIPTION                                                   LATEST-VERSION
    ...
    application-service-adapter.tanzu.vmware.com  Application Service Adapter  Application Service Adapter for VMware Tanzu® Application Platform  0.7.0
    ...
    ```

1. List the installation settings for the `application-service-adapter` package.

    ```bash
    tanzu package available get application-service-adapter.tanzu.vmware.com/0.7.0 --values-schema --namespace tas-adapter-install
    ```

   It should output a list of settings similar to:

    ```
    | Retrieving package details for application-service-adapter.tanzu.vmware.com/0.7.0...
      KEY                         DEFAULT  TYPE     DESCRIPTION
      api_auth_proxy.ca_cert               string   TLS CA certificate of your cluster's auth proxy
      api_auth_proxy.host                  string   FQDN of your cluster's auth proxy
      api_ingress.fqdn                     string   FQDN used to access the CF API
      api_ingress.tls.crt                  string   TLS certificate for the CF API (PEM format)
      api_ingress.tls.key                  string   TLS private key for the CF API (PEM format)
      app_ingress.default_domain           string   Default application domain
      app_ingress.tls.crt                  string   TLS certificate for the default application domain (PEM format)
      app_ingress.tls.key                  string   TLS private key for the default application domain (PEM format)
      kpack_cluster_builder_name  default  string   Name of the kpack cluster builder to use for staging
      kpack_image_tag_prefix               string   Container registry repository where staged, runnable app images (Droplets) will be stored
      package_registry_base_path           string   Container registry repository where uploaded app source code (Packages) will be stored
      ...
    ```

## <a id="configure-installation-settings"></a>Configure the installation settings

To configure the installation settings:

1. If you do not already have a certificate and private keypair for HTTPS ingress to the Application Service Adapter API, generate a self-signed certificate.

   If you are using `openssl`, or `libressl v3.1.0` or later:

    ```bash
    openssl req -x509 -newkey rsa:4096 \
      -keyout tls.key -out tls.crt \
      -nodes -subj '/CN=API-FQDN' \
      -addext "subjectAltName = DNS:API-FQDN" \
      -days 365
    ```
    Where `API-FQDN` is the fully qualified domain name (FQDN) to use to access the API.


    If you are using version `libressl` v3.1.0 or earlier:

    ```bash
    openssl req -x509 -newkey rsa:4096 \
      -keyout tls.key -out tls.crt \
      -nodes -subj '/CN=API-FQDN' \
      -extensions SAN -config <(cat /etc/ssl/openssl.cnf <(printf "[ SAN ]\nsubjectAltName='DNS:API-FQDN'")) \
     -days 365
    ```

    > **Note:** `libressl` v3.1.0 is the default version in macOS.

1. If you do not already have a wildcard certificate and private keypair for HTTPS application ingress, generate a self-signed certificate.

   If you are using `openssl`, or `libressl v3.1.0` or later:

    ```bash
    openssl req -x509 -newkey rsa:4096 \
      -keyout tls.key -out tls.crt \
      -nodes -subj '/CN=*.APP-DOMAIN' \
      -addext "subjectAltName = DNS:*.APP-DOMAIN" \
      -days 365
    ```
    Where `APP-DOMAIN` is the FQDN of the shared domain to use for application routes. By default, each application is mapped to a route on a subdomain of this shared domain.

   > **Note:** The TLS certificate for application ingress must be a wildcard certificate.

    If you are using version `libressl` v3.1.0 or earlier:

    ```bash
    openssl req -x509 -newkey rsa:4096 \
      -keyout tls.key -out tls.crt \
      -nodes -subj '/CN=*.APP-DOMAIN' \
      -extensions SAN -config <(cat /etc/ssl/openssl.cnf <(printf "[ SAN ]\nsubjectAltName='DNS:*.APP-DOMAIN'")) \
     -days 365
    ```

    > **Note:** `libressl` v3.1.0 is the default version in macOS.


1. Create a `tas-adapter-values.yml` file with the desired installation settings, following the schema specified for the package.

   The following values are required:

    ```yaml
    ---
    ceip_policy_disclosed: FALSE-OR-TRUE-VALUE # Installation fails if this is not set to true. Not a string.
    api_ingress:
      fqdn: "API-FQDN"
      tls:
        crt: |
          API-TLS-CRT
        key: |
          API-TLS-KEY
    app_ingress:
      default_domain: "DEFAULT-APP-DOMAIN"
      tls:
        crt: |
          APP-TLS-CRT
        key: |
          APP-TLS-KEY
    kpack_image_tag_prefix: "KPACK-TAG-PREFIX"
    package_registry_base_path: "PACKAGE-REGISTRY-BASE"
    app_registry_credentials:
      username: "APP-REGISTRY-USERNAME"
      password: "APP-REGISTRY-PASSWORD"
      hostname: "APP-REGISTRY-HOSTNAME"
    ```

   Where:

   - `API-FQDN` is the FQDN that you want to use for the Application Service Adapter API.
   - `API-TLS-CRT` is the PEM-encoded public certificate for the Application Service Adapter API.
   - `API-TLS-KEY` is the PEM-encoded private key for the Application Service Adapter API.
   - `DEFAULT-APP-DOMAIN` is the domain that you want to use.
   - `APP-TLS-CRT` is the PEM-encoded public certificate for applications deployed using the Application Service Adapter.
   - `APP-TLS-KEY` is the PEM-encoded private key for applications deployed using the Application Service Adapter.
   - `KPACK-TAG-PREFIX` is the container image registry "directory"/"project" where droplets (runnable application images) are uploaded.
   - `PACKAGE-REGISTRY-BASE` is the registry "directory"/"project" where packages (application source code) are uploaded.
   - `APP-REGISTRY-USERNAME` is the user name used to access the registry, or the reserved keyword indicating service account JSON. For example, `_json_key`.
   - `APP-REGISTRY-PASSWORD` is the password used to access the registry, or service account JSON. For example, `{\"type\": \"service_account\", \"project_id\": \"my-gcr-project-id\"...}\` for the GCP service account.
   - `APP-REGISTRY-HOSTNAME` is the hostname of the registry to be used for app packages and droplets. For example, `gcr.io`.

   See optional values in the following example. For more information, see the Tanzu CLI output.

    ```yaml
    ---
    api_auth_proxy:
      ca_cert: |
        API-AUTH-PROXY-TLS-CRT
      host: "API-AUTH-PROXY-FQDN"
    kpack_cluster_builder_name: "KPACK-CLUSTER-BUILDER-NAME"
    scaling:
      cf_k8s_api:
        limits:
          cpu: "API-CPU-LIMIT"
          memory: "API-MEMORY-LIMIT"
        requests:
          cpu: "API-CPU-REQUEST"
          memory: "API-MEMORY-REQUEST"
        replicas: API-REPLICA-COUNT
      cf_k8s_controllers:
        ... #! scaling keys are the same as above
      eirini_controller:
        ... #! scaling keys are the same as above
      kube_rbac_proxy:
        ... #! scaling keys are the same as above, minus the "replicas" key
    telemetry:
      enabled: TELEMETRY-ENABLED
      heartbeat_interval: TELEMETRY-HEARTBEAT-INTERVAL
    ```

   Where:

   - `API-AUTH-PROXY-TLS-CRT` is the CA certificate from the authentication proxy running along side your Kubernetes cluster.
   - `API-AUTH-PROXY-FQDN` is the FQDN for the authentication proxy running along side your Kubernetes cluster.
   - `KPACK-CLUSTER-BUILDER-NAME` is the name of the kpack cluster builder to use for staging. Tanzu Build Service provides two cluster builders named `base` and `default`. To create your own builder, see [Managing Builders](https://docs.vmware.com/en/Tanzu-Build-Service/1.3/vmware-tanzu-build-service-v13/GUID-managing-builders.html) in the Tanzu Build Service documentation, and update this setting with the corresponding builder name.
   - `API-CPU-LIMIT` is the desired CPU resource limit for the pods in the specified deployment.
   - `API-MEMORY-LIMIT` is the desired memory resource limit for the pods in the specified deployment.
   - `API-CPU-REQUEST` is the desired CPU resource request for the pods in the specified deployment.
   - `API-MEMORY-REQUEST` is the desired memory resource request for the pods in the specified deployment.
   - `API-REPLICA-COUNT` is the desired number of replicas for the specified deployment.
   - `TELEMETRY-ENABLED` determines whether to send telemetry data to VMware. Default is true.
   - `TELEMETRY-HEARTBEAT-INTERVAL` is how often telemetry data is sent to VMware. Default is every 24 hours.

   The `requests` and `limits` fields map directly to the resource requests and limits fields on the Kubernetes containers for these system components.
   For more information, see [Resource requests and limits of Pod and container](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-requests-and-limits-of-pod-and-container) in the Kubernetes documentation.


### <a id="opt-out-telemetry"></a>Opting out of telemetry reporting

To disable telemetry reporting in your Application Service Adapter installation:

1. Set the value of the `telemetry.enabled` property in the `tas-adapter-values.yml` file to `false`.

After you install Application Service Adapter via the steps below, it will not send telemetry data to VMware.


## <a id="install-adapter"></a>Install the Application Service Adapter

To install the Application Service Adapter:

1. Install the Application Service Adapter to the cluster.

    ```bash
    tanzu package install tas-adapter \
      --package-name application-service-adapter.tanzu.vmware.com \
      --version 0.7.0 \
      --values-file tas-adapter-values.yml \
      --namespace tas-adapter-install
    ```

1. Verify that the package install was successful.

    ```bash
    tanzu package installed get tas-adapter \
      --namespace tas-adapter-install
    ```

   The output looks like the following:

    ```bash
    | Retrieving installation details for tas-adapter...
    NAME:                    tas-adapter
    PACKAGE-NAME:            application-service-adapter.tanzu.vmware.com
    PACKAGE-VERSION:         0.7.0
    STATUS:                  Reconcile succeeded
    CONDITIONS:              [{ReconcileSucceeded True  }]
    USEFUL-ERROR-MESSAGE:
    ```

## <a id="configure-dns"></a>Configure DNS for the Application Service Adapter

To configure DNS for the Application Service Adapter:

1. Determine the external IP address to be used for ingress to your cluster. This step varies depending on the IaaS used to provision your cluster.

   For clusters that support LoadBalancer services, you can obtain the external IP address of the LoadBalancer Service that is associated with Contour's Envoy proxy. The Namespace for this service is typically either `tanzu-system-ingress` or `projectcontour` depending on how Contour was installed.

    ```bash
    kubectl -n tanzu-system-ingress get service envoy -ojsonpath='{.status.loadBalancer.ingress[*].ip}'
    ```

   > **Note:** If you are using a cluster deployed on AWS, your LoadBalancer has a DNS name instead of an IP address.

1. Create an A record in your DNS zone that resolves the configured API FQDN to the external IP address from step 1. This step varies depending on your DNS provider.

   > **Note:** If you are using a cluster deployed on AWS, create a CNAME record that resolves to the DNS name of the load balancer instead of an A record.

1. Create a wildcard A record in your DNS zone that resolves all sub-domains of the configured application domain to the external IP address from step 1. This step varies depending on your DNS provider.

1. Verify that the Contour HTTPProxy for the API endpoint is valid.

    ```bash
    kubectl -n korifi-api-system get httpproxy korifi-api-proxy
    ```

   The output looks like the following:
    ```bash
    NAME               FQDN       TLS SECRET                STATUS   STATUS DESCRIPTION
    korifi-api-proxy   API-FQDN   korifi-api-ingress-cert   valid    Valid HTTPProxy
    ```

   Target the API endpoint by running `cf api API-FQDN` and start deploying applications. To test the adapter, continue to [Getting Started](getting-started.md).
