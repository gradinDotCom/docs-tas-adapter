# Installing Prerequisites

This topic describes the first part of the installation process for the Application Service Adapter for VMware Tanzu Application Platform.

----

Usage of the Application Service Adapter requires v8.1.0 or later of the Cloud Foundry command-line interface (cf CLI).
For more information, see the [cf CLI repository](https://github.com/cloudfoundry/cli) on GitHub.

To install the Application Service Adapter, you need:

* Admin access to a Kubernetes cluster. See [Kubernetes cluster requirements](https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-install-general.html#kubernetes-cluster-requirements-2) in _Installing Part I: Prerequisites, EULA, and CLI_.

* A container image registry. See [Tanzu Network and container image registry requirements](https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-install-general.html#tanzu-network-and-container-image-registry-requirements-1) in _Installing Part I: Prerequisites, EULA, and CLI_.

The following tools must be installed in the workstation environment in which you intend to perform the installation:

* The Kubernetes CLI (kubectl) v1.20, v1.21, or v1.22.

* Tanzu CLI and its plug-ins. See [Install or update the Tanzu CLI and plugins](https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-install-general.html#cli-and-plugin) in _Installing Part I: Prerequisites, EULA, and CLI_.
   > **Note:** After you install the Tanzu CLI, run `tanzu plugin list` to see which plug-ins are installed. For the adapter to run, you need `package` and `secret` plug-ins. To install these plug-ins, run:
    ```bash
    tanzu plugin install --local cli package
    tanzu plugin install --local cli secret
    ```

The following dependencies must be installed to the target Kubernetes cluster before installing Application Service Adapter. If you have completed an installation of Tanzu Application Platform in your target Kubernetes cluster, then these dependencies are already present.

* Cluster Essentials. See [Install Cluster Essentials for VMware Tanzu](https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-install-general.html#tanzu-cluster-essentials) in _Installing Part I: Prerequisites, EULA, and CLI_.
   > **Note:** If you are operating a Tanzu Kubernetes Grid or Tanzu Community Edition cluster, the Cluster Essentials are already installed.

* cert-manager for managing internal certificates.
   * If you are installing on a Tanzu Community Edition workload cluster, see the [cert-manager documentation](https://tanzucommunityedition.io/docs/latest/package-readme-cert-manager-1.6.1/).
   * If you have installed the Tanzu Application Platform, see [Install cert-manager and FluxCD Source Controller](https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-install-components.html#install-prereqs) in _Installing Individual Packages_.
      > **Note:** This package is also installed as part of either the `full` or `dev` profiles.

* Tanzu Build Service for building images. See [Install Tanzu Build Service](https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-install-components.html#install-tbs) in _Installing Individual Packages_.
   > This package is also installed as part of either the `full` or `dev` profiles.

* Contour for ingress control.
   * If you are installing on a Tanzu Community Edition workload cluster, see [Contour Package](https://tanzucommunityedition.io/docs/latest/package-readme-contour-1.19.1/) in the Tanzu Community Edition documentation.
   * If you have installed Tanzu Application Platform, this package is installed as part of either the `full` or `light` profiles.
   > **Note:** You must configure Contour's ingress to provision a LoadBalancer. See [Configure LoadBalancer for Contour ingress](https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-install.html#configure-loadbalancer-for-contour-ingress-5) in _Installing Part II: Profiles_.

After you have installed these prerequisites, proceed to [Installing Application Service Adapter](install.md).
