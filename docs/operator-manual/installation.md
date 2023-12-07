# Installing Superb Data Kraken

The Superb Data Kraken consists of multiple services which combine to the most amazing Data Kraken :smiley:

!!! note

    * These instructions assume, that the [prerequesits](prerequesits.md) are already met.
    * You might want to manage your configuration globally, as many properties are required by various services.

## Organizationmanager

To get an up and running instance of the organizationmanager the following steps are required:

* provide a PostgreSQL-database, configure via the following properties:
    * in [config-map.yml](https://github.com/EFS-OpenSource/superb-data-kraken-organizationmanager/blob/develop/kubernetes/config-map.yml) adjust `$(DATABASE_SERVER)` and `$(ORGAMANAGER_DATABASE)` accordingly
    * in [provided-secrets.yml](https://github.com/EFS-OpenSource/superb-data-kraken-organizationmanager/blob/develop/kubernetes/provided-secrets.yml) adjust `DATABASE_PASSWORD` and `DATABASE_USER` (in the form of `<USER>@<DATABASE_SERVER>`) accordingly
* provide a Kafka-instance with the following topic: `space-deleted` (or any other topic configured as property `organizationmanager.kafka.topic.space-deleted`), configure via the following properties:
    * in [provided-secrets.yml](https://github.com/EFS-OpenSource/superb-data-kraken-organizationmanager/blob/develop/kubernetes/provided-secrets.yml) adjust `KAFKA_SASL_JAAS_CONFIG` (in the form of `org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://<KAFKA_BROKER>/;SharedAccessKeyName=<KEY_NAME>;SharedAccessKey=<KEY>";`) accordingly
* provide the following libraries locally:
    * [superb-data-kraken-logging](https://github.com/EFS-OpenSource/superb-data-kraken-logging)
    * [superb-data-kraken-common](https://github.com/EFS-OpenSource/superb-data-kraken-common)
    * [superb-data-kraken-storage-schemas](https://github.com/EFS-OpenSource/superb-data-kraken-storage-schemas)
* build a Docker-image to the container-registry of your liking. We provided you with 2 options:
    * with additional logging to [Azure Application Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview) ([Dockerfile](https://github.com/EFS-OpenSource/superb-data-kraken-organizationmanager/blob/develop/Dockerfile))
    * and without Azure Application Insights ([Dockerfile-no-appinsights](https://github.com/EFS-OpenSource/superb-data-kraken-organizationmanager/blob/develop/Dockerfile-no-appinsights))

If you choose to use the Docker-image with additional logging to Azure Application Insights, you need to provide extra properties in [config-map.yml](https://github.com/EFS-OpenSource/superb-data-kraken-organizationmanager/blob/develop/kubernetes/config-map.yml): 

* `APP_INSIGHTS_CONNECTION_STRING` the Connection-String of your Azure Application Insights instance
* `APP_INSIGHTS_INSTRUMENTATION_KEY` the Instrumentation-Key of your Azure Application Insights instance

For additional configuration, please consider these properties within the [kubernetes-folder](https://github.com/EFS-OpenSource/superb-data-kraken-organizationmanager/tree/develop/kubernetes):

* `postfix` a postfix you might want to add to your servides (:exclamation: should be consistent across your installation as cluster-interal domains are predifined with this postfix :exclamation:)
* `REALM` the specific realm set up with the openid connect (oidc) provider
* `CLIENT_ID` the client-id within defined realm (used for simplifying swagger-access)
* `CLIENT_ID_CONFIDENTIAL` the id of the confidential client used for Service Account-access. This Service Account should have the following permissions:
    * get/update users in [User-Management](prerequesits.md#user-management)
    * get/create/update/delete roles in [User-Management](prerequesits.md#user-management)
* `CLIENT_SECRET_CONFIDENTIAL` the secret of the confidential client used for Service Account-access
* `LOG_LEVEL` the logging-level for organizationmanager
* `CONTAINER_REGISTRY` the container-registry that stores the Docker-image
* `tagVersion` the tag-version of the Docker-image
* `DOMAIN` the domain organizationmanager should be available at
* `KAFKA_BOOTSTRAP_SERVER` the Kafka-Bootstrap-Server

## Storagemanager

To get an up and running instance of the storage-manager the following steps are required:

* provide the following libraries locally:
    * [superb-data-kraken-logging](https://github.com/EFS-OpenSource/superb-data-kraken-logging)
    * [superb-data-kraken-common](https://github.com/EFS-OpenSource/superb-data-kraken-common)
* build a Docker-image to the container-registry of your liking. We provided you with 2 options:
    * with additional logging to [Azure Application Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview) ([Dockerfile](https://github.com/EFS-OpenSource/superb-data-kraken-storagemanager/blob/develop/Dockerfile))
    * and without Azure Application Insights ([Dockerfile-no-appinsights](https://github.com/EFS-OpenSource/superb-data-kraken-storagemanager/blob/develop/Dockerfile-no-appinsights))

If you choose to use the Docker-image with additional logging to Azure Application Insights, you need to provide extra properties in [config-map.yml](https://github.com/EFS-OpenSource/superb-data-kraken-storagemanager/blob/develop/kubernetes/config-map.yml): 

* `APP_INSIGHTS_CONNECTION_STRING` the Connection-String of your Azure Application Insights instance
* `APP_INSIGHTS_INSTRUMENTATION_KEY` the Instrumentation-Key of your Azure Application Insights instance

For additional configuration, please consider these properties within the [kubernetes-folder](https://github.com/EFS-OpenSource/superb-data-kraken-storagemanager/tree/develop/kubernetes):

* `postfix` a postfix you might want to add to your servides (:exclamation: should be consistent across your installation as cluster-interal domains are predifined with this postfix :exclamation:)
* `REALM` the specific realm set up with the openid connect (oidc) provider
* `CLIENT_ID` the client-id within defined realm (used for simplifying swagger-access)
* `LOG_LEVEL` the logging-level for storage-manager
* `CONTAINER_REGISTRY` the container-registry that stores the Docker-image
* `tagVersion` the tag-version of the Docker-image
* `DOMAIN` the domain storage-manager should be available at
* `RESOURCE_GROUP` the resource-group in Azure the storage-principal is permitted to (see [Azure Subscription](prerequesits.md#azure-subscription))
* `AZURE_STORAGE_CLIENT_ID` application (client) ID of the storage-principal managed application (see [Azure Subscription](prerequesits.md#azure-subscription))
* `AZURE_STORAGE_CLIENT_SECRET` application (client) secret of the storage-principal managed application (see [Azure Subscription](prerequesits.md#azure-subscription))
* `AZURE_TENANT_ID` the tenant-id of the Azure Subscription

## Accessmanager

To get an up and running instance of the accessmanager the following steps are required:

* provide a Kafka-instance with the following topic: `accessmanager-commit` (or any other topic configured as property `accessmanager.topic.upload-complete`), configure via the following properties:
    * in [provided-secrets.yml](https://github.com/EFS-OpenSource/superb-data-kraken-accessmanager/blob/develop/kubernetes/provided-secrets.yml) adjust `KAFKA_SASL_JAAS_CONFIG` (in the form of `org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://<KAFKA_BROKER>/;SharedAccessKeyName=<KEY_NAME>;SharedAccessKey=<KEY>";`) accordingly
* provide the following libraries locally:
    * [superb-data-kraken-logging](https://github.com/EFS-OpenSource/superb-data-kraken-logging)
* build a Docker-image to the container-registry of your liking. We provided you with 2 options:
    * with additional logging to [Azure Application Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview) ([Dockerfile](https://github.com/EFS-OpenSource/superb-data-kraken-accessmanager/blob/develop/Dockerfile))
    * and without Azure Application Insights ([Dockerfile-no-appinsights](https://github.com/EFS-OpenSource/superb-data-kraken-accessmanager/blob/develop/Dockerfile-no-appinsights))

If you choose to use the Docker-image with additional logging to Azure Application Insights, you need to provide extra properties in [config-map.yml](https://github.com/EFS-OpenSource/superb-data-kraken-accessmanager/blob/develop/kubernetes/config-map.yml): 

* `APP_INSIGHTS_CONNECTION_STRING` the Connection-String of your Azure Application Insights instance
* `APP_INSIGHTS_INSTRUMENTATION_KEY` the Instrumentation-Key of your Azure Application Insights instance

For additional configuration, please consider these properties within the [kubernetes-folder](https://github.com/EFS-OpenSource/superb-data-kraken-accessmanager/tree/develop/kubernetes):

* `postfix` a postfix you might want to add to your servides (:exclamation: should be consistent across your installation as cluster-interal domains are predifined with this postfix :exclamation:)
* `REALM` the specific realm set up with the openid connect (oidc) provider
* `CLIENT_ID` the client-id within defined realm (used for simplifying swagger-access)
* `LOG_LEVEL` the logging-level for accessmanager
* `CONTAINER_REGISTRY` the container-registry that stores the Docker-image
* `tagVersion` the tag-version of the Docker-image
* `DOMAIN` the domain accessmanager should be available at
* `RESOURCE_GROUP` the resource-group in Azure the storage-principal is permitted to (see [Azure Subscription](prerequesits.md#azure-subscription))
* `AZURE_STORAGE_CLIENT_ID` application (client) ID of the storage-principal managed application (see [Azure Subscription](prerequesits.md#azure-subscription))
* `AZURE_STORAGE_CLIENT_SECRET` application (client) secret of the storage-principal managed application (see [Azure Subscription](prerequesits.md#azure-subscription))
* `AZURE_TENANT_ID` the tenant-id of the Azure Subscription
* `KAFKA_BOOTSTRAP_SERVER` the Kafka-Bootstrap-Server

## Metadata

To get an up and running instance of the metadata-service the following steps are required:

* provide a Kafka-instance with the following topics: `indexing-done` (or any other topic configured as property `metadata.topics.indexing-done-topic`) - will be triggered, once a new metadata-set is indexed - and `metadata-update` (or any other topic configured as property `metadata.topics.metadata-update-topic`) - will be triggered, once a new metadata-set is updated configure via the following properties:
    * in [provided-secrets.yml](https://github.com/EFS-OpenSource/superb-data-kraken-metadata/blob/develop/kubernetes/provided-secrets.yml) adjust `KAFKA_SASL_JAAS_CONFIG` (in the form of `org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://<KAFKA_BROKER>/;SharedAccessKeyName=<KEY_NAME>;SharedAccessKey=<KEY>";`) accordingly
* provide the following libraries locally:
    * [superb-data-kraken-logging](https://github.com/EFS-OpenSource/superb-data-kraken-logging)
    * [superb-data-kraken-common](https://github.com/EFS-OpenSource/superb-data-kraken-common)
* build a Docker-image to the container-registry of your liking. We provided you with 2 options:
    * with additional logging to [Azure Application Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview) ([Dockerfile](https://github.com/EFS-OpenSource/superb-data-kraken-metadata/blob/develop/Dockerfile))
    * and without Azure Application Insights ([Dockerfile-no-appinsights](https://github.com/EFS-OpenSource/superb-data-kraken-metadata/blob/develop/Dockerfile-no-appinsights))

If you choose to use the Docker-image with additional logging to Azure Application Insights, you need to provide extra properties in [config-map.yml](https://github.com/EFS-OpenSource/superb-data-kraken-metadata/blob/develop/kubernetes/config-map.yml): 

* `APP_INSIGHTS_CONNECTION_STRING` the Connection-String of your Azure Application Insights instance
* `APP_INSIGHTS_INSTRUMENTATION_KEY` the Instrumentation-Key of your Azure Application Insights instance

For additional configuration, please consider these properties within the [kubernetes-folder](https://github.com/EFS-OpenSource/superb-data-kraken-metadata/tree/develop/kubernetes):

* `postfix` a postfix you might want to add to your servides (:exclamation: should be consistent across your installation as cluster-interal domains are predifined with this postfix :exclamation:)
* `REALM` the specific realm set up with the openid connect (oidc) provider
* `CLIENT_ID` the client-id within defined realm (used for simplifying swagger-access)
* `CLIENT_ID_CONFIDENTIAL` the id of the confidential client used for Service Account-access. This Service Account should have the following permissions:
    * access to [OpenSearch](prerequesits.md#metadata-management) security-plugin (edit roles/rolesmappings/tenants)
    * update all indices in [OpenSearch](prerequesits.md#metadata-management)
* `CLIENT_SECRET_CONFIDENTIAL` the secret of the confidential client used for Service Account-access
* `LOG_LEVEL` the logging-level for metadata-service
* `CONTAINER_REGISTRY` the container-registry that stores the Docker-image
* `tagVersion` the tag-version of the Docker-image
* `DOMAIN` the domain metadata-service should be available at
* `ELASTICSEARCH_SERVICE` name of the kubernetes-service of the elasticsearch/opensearch-client-service
* `ELASTICSEARCH_SECURITY_ENDPOINT` endpoint of the elasticsearch/opensearch security-plugin (might be `/_plugins/_security/api` - OpenSearch - or `/_opendistro/_security/api` - Elasticsearch)

## Search

To get an up and running instance of the search-service no explicit steps are required.

For configuration, please consider these properties within the [kubernetes-folder](https://github.com/EFS-OpenSource/superb-data-kraken-search/tree/develop/kubernetes):

* `postfix` a postfix you might want to add to your servides (:exclamation: should be consistent across your installation as cluster-interal domains are predifined with this postfix :exclamation:)
* `REALM` the specific realm set up with the openid connect (oidc) provider
* `CLIENT_ID` the client-id within defined realm (used for simplifying swagger-access)
* `LOG_LEVEL` the logging-level for search
* `CONTAINER_REGISTRY` the container-registry that stores the Docker-image
* `tagVersion` the tag-version of the Docker-image
* `DOMAIN` the domain search should be available at
* `ELASTICSEARCH_SERVICE` name of the kubernetes-service of the elasticsearch/opensearch-client-service

## Ingest

To get an up and running instance of the metadata-service the following steps are required:

* provide an [EventSource](https://argoproj.github.io/argo-events/concepts/event_source/) for your Kafka-topic defined in [AccessManager](#accessmanager) (`accessmanager-commit` or any other topic configured as property `accessmanager.topic.upload-complete`) - this EventSource is referenced by ingest-Sensor:
    * EventSource accessmanager-commit:
        ```yaml title="accessmanager-commit.eventsource.yaml"
		apiVersion: argoproj.io/v1alpha1
		kind: EventSource
		metadata:
		  name: accessmanager-commit
		spec:
		  template:
			affinity:
			  nodeAffinity:
				preferredDuringSchedulingIgnoredDuringExecution:
				- weight: 1
				  preference:
					matchExpressions:
					- key: agentpool
					  operator: In
					  values:
					  - userpool
			container:
			  resources:
				requests:
				  cpu: 20m
				  memory: 200Mi
				limits:
				  cpu: 20m
				  memory: 200Mi
		  azureEventsHub:
			accessmanager:
			  fqdn: sdk-eventhub-dev.servicebus.windows.net
			  sharedAccessKeyName:
				name: azure-event-source
				key: sharedAccessKeyName
			  sharedAccessKey:
				name: azure-event-source
				key: sharedAccessKey
			  hubName: accessmanager-commit
        ```
* build the Docker-images to the container-registry of your liking:
    * [skipvalidation](https://github.com/EFS-OpenSource/superb-data-kraken-ingest/blob/develop/batch/skip_validation/Dockerfile)
    * [basicmetadata](https://github.com/EFS-OpenSource/superb-data-kraken-ingest/blob/develop/batch/basic_metadata/Dockerfile)
    * [movedata](https://github.com/EFS-OpenSource/superb-data-kraken-ingest/blob/develop/batch/move_data/Dockerfile)
    * [metadataindex](https://github.com/EFS-OpenSource/superb-data-kraken-ingest/blob/develop/batch/metadata_index/Dockerfile)

For additional configuration, please consider these properties within the [kubernetes-folder](https://github.com/EFS-OpenSource/superb-data-kraken-ingest/tree/develop/argo):

* `postfix` a postfix you might want to add to your servides (:exclamation: should be consistent across your installation as cluster-interal domains are predifined with this postfix :exclamation:)
* `CONTAINER_REGISTRY` the container-registry that stores the Docker-image
* `tagVersion` the tag-version of the Docker-image
* `DOMAIN` the domain oidc-provider is available at
* `SKIP_VALIDATE_ORGANIZATIONS` Names of organization for which the validation-tree (`basicmetadata`, `anonymize`, `enrichment` and `validate`) should be skipped
* `CLIENT_ID_CONFIDENTIAL` the id of the confidential client used for Service Account-access. This Service Account should have the following permissions:
    * update all indices in [OpenSearch](prerequesits.md#metadata-management)
    * update datasets within all spaces
* `CLIENT_SECRET_CONFIDENTIAL` the secret of the confidential client used for Service Account-access

## Worker

To get an up and running instance of the workers no explicit steps are required.

For configuration, please consider these properties within the [argo-folders of the respective workers](https://github.com/EFS-OpenSource/superb-data-kraken-worker):

* `postfix` a postfix you might want to add to your servides (:exclamation: should be consistent across your installation as cluster-interal domains are predifined with this postfix :exclamation:)
* `CONTAINER_REGISTRY` the container-registry that stores the Docker-image
* `tagVersion` the tag-version of the Docker-image

## UI

Will be defined soon.