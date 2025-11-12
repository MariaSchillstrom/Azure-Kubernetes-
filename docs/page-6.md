# Page 6

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
