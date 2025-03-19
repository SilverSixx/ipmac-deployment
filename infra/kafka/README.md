# Deploying Kafka on Kubernetes with Helm and Strimzi Operator

This README guides you through deploying a Kafka cluster on Kubernetes using a custom Helm chart and the Strimzi Kafka Operator.

## Prerequisites

* **kubectl:** Installed and configured to connect to your Kubernetes cluster.
* **Helm:** Installed on your local machine.

## Steps

1.  **Create the Helm Chart for Kafka Cluster Definition:**

    First, we'll create a Helm chart named `kafka`. This chart will contain the definition for our Kafka cluster as a Strimzi custom resource.

    ```sh
    helm create kafka
    ```

    This command will generate a basic Helm chart structure in a directory named `kafka`.

2.  **Add the Strimzi Helm Repository:**

    The Strimzi operator is managed using its own Helm chart. Add the Strimzi Helm repository to your Helm configuration:

    ```sh
    helm repo add strimzi https://strimzi.io/charts/
    // kafka ui
    helm repo add kafka-ui https://provectus.github.io/kafka-ui-charts
    ```

3.  **Update Helm Repositories:**

    Ensure your local Helm chart index is up-to-date:

    ```sh
    helm repo update
    ```

4.  **Install the Strimzi Operator:**

    Now, install the Strimzi Kafka Operator into your Kubernetes cluster. It's recommended to install it in a dedicated namespace, such as `strimzi`.

    ```sh
    kubectl create -f 'https://strimzi.io/install/latest?namespace=strimzi'
    ```

    Verify that the Strimzi operator pod is running in the `strimzi` namespace:

    ```sh
    kubectl get pods -n strimzi
    ```

5.  **Install the Kafka Cluster using Your Custom Chart:**

    Now, you can deploy your Kafka cluster using the `kafka` Helm chart. Navigate back to the directory containing the `kafka` chart (one level up from the `kafka` directory).

    ```sh
    cd ..
    ```

    Then, install the chart:

    ```sh
    helm install kafka-cluster ./kafka -n kafka --create-namespace
    ```

    * `kafka-cluster`: This is the release name for your Kafka deployment.
    * `./kafka`: This points to the directory of your `kafka` Helm chart.
    * `-n kafka`: This specifies the namespace where you want to deploy your Kafka cluster.
    * `--create-namespace`: This will create the namespace if it doesn't exist.

6. **Install the kafka ui**

    And expose the dns
    ```sh
    kubectl apply -f kafka-ui-ingress.yml -n strimzi
    ```

    Add the ip of the hostname to /etc/hosts

    ```sh
    echo "$(minikube ip) test.kafka-ui.org" | sudo tee -a /etc/hosts
    ```

    The UI need the internal dns to connect to the kafka broker
    ```yml
    yamlApplicationConfig:
      kafka:
        clusters:
        - bootstrapServers: kafka-cluster-kafka-bootstrap.strimzi.svc.cluster.local:9092
          name: my-kafka
    ```

    install the ui

    ```sh
    helm upgrade --install kafka-ui kafka-ui/kafka-ui -n strimzi \                                                                               ─╯
    --set 'yamlApplicationConfig.kafka.clusters[0].name=my-kafka' \
    --set 'yamlApplicationConfig.kafka.clusters[0].bootstrapServers=kafka-cluster-kafka-bootstrap.strimzi.svc.cluster.local:9092'
    ```

7.  **Deploy to other ENV**

```sh
helm install kafka-dev ./kafka -n DEV --create-namespace -f values-dev.yaml

helm install kafka-prod ./kafka -n PROD --create-namespace -f values-prod.yaml
```

## Customization

You can customize your Kafka cluster by modifying the `values.yaml` file in the `kafka` chart before installing it. For example, you can change the number of replicas, Kafka version, storage size, and other configurations.

Alternatively, you can use the `--set` flag during the `helm install` command to override specific values.

## Cleanup (Optional)

To uninstall the Kafka cluster:

```sh
helm uninstall kafka-cluster -n kafka
```