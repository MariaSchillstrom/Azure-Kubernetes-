# Azure-Kubernetes-
Deploya en applikation till ett Kubernetes-kluster (AKS)

Detta projekt är en komplett end-to-end-implementation av en containeriserad webapplikation som körs i ett Kubernetes-kluster i Azure (AKS). Applikationen är en egenutvecklad CRUD-lösning för svampentusiaster, byggd i ASP.NET Core med MongoDB som databas.

Målet har varit att följa hela livscykeln för en modern molnapplikation – från lokal utveckling och containerisering till registry-hantering, kluster-deployment, trafikstyrning och ett fullt automatiserat GitOps-flöde.

Syftet är inte att skapa en avancerad produkt, utan att visa hur man från grunden bygger, kör och driftar en applikation i Azure Kubernetes Service enligt etablerade DevOps-principer. Varje moment har genomförts manuellt för att få en djup förståelse för vad som faktiskt händer under huven.

🔧 Projektets huvudmoment

Lokal utveckling: CRUD-app med MongoDB och Mongo Express (Docker Compose)

Containerisering: Bygga och testa Docker-containers lokalt

Push till ACR: Publicera Docker-images i Azure Container Registry

Skapa AKS-kluster: Provisionera kluster via Azure CLI och koppla det till ACR

Kubernetes-manifests: Deployment, Service, PVC, Ingress

Deployment till AKS: kubectl apply och verifiering av pods/tjänster

Nginx Ingress Controller: Extern trafikstyrning via en gemensam IP

GitOps med ArgoCD: Automatisk deploy vid kodändringar

CI med GitHub Actions: Bygger, taggar och pushar Docker-images automatiskt

🚀 Sammanfattning

Projektet visar hela resan:

Lokal utveckling → Container → ACR → AKS → Ingress → GitOps → CI/CD

Resultatet är en fungerande, skalbar och automatiserad Kubernetes-deployment där GitHub Actions ansvarar för CI-delen och ArgoCD för CD-delen.

Jag valde Azure då jag föredrar plattformen framför AWS i detta sammanhang och ville arbeta med Azure CLI, AKS och ACR. Där dokumentation saknades eller var otydlig använde jag LLM-stöd för felsökning och för att förstå specifika steg.
