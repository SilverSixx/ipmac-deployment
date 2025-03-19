# Deploying Keycloak on Kubernetes with Helm

This README guides you through deploying a keycloak cluster on Kubernetes using a custom Helm chart.

## Prerequisites

- **kubectl:** Installed and configured to connect to your Kubernetes cluster.
- **Helm:** Installed on your local machine.

## Steps

1.  **Create the Helm Chart:**

	```sh
	helm create keycloak
	```

2.  **Add the Helm Repository:**

	The Strimzi operator is managed using its own Helm chart. Add the Strimzi Helm repository to your Helm configuration:

	```sh
	helm repo add codecentric https://codecentric.github.io/helm-charts
	```

3.  **Update Helm Repositories:**

    Ensure your local Helm chart index is up-to-date:

    ```sh
    helm repo update
    ```

4.  **Install the keycloak Cluster using Your Custom Chart:**

	Now, you can deploy your keycloak cluster using the `keycloak` Helm chart. Navigate back to the directory containing the `keycloak` chart (one level up from the `keycloak` directory).

	```sh
	cd ..
	```

	Then, install the chart:

	```sh
	helm install keycloak ./keycloak -n keycloak --create-namespace -f values.yaml
	```

5.  **Deploy to other ENV**

```sh
helm install keycloak-dev ./keycloak -n DEV --create-namespace -f values-dev.yaml

helm install keycloak-prod ./keycloak -n PROD --create-namespace -f values-prod.yaml
```

## Cleanup (Optional)

To uninstall the keycloak cluster:

```sh
helm uninstall keycloak -n keycloak
```
