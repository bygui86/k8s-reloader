
# k8s-reloader

Example of how to use [stakater/Reloader](https://github.com/stakater/Reloader) in Kubernetes to trigger Pods restart upon ConfigMaps and Secrets updates

## Prerequisites

- Kubernetes cluster ([Minikube](https://minikube.sigs.k8s.io/docs/start/) or anything else)
- [Kustomize](https://kustomize.io/) installed locally

## Instructions

### 1 - Prepare environment

1. If you don't have the Kubernetes cluster already running, spin it up!

	```shell script
	minikube start --cpus=4 --memory=8192
	```

1. Deploy Reloader

	```shell script
	kustmize build reloader | kubectl apply -f -
	```

1. Create ConfigMap V1

	```shell script
	kubectl apply -f configmap-v1.yaml
	```

1. Create Secret V1

	```shell script
	kubectl apply -f secret-v1.yaml
	```

1. Create Deployment

	```shell script
	kubectl apply -f deployment.yaml
	```

1. Open a second terminal to watch pods

	```shell script
	watch kubectl get pods
	```

1. Open a third terminal to watch logs of Reloader controller

	```shell script
	kubectl logs -n reloader $(k get pods -l app=reloader -n reloader | grep -v NAME | awk '{print $1}') -f
	```

1. Till here everything should work normally

### 2 - Update ConfigMap test

1. Update ConfigMap to V2

	```shell script
	kubectl apply -f configmap-v2.yaml
	```

1. Very soon you should observe:

	- a pod restart in the second terminal

	- in the third terminal some logs like
	
	```
	time="2020-11-12T09:31:42Z" level=info msg="Changes detected in 'kubectl' of type 'CONFIGMAP' in namespace 'default'"
	time="2020-11-12T09:31:42Z" level=info msg="Updated 'pause' of type 'Deployment' in namespace 'default'"
	```

### 3 - Update Secret test

1. Update Secret to V2

	```shell script
	kubectl apply -f secret-v2.yaml
	```

1. Very soon you should observe:

	- a pod restart in the second terminal

	- in the third terminal some logs like
	
	```
	time="2020-11-12T09:28:38Z" level=info msg="Changes detected in 'kubectl' of type 'SECRET' in namespace 'default'"
	time="2020-11-12T09:28:38Z" level=info msg="Updated 'pause' of type 'Deployment' in namespace 'default'"
	```
