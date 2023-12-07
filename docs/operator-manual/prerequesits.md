# Prerequesits (SDK ecosystem)

Superb Data Kraken utilizes 3rd-Party-Tools for various reasons:

* Low Implementation and Integration Effort: By incorporating 3rd-Party-Tools into the SDK, the development team can reduce the overall implementation and integration effort required. These tools are developed by experts in their respective domains and come with well-documented APIs and pre-built functionalities. Leveraging these tools enables the team to streamline the development process, saving time and resources. 
* Best Possible User Experience: The integration of 3rd-Party-Tools in the SDK contributes to the delivery of an enhanced user experience. These tools are designed with a strong emphasis on user-centric principles, providing intuitive interfaces and optimized workflows. By leveraging these tools, developers can incorporate established user experience guidelines and best practices into their applications. This ensures that the end-users of the SDK have a seamless and satisfying experience, resulting in increased user satisfaction and adoption.
* Feature-Rich Implementation: 3rd-Party-Tools offer a wealth of features and functionalities that can be integrated into the SDK, enhancing its capabilities. By utilizing these tools, developers can leverage pre-built components, libraries, and APIs, reducing the need to develop functionalities from scratch. This not only saves development time but also enables the SDK to offer a wider range of features and capabilities to its users. Additionally, 3rd-Party-Tools often provide regular updates and maintenance, ensuring continuous improvement and the addition of new features, further enriching the overall implementation.

## Kubernetes

Superb Data Kraken uses Kubernetes for several reasons:

* **Scalability and Resource Management** Kubernetes allows for efficient scaling of applications and services. It helps manage resources effectively by automatically scaling up or down based on demand, ensuring optimal utilization of resources.
* **High Availability and Fault Tolerance** Kubernetes provides features like automatic container restarts, load balancing, and self-healing capabilities. These features help ensure that applications and services are highly available and can recover from failures.
* **Easy Deployment and Management** Kubernetes simplifies the deployment and management of applications by providing declarative configuration and automation. It allows for easy updates, rollbacks, and monitoring of applications, making it easier to manage complex systems.
* **Portability and Interoperability** Kubernetes is widely adopted and supported by various cloud providers and vendors. This allows for easy migration of applications across different environments and avoids vendor lock-in.
* **Ecosystem and Community Support** Kubernetes has a large and active community that contributes to its development and provides support. It has a rich ecosystem of tools and services that integrate well with Kubernetes, making it a popular choice for managing containerized applications.

We use the following namespace/service-structure - to keep your adjustments to a minimum you might want to apply this structure:

