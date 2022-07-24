---
layout: page
title: Conversational AI with Rasa
description: Conversational AI with Rasa
keywords: book, rasa, ai, machine learning
permalink: /ai/nlp/nlu/rasa/book/conversational-ai-with-rasa/
---

# [Book] Conversational AI with Rasa

<br/>

### Prepare environment

```
$ pip install wheel

$ git clone https://github.com/PacktPublishing/Conversational-AI-with-RASA

$ cd Conversational-AI-with-RASA/

$ pip install -r requirements.txt
```

<br/>

### Starting the Rasa NLU service

```
$ rasa run --enable-api
```

<br/>

```
$ curl localhost:5005/model/parse -d '{"text":"hello"}'
```

<br/>

### Practice – building the NLU part of a medical bot

```
$ cd Chapter02/
$ rasa train nlu
$ rasa shell nlu

//
// $ rasa shell -m models/nlu-<timestamp>.tar.gz
```

<br/>

```
What medicine should I take if I catch a cold?
```
