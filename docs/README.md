# Azure-Kubernetes

## Inledning

Jag har valt att skapa en applikation för svampentusiaster. Syftet med projektet är i sin enkelhet att implementera ett Kubernetes-kluster.

Jag valde att skapa klustret i **Azure**, eftersom jag personligen föredrar den plattformen framför **AWS**.

Eftersom det inte fanns någon komplett instruktion att följa, fick jag i vissa delar söka vägledning via **LLM** (Large Language Model) för att hitta lämpliga lösningar.

Projektets olika moment har genomförts i den ordning som presenteras nedan.;



<table><thead><tr><th>Steg</th><th>Lärarens moment</th><th width="212.7777099609375">Min motsvarighet (Azure)</th><th>Vad jag gör</th></tr></thead><tbody><tr><td>1</td><td>MongoDB Todo App Development</td><td>Utveckla Svampsidan lokalt</td><td>Bygg app, testa CRUD, containerisera med Docker Compose</td></tr><tr><td>2</td><td>Inbakat i deploy (AWS)</td><td>Push till ACR</td><td>Bygg och pusha image till Azure Container Registry</td></tr><tr><td>3</td><td>AWS EKS / Kubernetes GKE</td><td>Skapa AKS-kluster</td><td>Provisionera Kubernetes-kluster i Azure</td></tr><tr><td>4</td><td>Kubernetes EKS</td><td>Skapa Kubernetes Manifests</td><td>Skriv YAML-filer (deployment, service, secret, etc.)</td></tr><tr><td>5</td><td>Deploy Todo App / Kustomize</td><td>Deploya till AKS</td><td>Använd kubectl apply för manuell deploy</td></tr><tr><td>6</td><td>Ingress</td><td>Installera Nginx Ingress</td><td>Exponera appen externt via ingress.yaml</td></tr><tr><td>7</td><td>ArgoCD</td><td>Aktivera GitOps med ArgoCD</td><td>Installera ArgoCD, anslut repo, automatisk synk</td></tr></tbody></table>

#### **DEL 1: Lokal utveckling(MongoDB Todo App Development)**

**Applikationsbeskrivning**

En fullständig CRUD-applikation för att hantera svampar, med:

* **Backend:** ASP.NET Core 8.0 med Minimal API
* **Frontend:** Vanilla JavaScript (HTML/CSS/JS)
* **Databas:** MongoDB
* **Admin UI:** Mongo Express
* **Containerisering:** Docker Compose

***

**Filstruktur**

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



#### **Funktionalitet**

✅ Visa lista med svampar\
✅ Lägga till ny svamp\
✅ Bocka i som "hittad"\
✅ Uppdatera namn\
✅ Ta bort svamp\
✅ Data sparas i MongoDB och överlever omstarter

**Åtkomst**

