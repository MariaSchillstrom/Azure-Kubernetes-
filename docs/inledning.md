# Inledning



### **Inledning**

Detta projekt är en komplett end-to-end-implementation av en containeriserad webapplikation som körs i ett Kubernetes-kluster i Azure (AKS). Projektet utgår från en egenutvecklad CRUD-applikation för svampentusiaster, byggd i ASP.NET Core med MongoDB. Målet har varit att följa hela livscykeln för en modern molnapplikation – från lokal utveckling och containerisering, till registry-hantering, kluster-deployment, trafikstyrning och fullt automatiserat GitOps-flöde.

Syftet har inte varit att skapa en avancerad produkt, utan att visa hur man från grunden bygger och driftar en applikation i Azure Kubernetes Service enligt etablerade DevOps-principer. Jag har därför valt att genomföra varje moment manuellt för att förstå vad som faktiskt händer, istället för att utgå från färdiga mallar eller automatiserade guider.

Projektet innehåller följande huvudmoment:

* **Lokal utveckling:** CRUD-app med MongoDB och Mongo Express, körd via Docker Compose.
* **Containerisering:** Bygga och testa Docker-containers lokalt.
* **Push till ACR:** Publicera images till Azure Container Registry.
* **Skapa AKS-kluster:** Provisionera Kubernetes-miljö via Azure CLI och koppla klustret mot ACR.
* **Manifest-filer:** Skapa egna deployment-, service-, PVC- och ingress-filer.
* **Deployment till AKS:** Applicera YAML-filer med kubectl och verifiera pods och tjänster.
* **Nginx Ingress Controller:** Installera ingress, styra extern trafik och konsolidera tjänster under en extern IP.
* **GitOps med ArgoCD:** Automatisk deployment vid ändringar i GitHub.
* **CI med GitHub Actions:** Bygga, tagga och pusha Docker-images automatiskt till ACR.

Sammanfattat visar projektet hur man tar en lokal applikation från utvecklingsmiljö → till container → till ACR → vidare till full drift i Kubernetes → och slutligen till ett automatiserat CI/CD-flöde med ArgoCD och GitHub Actions.

Jag valde Azure eftersom jag föredrar plattformen framför AWS i detta sammanhang och ville arbeta direkt med verktyg som Azure CLI, AKS och ACR. Där dokumentation saknades eller var otydlig använde jag LLM-stöd för felsökning och för att förstå specifika steg i processen.

***

### **Komponenter i lösningen**

Ett Kubernetes-kluster består av en **Control Plane** och flera **Worker Nodes** som kör applikationerna. I Azure Kubernetes Service hanteras Control Plane helt av Microsoft, och när jag körde `az aks create` skapades mina worker nodes automatiskt. Där körs mina pods med både svampapplikationen och MongoDB.

#### **Kubernetes-objekt som jag har skapat och använt**

* **Pod** – kör containerinstanserna
* **Deployment** – svampappens och MongoDBs deployments
* **Service** – svampapp-service (LoadBalancer/ClusterIP) och mongodb-service
* **PersistentVolumeClaim** – lagrar MongoDBs data
* **Ingress** – styr extern trafik via Nginx Ingress Controller

***

### **Verktyg och administration**

Jag har administrerat klustret med följande verktyg:

**Lokalt (utveckling):**

* Docker Desktop för lokal körning av app + databas
* Docker Compose för att köra hela miljön
* Ingen lokal Kubernetes-körning – deployment gjordes direkt till molnet

**I Azure (produktion/test):**

* Azure CLI för att skapa, koppla och hantera AKS
* `kubectl` för deployment, felsökning och inspektion av resurser
* ArgoCD för automatisk synkning och dubbelriktad GitOps

#### **Vanliga kommandon**

* `kubectl get pods` – visar körande pods
* `kubectl get services` – visar tjänster och IP-adresser
* `kubectl apply -f manifests/` – deploya alla YAML-filer
* `kubectl logs <pod>` – felsökning

***

### **Implementerad säkerhet**

Säkerhetsnivån är avsiktligt låg eftersom fokus ligger på Kubernetes-deployment, inte på produktionssäkerhet.

Implementerat:

* GitHub Secrets för känsliga värden
* ACR-autentisering via Service Principal
* Avgränsad åtkomst via `kubectl` och Azure CLI

Applikationen körs i övrigt:

* utan autentisering
* över HTTP
* med MongoDB utan lösenord
* utan Kubernetes Secrets

I en produktionsmiljö skulle jag implementera:

* OAuth/JWT för autentisering
* TLS/HTTPS
* Kubernetes Secrets för databaslösenord
* Network Policies för att begränsa pod-kommunikation
* RBAC-roller för kontrollerad åtkomst

**Översikt av projektet**

<table><thead><tr><th>Steg</th><th>Lärarens moment</th><th width="212.7777099609375">Min motsvarighet (Azure)</th><th>Vad jag gör</th></tr></thead><tbody><tr><td>1</td><td>MongoDB Todo App Development</td><td>Utveckla Svampsidan lokalt</td><td>Bygg app, testa CRUD, containerisera med Docker Compose</td></tr><tr><td>2</td><td>Inbakat i deploy (AWS)</td><td>Push till ACR</td><td>Bygg och pusha image till Azure Container Registry</td></tr><tr><td>3</td><td>AWS EKS / Kubernetes GKE</td><td>Skapa AKS-kluster</td><td>Provisionera Kubernetes-kluster i Azure</td></tr><tr><td>4</td><td>Kubernetes EKS</td><td>Skapa Kubernetes Manifests</td><td>Skriv YAML-filer (deployment, service, secret, etc.)</td></tr><tr><td>5</td><td>Deploy Todo App / Kustomize</td><td>Deploya till AKS</td><td>Använd kubectl apply för manuell deploy</td></tr><tr><td>6</td><td>Ingress</td><td>Installera Nginx Ingress</td><td>Exponera appen externt via ingress.yaml</td></tr><tr><td>7</td><td>ArgoCD</td><td>Aktivera GitOps med ArgoCD</td><td>Installera ArgoCD, anslut repo, automatisk synk</td></tr></tbody></table>

### &#x20;

###

