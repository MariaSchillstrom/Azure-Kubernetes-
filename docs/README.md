# Azure-Kubernetes

## Inledning

Jag har valt att skapa en applikation för  svampentusiaster. Versionen är i sin enkelhet, då den primära uppgiften var att implementera ett kubernetes-kluster.

Jag valde att skapa klustret i Azure, då jag är mer förtjust i det än AWS.

Detta innebär givetvis att jag inte har en komplett instruktion att följa, så fick fint fråga LLM på de delar där jag inte kunde följa någon form av instruktion.

Ordningen på mitt projekt har gjorts enligt nedan;

| Steg | Lärarens moment | Min  motsvarighet (Azure) | Vad du gör i det steget |
| ---- | --------------- | ------------------------- | ----------------------- |

| 1️⃣ | MongoDB Todo App Development | **Utveckla Svampsidan lokalt (med MongoDB)** | Du bygger själva applikationen lokalt. Testa CRUD-funktionalitet med MongoDB. Ingen container ännu. |
| --- | ---------------------------- | -------------------------------------------- | --------------------------------------------------------------------------------------------------- |

| 2️⃣ | Fundamentals | **Kubernetes-grunder (Fundamentals)** | Förstå pods, services, deployments, namespaces och YAML. Förbered för nästa steg. |
| --- | ------------ | ------------------------------------- | --------------------------------------------------------------------------------- |

| 3️⃣ | Inbakat i deploysteget i AWS | **Push till Azure Container Registry (ACR)** | Skapa `Dockerfile`, bygg och pusha imaget till ACR. (`az acr build` eller `docker push`) |
| --- | ---------------------------- | -------------------------------------------- | ---------------------------------------------------------------------------------------- |

| 4️⃣ | AWS EKS / Kubernetes GKE | **Skapa AKS-kluster** | Provisionera klustret i Azure och koppla det till ditt ACR (så AKS kan dra din image). |
| --- | ------------------------ | --------------------- | -------------------------------------------------------------------------------------- |

| 5️⃣ | Kubernetes EKS | **Skapa Kubernetes Manifests** | Skriv YAML-filer: `deployment.yaml`, `service.yaml`, ev. `configmap.yaml`, `secret.yaml`. Lägg i t.ex. `/manifests`-mapp i repo:t. |
| --- | -------------- | ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |

| 6️⃣ | — (mellan Deploy Todo App / Kustomize) | **Deploya till AKS (kubectl apply)** | Testa att deploya manuellt för att se att allting fungerar. |
| --- | -------------------------------------- | ------------------------------------ | ----------------------------------------------------------- |

| 7️⃣ | Ingress | **Installera Nginx Ingress Controller** | Installera ingress i klustret, skapa ingress.yaml, och exponera appen externt via en publik IP. |
| --- | ------- | --------------------------------------- | ----------------------------------------------------------------------------------------------- |

| 8️⃣ | ArgoCD | **Aktivera GitOps med ArgoCD** | Installera ArgoCD i AKS, anslut ditt repo och låt ArgoCD |
| --- | ------ | ------------------------------ | -------------------------------------------------------- |

###