* `frontend`
    * [superb-data-kraken-frontend](https://github.com/efs-opensource/superb-data-kraken-frontend)
* `backend`
    * [superb-data-kraken-accessmanager](https://github.com/efs-opensource/superb-data-kraken-accessmanager)
    * [superb-data-kraken-metadata](https://github.com/efs-opensource/superb-data-kraken-metadata)
    * [superb-data-kraken-organizationmanager](https://github.com/efs-opensource/superb-data-kraken-organizationmanager)
    * [superb-data-kraken-search](https://github.com/efs-opensource/superb-data-kraken-search)
* `operations`
    * [superb-data-kraken-storagemanager](https://github.com/efs-opensource/superb-data-kraken-storagemanager)
* `argo-mgmt`
    * [superb-data-kraken-worker](https://github.com/efs-opensource/superb-data-kraken-worker)
    * [superb-data-kraken-ingest](https://github.com/efs-opensource/superb-data-kraken-ingest)

Global Role `namespace-reader`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: namespace-reader
rules:
- apiGroups:
  - ""
  - extensions
  - apps
  resources:
  - configmaps
  - pods
  - services
  - endpoints
  - secrets
  verbs:
  - get
  - list
  - watch
```

## PostgreSQL-Database

You will need a PostgreSQL-Database-Server for various databases (e.g. [User-Management](#user-management) or [Organizationmanager](#organizationmanager)).

## Kafka

SDK is in some parts event-driven. For that reason you will need a Kafka-Broker with various Topics (see [Ingest](#ingest) or [Organizationmanager](#organizationmanager)).

## Azure Subscription

As Superb Data Kraken currently supports Azure Blob Storage, you will need an Azure Subscription, that meets the requirements. These requirements consist of:

* a Service Principal with storage-access ("storage-principal"), i.e. the following permissions scoped on your Resource Group:
    * `Microsoft.Storage/storageAccounts/listkeys/action` List Storage Account Keys
    * `Microsoft.Storage/storageAccounts/delete` Delete Storage Account
    * `Microsoft.Storage/storageAccounts/read` List/Get Storage Account(s)
    * `Microsoft.Storage/storageAccounts/write` Create/Update Storage Account
    * `Microsoft.Storage/storageAccounts/managementPolicies/write` Put storage account management policies
    * `Microsoft.Storage/storageAccounts/blobServices/write` Put blob service properties

!!! note

    * You might want to use Kubernetes, PostgreSQL-Database and Kafka as managed services in Azure :wink:
    * In the near future, SDK will also support other storage technologies (S3) and thus take a final step towards cloud-agnostics. Feel free to contribute :wink:

## User-Management

We use [Keycloak](https://www.keycloak.org/) as OpenID-Provider. On installing, please consider the [operator-installation](https://www.keycloak.org/operator/installation).

**A realm-configuration as expected by our eco-system, will be provided soon.**

## Metadata-Management

We use [OpenSearch](https://opensearch.org/) for metadata management and to store analysis results. On installing, please consider the [installation-guide](https://opensearch.org/docs/latest/install-and-configure/install-opensearch/index/).

For initial setup we use this configuration ([superb-data-kraken-metadata](https://github.com/efs-opensource/superb-data-kraken-metadata) takes care of these resources in ongoing usage):

* `roles` - definition of a set of permissions that determine what actions a user or group of users can perform within the system:
  ```yaml
  _meta:
    type: "roles"
    config_version: 2

  # Restrict users so they can only view visualization and dashboard on kibana
  kibana_read_only:
    reserved: true

  # The security REST API access role is used to assign specific users access to change the security settings through the REST API.
  security_rest_api_access:
    reserved: true

  # Allows users to view alerts
  alerting_view_alerts:
    reserved: true
    index_permissions:
      - index_patterns:
          - ".opendistro-alerting-alert*"
        allowed_actions:
          - read

  # Allows users to view and acknowledge alerts
  alerting_crud_alerts:
    reserved: true
    index_permissions:
      - index_patterns:
          - ".opendistro-alerting-alert*"
        allowed_actions:
          - crud

  # Allows users to use all alerting functionality
  alerting_full_access:
    reserved: true
    index_permissions:
      - index_patterns:
          - ".opendistro-alerting-config"
          - ".opendistro-alerting-alert*"
        allowed_actions:
          - crud

  reports_read_access:
    cluster_permissions:
      - cluster:admin/opendistro/reports/definition/get
      - cluster:admin/opendistro/reports/definition/list
      - cluster:admin/opendistro/reports/instance/get
      - cluster:admin/opendistro/reports/instance/list
      - cluster:admin/opendistro/reports/menu/download
  ```

* `rolesmapping` - map backend roles to OpenSearch roles:
  ```yaml
  # In this file users, backendroles and hosts can be mapped to Opensearch Security roles.
  # Permissions for Opensearch roles are configured in roles.yml

  _meta:
    type: "rolesmapping"
    config_version: 2

  # Define your roles mapping here

  ## Demo roles mapping

  all_access:
    reserved: false
    backend_roles:
      - "SDK_ADMIN"
    description: "Maps SDK_ADMIN to all_access"

  own_index:
    reserved: false
    users:
      - "*"
    description: "Allow full access to an index named like the username"

  logstash:
    reserved: false
    backend_roles:
      - "logstash"

  kibana_user:
    reserved: false
    backend_roles:
      - "SDK_USER"
    description: "Maps kibanauser to SDK_USER"

  reports_read_access:
    reserved: false
    backend_roles:
      - "SDK_USER"
    description: "Maps reports_read_access to SDK_USER"

  readall:
    reserved: false
    backend_roles:
      - "readall"

  manage_snapshots:
    reserved: false
    backend_roles:
      - "snapshotrestore"

  kibana_server:
    reserved: true
    users:
      - "kibanaserver"
  ```

* `tenant` - logical partitions or isolated spaces within the system that allow for the segregation and organization of data, resources, and configurations:
  ```yaml
  ---
  _meta:
    type: "tenants"
    config_version: 2

  # Define your tenants here

  admin_tenant:
    reserved: false
    description: "Tenant for admin users"
  ```

## Workflow Engine

We use [Argo Workflows](https://argoproj.github.io/argo-workflows/) as a workflow engine. On installing, please consider the [installation-guide](https://argoproj.github.io/argo-workflows/installation/).

To enable Argo Workflows to react to events (e.g. Kafka), we use [Argo Events](https://argoproj.github.io/argo-events/). On installing, please consider the [installation-guide](https://argoproj.github.io/argo-events/installation/).

## Analysis

As already mentioned in [Metadata-Management](#metadata-management) we use OpenSearch also as a result-store. We use [OpenSearch Dashboards](https://opensearch.org/docs/latest/dashboards/index/) to visualize these results. On installing, please consider the [installation-guide](https://opensearch.org/docs/latest/install-and-configure/install-dashboards/index/).

A great addition for easily interact with your data is [Jupyterhub](https://jupyter.org/hub). On installing, please consider the [installation-guide](https://z2jh.jupyter.org/en/latest/jupyterhub/installation.html).

## Monitoring

Our services publish metrics to [Prometheus](https://prometheus.io/). For a detailed installation-guide please refer to the [installation-guide](https://prometheus.io/docs/prometheus/latest/installation/).
