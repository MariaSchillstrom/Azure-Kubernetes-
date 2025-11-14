# Problem & lösningar

### 9. Problem & lösningar

#### **9. 1 ACR** \*¹

När jag försökte bygga och pusha min Docker image direkt i Azure med kommandot:

bash

````bash
az acr build --registry svampapp --image svampapp:v1 .
```

Fick jag felet:
```
(TasksOperationsNotAllowed) ACR Tasks requests for the registry svampapp 
are not permitted.
````

#### **Orsak:**

Azure for Students-subscriptionen har **begränsningar** som blockerar ACR Tasks (Azure's tjänst för att bygga Docker images i molnet).

#### **Lösning:**&#x20;

Istället för att låta Azure bygga imagen byggde jag den **lokalt** och pushade sedan den färdiga imagen till ACR:

**Se punkt 2**



### Referenser

**Websidor**



{% embed url="https://portal.azure.com/#allservices/category/All" %}

{% embed url="https://cloud-developer.educ8.se/clo/4.-run-cloud-applications/3.-kubernetes/index.html" %}

{% embed url="https://kubernetes.io/" %}



**LLM**

* [https://claude.ai/login?returnTo=%2F%3F](https://claude.ai/login?returnTo=%2F%3F)
* [https://chatgpt.com/](https://chatgpt.com/)
