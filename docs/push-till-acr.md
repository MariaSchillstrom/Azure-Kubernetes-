# Push till ACR

#### **2: PUSH TILL ACR** \*¹&#x20;

**Varför ACR?** Min Docker image finns just nu endast lokalt på datorn. AKS (Azure Kubernetes Service) kan inte komma åt min lokala dator, därför behöver imagen finnas i molnet. ACR (Azure Container Registry) fungerar som ett molnbaserat bildbibliotek där Kubernetes kan hämta imagen.

#### **2.1 Flöde:**

```
Lokal dator (Docker image) 
    ↓ PUSH
ACR (Lagring i Azure) 
    ↓ PULL
AKS (Kubernetes kör appen)
```

#### **2.2 Steg som utfördes:**

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

```bash
docker push svampapp.azurecr.io/svampapp:v1
```

<figure><img src=".gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

#### **2.3 Resultat:** Docker imagen finns nu i `svampapp.azurecr.io/svampapp:v1` och kan användas av AKS.
