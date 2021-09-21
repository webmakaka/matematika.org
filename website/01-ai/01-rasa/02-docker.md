---
layout: page
title: Rasa Docker
description: Rasa Docker
keywords: ai, nlp, nlu, natural language understanding, rasa, docker
permalink: /ai/nlp/nlu/rasa/docker/
---

# Rasa Docker

<br/>

[Инсталляция docker и docker-compose в ubuntu 20.04](//gitops.ru/containers/docker/setup/ubuntu/)

<br/>

**Understanding Rasa Deployments - Intro to Docker**

<br/>

https://www.youtube.com/watch?v=_UwEbGVjwEg

<br/>

    $ cd ~/projects/rasa-project/rasa/rasa-assistant
    $ rasa run --enable-api

<br/>

```
$ curl \
    --data '{
      "text":"I am doing great"
      }' \
    --header "Content-Type: application/json" \
    --request POST \
    --url http://localhost:5005/model/parse \
    | jq
```

<br/>

OK!

<br/>

    $ vi Dockerfile

<br/>

```
FROM python:3.7-slim

RUN python -m pip install rasa

WORKDIR /app
COPY . .

RUN rasa train nlu

USER 1001

ENTRYPOINT ['rasa']

CMD ["run", "--enable-api", "--port", "8080"]
```

<br/>

    $ vi .dockerignore

<br/>

```
tests/*
models/*
actions/*
**/*.md
venv
```

<br/>

    $ docker build -t webmakaka/rasa-demo .

<br/>

    // При обучении мой комп с 4GB видео просто выключается.
    // $ docker run -it -p 8080:8080 webmakaka/rasa-demo
    $ docker run -it -p 8080:8080 koaning/rasa-demo

<br/>

```
$ curl \
    --data '{
      "text":"I am doing great"
      }' \
    --header "Content-Type: application/json" \
    --request POST \
    --url http://localhost:8080/model/parse \
    | jq
```

<br/>

OK!

<br/>

    // Запуск интерактивного shell
    // $ docker run -it -p 8080:8080 koaning/rasa-demo shell

<br/>

**Understanding Rasa Deployments - Premade Rasa Containers**

<br/>

https://www.youtube.com/watch?v=i1FCsQ271DA

<br/>

https://hub.docker.com/r/rasa/rasa

<br/>

    $ cd ~/projects/rasa-project/rasa/rasa-assistant
    $ docker run -it -p 8080:8080 -v $(pwd):/app rasa/rasa:2.8.1-full run --enable-api --port 8080

<br/>

    // Понизить версию rasa до 2.5.0
    $ docker run -it -p 8080:8080 -v $(pwd):/app rasa/rasa:2.5.0-full run --enable-api --port 8080

<br/>

```
$ curl \
    --data '{
      "text":"I am feeling great"
      }' \
    --header "Content-Type: application/json" \
    --request POST \
    --url http://localhost:8080/model/parse \
    | jq
```

<br/>

localhost:8080/version

<br/>

### Deploy Rasa в Mini Kubernetes

**Подготовка:**

[Инсталляция и подготовка minikube для работы в ubuntu 20.04](//gitops.ru/containers/k8s/setup/minikube/)

[Инсталляция kubectl в ubuntu 20.04](//gitops.ru/containers/k8s/setup/tools/kubectl/)

<br/>

**По видео:**  
https://www.youtube.com/watch?v=Cj-LFSCf7Jw

<br/>

```
$ sudo apt install -y jq
```

<br/>

```yaml
$ cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rasa-custom-model
  labels:
    app: rasa
spec:
  replicas: 1
  selector:
    matchLabels:
      app: rasa
  template:
    metadata:
      labels:
        app: rasa
    spec:
      containers:
      - name: rasa-demo
        image: koaning/rasa-demo
        imagePullPolicy: Always
        ports:
            - containerPort: 8080
        command: ["rasa", "run", "--enable-api", "--port", "8080", "--debug"]
EOF
```

<br/>

```yaml
$ cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  name: rasa-web
spec:
  type: LoadBalancer
  ports:
    - port: 8080
      targetPort: 8080
  selector:
    app: rasa
EOF
```

<br/>

```
$ kubectl get pods
NAME                                 READY   STATUS    RESTARTS   AGE
rasa-custom-model-7898d99f5c-crvmz   1/1     Running   0          100s
```

<br/>

```
$ kubectl get svc
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          13m
rasa-web     LoadBalancer   10.104.124.14   <pending>     8080:30341/TCP   2m2s
```

<br/>

```
$ kubectl port-forward svc/rasa-web 8080:8080
```

<br/>

```
$ curl \
    --data '{
      "text":"hello"
      }' \
    --header "Content-Type: application/json" \
    --request POST \
    --url http://localhost:8080/model/parse \
    | jq
```

<br/>

**response:**

```
{
  "text": "hello",
  "intent": {
    "id": -5103827793865461000,
    "name": "greet",
    "confidence": 0.9999986886978149
  },
  "entities": [],
  "intent_ranking": [
    {
      "id": -5103827793865461000,
      "name": "greet",
      "confidence": 0.9999986886978149
    },
    {
      "id": 5536158928852286000,
      "name": "affirm",
      "confidence": 4.7248207124539476e-07
    },
    {
      "id": 6686709639357369000,
      "name": "bot_challenge",
      "confidence": 3.622365909450309e-07
    },
    {
      "id": -3706007115391047700,
      "name": "mood_great",
      "confidence": 1.7313301725607744e-07
    },
    {
      "id": 5206614780026875000,
      "name": "goodbye",
      "confidence": 1.131188653857862e-07
    },
    {
      "id": 3674740820619989000,
      "name": "mood_unhappy",
      "confidence": 6.645655048487242e-08
    },
    {
      "id": 6872611688719536000,
      "name": "deny",
      "confidence": 5.379793321935722e-08
    }
  ],
  "response_selector": {
    "all_retrieval_intents": [],
    "default": {
      "response": {
        "id": null,
        "responses": null,
        "response_templates": null,
        "confidence": 0,
        "intent_response_key": null,
        "utter_action": "utter_None",
        "template_name": "utter_None"
      },
      "ranking": []
    }
  }
}

```

<br/>

### Удаление ранее созданных ресурсов kubernetes

<br/>

    $ kubectl delete svc rasa-web
    $ kubectl delete deployment rasa-custom-model
