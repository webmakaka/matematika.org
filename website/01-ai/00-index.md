---
layout: page
title: RASA
description: RASA
keywords: ai, nlp, nlu, natural language understanding, rasa
permalink: /ai/nlp/nlu/rasa/
---

# RASA

https://rasa.com/

<br/>

### Deploy Rasa в Mini Kubernetes

**Подготовка:**

[Инсталляция и подготовка minikube для работы в ubuntu 20.04](/containers/k8s/setup/minikube/)

[Инсталляция kubectl в ubuntu 20.04](/containers/k8s/setup/tools/kubectl/)

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

<!--

<br/>

https://www.youtube.com/watch?v=rlAQWbhwqLA&list=PL75e0qA87dlHQny7z43NduZHPo6qd-cRc

<br/>

    $ rasa train nlu
    $ rasa shell nlu

    $ rasa shell train

    $ rasa run actions && rasa shell

<br/>

### Installing Rasa Open Source on Ubuntu

https://www.youtube.com/watch?v=tXiYJM2vGJk&list=PL75e0qA87dlEWUA5ToqLLR026wIkk2evk&index=3

<br/>

python - 3.6.9

    $ mkdir rasa-project
    $ cd rasa-project

    venv

    $ pip install -U pip
    $ pip install rasa
    $ rasa --version
    $ rasa init
    $ ./rasa-assistant
-->