* **Svampapp:** [http://localhost:8080](http://localhost:8080)
* **CRUD-sidan:** [http://localhost:8080/crud.html](http://localhost:8080/crud.html)
* **Mongo Express:** [http://localhost:8081](http://localhost:8081) (admin/pass)

#### **Frontend-struktur**

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

**Fördelar med denna separation:**

* ✅ Tydlig separation mellan presentation och funktionalitet
* ✅ Bättre användarupplevelse med dedikerad arbetssida
* ✅ Enklare att underhålla och vidareutveckla

#### **DEL 2: PUSH TILL ACR** \*¹&#x20;

**Varför ACR?** Min Docker image finns just nu endast lokalt på datorn. AKS (Azure Kubernetes Service) kan inte komma åt min lokala dator, därför behöver imagen finnas i molnet. ACR (Azure Container Registry) fungerar som ett molnbaserat bildbibliotek där Kubernetes kan hämta imagen.

**Flöde:**

```
Lokal dator (Docker image) 
    ↓ PUSH
ACR (Lagring i Azure) 
    ↓ PULL
AKS (Kubernetes kör appen)
```

**Steg som utfördes:**

1. **Skapade Resource Group:**

```bash
   az group create --name mysvampsidaRG --location westeurope
```

2. **Skapade ACR:**

```bash
   az acr create --resource-group mysvampsidaRG --name svampapp --sku Basic
```

3. **Loggade in i ACR:**

```bash
   az acr login --name svampapp
```

4. **Tagga imagen för ACR**

```bash
   docker tag svampapp-web:latest svampapp.azurecr.io/svampapp:v1
```

5. **Pusha till ACR**

```
docker push svampapp.azurecr.io/svampapp:v1
```

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**Resultat:** Docker imagen finns nu i `svampapp.azurecr.io/svampapp:v1` och kan användas av AKS.













### **Slutgiltiga filer**

**Program.cs**

csharp

```csharp
using Microsoft.AspNetCore.Mvc;
using MongoDB.Bson;
using MongoDB.Bson.Serialization.Attributes;
using MongoDB.Driver;

var builder = WebApplication.CreateBuilder(args);

// Configure JSON serialization
builder.Services.ConfigureHttpJsonOptions(options =>
{
    options.SerializerOptions.PropertyNamingPolicy = System.Text.Json.JsonNamingPolicy.CamelCase;
});

// Add MongoDB client
var mongoHost = Environment.GetEnvironmentVariable("MONGODB_HOST") ?? "localhost";
var mongoPort = Environment.GetEnvironmentVariable("MONGODB_PORT") ?? "27017";
var mongoDatabase = Environment.GetEnvironmentVariable("MONGODB_DATABASE") ?? "SvampappDb";

var connectionString = $"mongodb://{mongoHost}:{mongoPort}";
builder.Services.AddSingleton<IMongoClient>(new MongoClient(connectionString));
builder.Services.AddScoped(sp =>
{
    var client = sp.GetRequiredService<IMongoClient>();
    return client.GetDatabase(mongoDatabase);
});

var app = builder.Build();

// Serve static files
app.UseDefaultFiles();
app.UseStaticFiles();

// API Endpoints
app.MapGet("/api/svampar", async (IMongoDatabase db) =>
{
    var collection = db.GetCollection<Svamp>("Svampar");
    var svampar = await collection.Find(_ => true).ToListAsync();
    return Results.Ok(svampar);
});

app.MapGet("/api/svampar/{id}", async (int id, IMongoDatabase db) =>
{
    var collection = db.GetCollection<Svamp>("Svampar");
    var svamp = await collection.Find(s => s.Id == id).FirstOrDefaultAsync();
    return svamp is not null ? Results.Ok(svamp) : Results.NotFound();
});

app.MapPost("/api/svampar", async (Svamp svamp, IMongoDatabase db) =>
{
    var collection = db.GetCollection<Svamp>("Svampar");
    await collection.InsertOneAsync(svamp);
    return Results.Created($"/api/svampar/{svamp.Id}", svamp);
});

app.MapPut("/api/svampar/{id}", async (int id, Svamp updatedSvamp, IMongoDatabase db) =>
{
    var collection = db.GetCollection<Svamp>("Svampar");
    var existing = await collection.Find(s => s.Id == id).FirstOrDefaultAsync();
    if (existing == null) return Results.NotFound();

    updatedSvamp.Id = id;
    updatedSvamp._id = existing._id;
    var result = await collection.ReplaceOneAsync(s => s.Id == id, updatedSvamp);
    return result.ModifiedCount > 0 ? Results.Ok(updatedSvamp) : Results.NotFound();
});

app.MapDelete("/api/svampar/{id}", async (int id, IMongoDatabase db) =>
{
    var collection = db.GetCollection<Svamp>("Svampar");
    var result = await collection.DeleteOneAsync(s => s.Id == id);
    return result.DeletedCount > 0 ? Results.Ok() : Results.NotFound();
});

app.Run();

public class Svamp
{
    [BsonId]
    public ObjectId _id { get; set; }
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public bool IsComplete { get; set; }
}
```

**docker-compose.yml**

yaml

```yaml
services:
  mongodb:
    image: mongo:latest
    container_name: svamp-mongodb
    ports:
      - "27017:27017"
    volumes:
      - mongodb-data:/data/db
      - ./init-mongo.js:/docker-entrypoint-initdb.d/init-mongo.js:ro
    networks:
      - svampapp-network
    healthcheck:
      test: echo 'db.runCommand("ping").ok' | mongosh localhost:27017/test --quiet
      interval: 10s
      timeout: 5s
      retries: 5

  mongo-express:
    image: mongo-express:latest
    container_name: svamp-mongo-express
    ports:
      - "8081:8081"
    environment:
      ME_CONFIG_MONGODB_URL: mongodb://mongodb:27017
      ME_CONFIG_BASICAUTH_USERNAME: admin
      ME_CONFIG_BASICAUTH_PASSWORD: pass
    networks:
      - svampapp-network
    depends_on:
      mongodb:
        condition: service_healthy

  webapp:
    build:
      context: ./webapp/Svampsidan
      dockerfile: Dockerfile
    container_name: svampapp-webapp
    ports:
      - "8080:8080"
    environment:
      MONGODB_HOST: mongodb
      MONGODB_PORT: "27017"
      MONGODB_DATABASE: SvampappDb
    networks:
      - svampapp-network
    depends_on:
      mongodb:
        condition: service_healthy

volumes:
  mongodb-data:

networks:
  svampapp-network:
    driver: bridge
```

**init-mongo.js**

javascript

```javascript
db = db.getSiblingDB('SvampappDb');

db.Svampar.insertMany([
    {
        "Id": 1,
        "Name": "Kantarell",
        "IsComplete": false
    },
    {
        "Id": 2,
        "Name": "Karljohan",
        "IsComplete": true
    }
]);

print("Database initialized with sample svampar!");
```

***



***



### Problem & lösningar

#### **ACR** \*¹

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



