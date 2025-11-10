# Azure-Kubernetes

## MongoDb svamp-applikation

Innan vi börjar med denna tutorial så bör ni ha;

* Skapat ett repository på git
* Klonat det&#x20;
* Öppnat det i VS Code



### 1. Skapa en ASP.NET Core applikation&#x20;

Börja med att se till så att Docker Desktop är igång.

```bash
docker --version
docker compose version
```

Reslutat

```bash
Docker version 27.4.0, build bde2b89
Docker Compose version v2.31.0-desktop.2                        
```

### 1.1 Skapa mappen för projektet&#x20;

```bash
mkdir svamp-app
cd svamp-app
```

### 1.2 Skapa applikationen

```bash
mkdir webapp
cd webapp
dotnet new web -n Svampsidan
cd ..

```



### 1.3 Uppdatera Program.cs för API Backend

Vad vi gör här är att vi;&#x20;

* &#x20;**Konfigurerar JSON-serialisering** - för att hantera camelCase
* **Lägger till MongoDB-klient** - kopplar till databasen via environment variables
* **Skapar API-endpoints** - GET, POST, PUT, DELETE för svampar
* **Definierar Svamp-modellen** - med MongoDB attribut

```bash
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

// Serve static files (frontend)
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
    updatedSvamp._id = existing._id; // Preserve MongoDB's ObjectId
    
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

// Svamp model
public class Svamp
{
    [BsonId]
    public ObjectId _id { get; set; }
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public bool IsComplete { get; set; }
}
```

