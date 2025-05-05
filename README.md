# llm-local-k8s
Configuring LLM K8s

## Pre-req
Docker-hub/Rancher-desktop configured with k8s
Run the following command to verify 
```
 kubectl get pod
```

## Create a k8s namesapce 
A namespace is simialr to python virtual env and separates your config within the 
```
kubectl create ns llm 
```

## Create a deployment 
Deployment allows k8s to deploy pods. copy the deployment.yml to a local directory and execute the following command.
If your namespace is not 'llm' then change the namesapce in the deployment accordinly 
```
kubectl apply -f deployment.yml
```
It will deploy the ollama pod, run the following command to check the pod creation as it will take a while to pull the ollama layers 

```
kubectl get pod -n llm
```

Wait till the pod status becomes running

<img width="626" alt="image" src="https://github.com/user-attachments/assets/4d9ade9c-08cb-49de-8084-73ae31ddc7da" />

## Create a service
K8s service is the entry point of your microservices, when you invoke the url:port, it route your requests to a pod
Download the service.yml and execute the following command 
If your namespace is not 'llm' then change the namesapce in the deployment accordinly 

```
kubectl apply -f service.yml
```
<img width="717" alt="image" src="https://github.com/user-attachments/assets/41e04925-3d3e-406e-a955-29cfb74bc6be" />

run the following command to verify your service, llm is my namespace

```
kubectl get svc -n llm
```

<img width="726" alt="image" src="https://github.com/user-attachments/assets/392a124b-1c71-4189-95dc-b72f8f9ae450" />

## Send request to Ollama
the service is running in 30000 port. you can try accessing it from a browser 

<img width="419" alt="image" src="https://github.com/user-attachments/assets/e9457a38-2c01-4a81-8af5-658bdd34325a" />

### send a curl command 
We all love water-jug, lets find out
```
curl -s http://127.0.0.1:30000/api/generate -d '{
"model": "llama2",
"prompt": "Why water-jug is important for AI?"
}' | jq -r '.response' | tr -d '\n'

```

It will return 'null' as ollama doesnt pull any model untill we pull it manually
<img width="814" alt="image" src="https://github.com/user-attachments/assets/1aa8cae4-2aba-44d2-b822-84ec12880b29" />

### Exec into the pod
run the get pod command to get the pod's name
<img width="683" alt="image" src="https://github.com/user-attachments/assets/64129850-8f67-459d-9273-230fda0c6b9d" />

run bash in the container, replace the name 'ollama-7b8ddfc68f-ggpqb' with your pod's name
```
kubectl exec -it ollama-7b8ddfc68f-ggpqb -n llm -- bash
```
run list command to see if there are any models 
```
ollama list
```
<img width="506" alt="image" src="https://github.com/user-attachments/assets/3ffe4b03-b894-4fd8-aa43-77868765ee7e" />

pull the llama2 latest model 
```
ollama pull llama2
```
it will take a while to download 3.2 GB

<img width="630" alt="image" src="https://github.com/user-attachments/assets/b5cd8fac-dfa9-4c18-abec-fbb058f05abd" />

now exit from the container's bash

```
exit
```

<img width="425" alt="image" src="https://github.com/user-attachments/assets/388ab034-c275-419f-b6d5-8c435a7b1fa2" />

### again send the water-jug query
execute the following curl command

```
curl -s http://127.0.0.1:30000/api/generate -d '{
"model": "llama2",
"prompt": "Why water-jug is important for AI?"
}' | jq -r '.response' | tr -d '\n'

```

It will give you a response 

<img width="1321" alt="image" src="https://github.com/user-attachments/assets/0b28de45-32df-4d37-8a66-d633987923ae" />







