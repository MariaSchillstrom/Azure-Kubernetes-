# Inledning



## Inledning

Jag har valt att skapa en applikation för svampentusiaster. Syftet med projektet är i sin enkelhet att implementera ett Kubernetes-kluster.

Jag valde att skapa klustret i **Azure**, eftersom jag personligen föredrar den plattformen framför **AWS**.

Eftersom det inte fanns någon komplett instruktion att följa, fick jag i vissa delar söka vägledning via **LLM** (Large Language Model) för att hitta lämpliga lösningar, samt söka information på Kubernetes egna hemsida, samt via portalen i Azure.

Projektets olika moment har genomförts i den ordning som presenteras nedan.;

<table><thead><tr><th>Steg</th><th>Lärarens moment</th><th width="212.7777099609375">Min motsvarighet (Azure)</th><th>Vad jag gör</th></tr></thead><tbody><tr><td>1</td><td>MongoDB Todo App Development</td><td>Utveckla Svampsidan lokalt</td><td>Bygg app, testa CRUD, containerisera med Docker Compose</td></tr><tr><td>2</td><td>Inbakat i deploy (AWS)</td><td>Push till ACR</td><td>Bygg och pusha image till Azure Container Registry</td></tr><tr><td>3</td><td>AWS EKS / Kubernetes GKE</td><td>Skapa AKS-kluster</td><td>Provisionera Kubernetes-kluster i Azure</td></tr><tr><td>4</td><td>Kubernetes EKS</td><td>Skapa Kubernetes Manifests</td><td>Skriv YAML-filer (deployment, service, secret, etc.)</td></tr><tr><td>5</td><td>Deploy Todo App / Kustomize</td><td>Deploya till AKS</td><td>Använd kubectl apply för manuell deploy</td></tr><tr><td>6</td><td>Ingress</td><td>Installera Nginx Ingress</td><td>Exponera appen externt via ingress.yaml</td></tr><tr><td>7</td><td>ArgoCD</td><td>Aktivera GitOps med ArgoCD</td><td>Installera ArgoCD, anslut repo, automatisk synk</td></tr></tbody></table>

### &#x20;**Komponenter:**

"Ett Kubernetes-kluster består av **Control Plane** (som hanterar klustret) och **Worker Nodes** (som kör applikationerna).

I Azure Kubernetes Service (AKS) hanteras Control Plane helt av Microsoft, vilket innebär att jag inte behöver konfigurera det manuellt. Mina worker nodes skapades automatiskt när jag körde `az aks create`, och där körs mina pods med svampappen och MongoDB.

De verktyg jag använt för att interagera med klustret är:

* **kubectl** - för att deploya och inspektera resurser
* **Azure CLI** - för att skapa och hantera AKS-klustret"

### &#x20; **Kubernetes-objekt:**

* **Pod** (man ser dem med kubectl get pods)
* **Deployment** (svampapp-deployment.yaml, mongodb-deployment.yaml)
* **Service** (svampapp-service.yaml, mongodb-service.yaml)
* **PersistentVolumeClaim** (mongodb-pvc.yaml)
* **Ingress** (svampapp-ingress.yaml)

### &#x20;**Administration:**

Jag har administrerat mitt Kubernetes-kluster med:

**Lokalt (under utveckling):**

* Docker Desktop för att köra applikationen lokalt med Docker Compose
* Ingen lokal Kubernetes-testning (gick direkt till molnet)

**I molnet (Azure):**

* Azure CLI för att skapa AKS-klustret
* kubectl för att deploya manifests och inspektera klustret
* ArgoCD för automatisk deployment från Git

Vanliga kommandon jag använt:

* `kubectl get pods` - se vilka pods som körs
* `kubectl get services` - se services och deras IP-adresser
* `kubectl apply -f manifests/` - deploya alla YAML-filer
* `kubectl logs <pod-name>` - felsökning"

### **Säkerhet**

I nuvarande implementation har jag **inte** implementerat några säkerhetsåtgärder. Applikationen är:

* Öppen för vem som helst (ingen autentisering)
* Kommunicerar över HTTP (inte HTTPS)
* MongoDB körs utan lösenord
* Inga Kubernetes Secrets används

**Detta är medvetet för skoluppgiften** som fokuserar på Kubernetes-deployment, inte säkerhet.

**I en produktionsmiljö skulle jag implementera:**

* Autentisering (OAuth, JWT)
* HTTPS med TLS-certifikat
* MongoDB med lösenord (lagrat i Kubernetes Secrets)
* Network Policies för att begränsa pod-kommunikation"

