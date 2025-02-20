# Webinar | Cloud Native Application Development with Kubernetes.
This is the source code repository accompanying the webinar Cloud Native Application Development with Kubernetes.

## Overview
The sample application is a delivery tracker, allowing a "customer" to track a "rider's" progress in making a delivery.

The application is composed of the following _services_:

- `application/webapp`

  A NodeJS (express) application, which serves an HTML page to the user, fetches the latest rider location update from a Redis database and shows the position on a map.

- `application/api`

  A NodeJS (express) application, which accepts riders' location updates and stores them in a Redis database.

(In addition, a Redis database server is also made available as an application service)

To deploy these services onto a Kubernetes cluster, the `k8` folder contains deployment and service definitions for each of the three services (_webapp_, _api_ and _redis_).

## Installation
Ensure that the following is installed on your machine:

- [Docker](https://www.docker.com/get-started)

  Required for building Docker images and running Minikube.

- [Minikube](https://minikube.sigs.k8s.io/docs/start/)

  Developer tool to launch a local Kubernetes cluster.

- [kubectl](https://kubernetes.io/docs/tasks/tools/)

  CLI tool to interact with and administer the cluster.

## Usage
The following steps will deploy the sample application onto a local K8 cluster.

### Build Docker images for application services.
Open up a new terminal window and ensure that you're targeting the Minikube Docker daemon:

    eval $(minikube docker-env)

> See https://minikube.sigs.k8s.io/docs/handbook/pushing/#1-pushing-directly-to-the-in-cluster-docker-daemon-docker-env for more information about why this step is necessary.

Navigate to the `appliction/api` folder and run:

    docker build -t sample/api .

Navigate to the `application/webapp` folder. 

If you want to be able to display location updates on a Google Map, you need to first [acquire an API key](https://developers.google.com/maps/documentation/javascript/get-api-key).

Once you've got an API key, add it to the script tag in `webapp/public/index.html`.

Finally, build the Docker image:

    docker build -t sample/webapp .

Run `docker images` and verify that all Docker images have been built properly.

### Start Minikube
In a **separate** terminal window, launch a local cluster:

    minikube start

Then, to be able to run so-called Kubernetes _LoadBalancer_ services, run:

    minikube tunnel

### Create K8 resources.
To create the deployments and services described by the definitions in the `k8` folder, go back to the previous terminal window (in which you built Docker images) and run:

    kubectl apply -f k8/

> Note: You may also run this command for each of the *.yml files, to create one resource at a time.

Verify that all resources have been successfully created via:

    kubectl get all

### Test the sample application.
To update a location, invoke the location API service:

    curl -X POST http://localhost:8080/api/location -H 'Content-Type: application/json' -d '{"lat":55.60660,"lng":12.99786,"zoom":17}'

View the update at `http://localhost`.

### Stop Minikube.
Terminate the local cluster:

    minikube stop

> Created resources will remain when the cluster is started up again. You may delete resources via the `kubectl delete <resource>` command.

## Contact
Marc Klefter | marc.klefter@edument.se