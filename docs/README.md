# Azure-Kubernetes

## Inledning

Jag har valt att skapa en applikation för  svampentusiaster. Versionen är i sin enkelhet, då den primära uppgiften var att implementera ett kubernetes-kluster.

Jag valde att skapa klustret i Azure, då jag är mer förtjust i det än AWS.

Detta innebär givetvis att jag inte har en komplett instruktion att följa, så fick fint fråga LLM på de delar där jag inte kunde följa någon form av instruktion.

Ordningen på mitt projekt har gjorts enligt nedan;

| Steg | Lärarens moment | Min  motsvarighet (Azure) | Vad du gör i det steget |
| ---- | --------------- | ------------------------- | ----------------------- |

| 1️⃣ | MongoDB Todo App Development | **Utveckla Svampsidan lokalt (med MongoDB)** | Byggt själva applikationen lokalt. Testa CRUD-funktionalitet med MongoDB. **Containerisera med Docker & Docker Compose.** |
| --- | ---------------------------- | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |

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

#### **DEL 1: LOKAL UTVECKLING (MongoDB Todo App Development)**

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

**OBS! In med bilder på CRUD**



**Slutgiltiga filer**

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

**Funktionalitet**

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

#### Funktionalitet&#x20;

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

***





