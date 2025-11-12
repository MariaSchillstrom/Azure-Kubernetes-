# Page 1

## Inledning

Jag har valt att skapa en applikation för svampentusiaster. Syftet med projektet är i sin enkelhet att implementera ett Kubernetes-kluster.

Jag valde att skapa klustret i **Azure**, eftersom jag personligen föredrar den plattformen framför **AWS**.

Eftersom det inte fanns någon komplett instruktion att följa, fick jag i vissa delar söka vägledning via **LLM** (Large Language Model) för att hitta lämpliga lösningar.

Projektets olika moment har genomförts i den ordning som presenteras nedan.;



<table><thead><tr><th>Steg</th><th>Lärarens moment</th><th width="212.7777099609375">Min motsvarighet (Azure)</th><th>Vad jag gör</th></tr></thead><tbody><tr><td>1</td><td>MongoDB Todo App Development</td><td>Utveckla Svampsidan lokalt</td><td>Bygg app, testa CRUD, containerisera med Docker Compose</td></tr><tr><td>2</td><td>Inbakat i deploy (AWS)</td><td>Push till ACR</td><td>Bygg och pusha image till Azure Container Registry</td></tr><tr><td>3</td><td>AWS EKS / Kubernetes GKE</td><td>Skapa AKS-kluster</td><td>Provisionera Kubernetes-kluster i Azure</td></tr><tr><td>4</td><td>Kubernetes EKS</td><td>Skapa Kubernetes Manifests</td><td>Skriv YAML-filer (deployment, service, secret, etc.)</td></tr><tr><td>5</td><td>Deploy Todo App / Kustomize</td><td>Deploya till AKS</td><td>Använd kubectl apply för manuell deploy</td></tr><tr><td>6</td><td>Ingress</td><td>Installera Nginx Ingress</td><td>Exponera appen externt via ingress.yaml</td></tr><tr><td>7</td><td>ArgoCD</td><td>Aktivera GitOps med ArgoCD</td><td>Installera ArgoCD, anslut repo, automatisk synk</td></tr></tbody></table>

### **1. Lokal utveckling(MongoDB Todo App Development)**

#### **1.1 Applikationsbeskrivning**

En fullständig CRUD-applikation för att hantera svampar, med:

* **Backend:** ASP.NET Core 8.0 med Minimal API
* **Frontend:** Vanilla JavaScript (HTML/CSS/JS)
* **Databas:** MongoDB
* **Admin UI:** Mongo Express
* **Containerisering:** Docker Compose

***

#### 1.2 Filstruktur

```
svamp-app/webapp/Svampsidan/
├── Dockerfile
├── Svampsidan.csproj
├── Program.cs
└── wwwroot/
    ├── docker-compose.yml
    ├── init-mongo.js
    ├── index.html
    └── crud.html
```



#### **1.3 Funktionalitet**

✅ Visa lista med svampar\
✅ Lägga till ny svamp\
✅ Bocka i som "hittad"\
✅ Uppdatera namn\
✅ Ta bort svamp\
✅ Data sparas i MongoDB och överlever omstarter

#### **Åtkomst**

* **Svampapp:** [http://localhost:8080](http://localhost:8080)
* **CRUD-sidan:** [http://localhost:8080/crud.html](http://localhost:8080/crud.html)
* **Mongo Express:** [http://localhost:8081](http://localhost:8081) (admin/pass)

#### **1.4 Frontend-struktur**

Frontenden består av två separata HTML-filer:

**1. `index.html` - Startsida**

* Enkel välkomstsida med information om appen
* Länk till CRUD-funktionaliteten

**2. `crud.html` - CRUD-funktionalitet**

* Hantera alla svampoperationer (Create, Read, Update, Delete)
* Visa lista med alla svampar
* Lägga till nya svampar
* Markera svampar som "hittade"
* Ta bort svampar

#### **1.5 Fördelar med denna separation:**

* ✅ Tydlig separation mellan presentation och funktionalitet
* ✅ Bättre användarupplevelse med dedikerad arbetssida
* ✅ Enklare att underhålla och vidareutveckla



#### 1.6 Bilder

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>



**Delete (kantarell)**

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**Update**&#x20;

* Från Karljohan till KarlJohan/Stensopp

<figure><img src=".gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**Create**

* Testsvamp är tillagd manuellt via terminalen, innan jag fick ordning på Program.cs samt CRUD.html
* Trattkantarell är tillagd via <img src=".gitbook/assets/image (4).png" alt="" data-size="original">

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
