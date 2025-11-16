# Azure-Kubernetes-
Deploya en applikation, till ett kubernetesklusterDetta projekt är en komplett end-to-end-implementation av en containeriserad webapplikation som körs i ett Kubernetes-kluster i Azure (AKS). Projektet utgår från en egenutvecklad CRUD-applikation för svampentusiaster, byggd i ASP.NET Core med MongoDB. Målet har varit att följa hela livscykeln för en modern molnapplikation – från lokal utveckling och containerisering, till registry-hantering, kluster-deployment, trafikstyrning och fullt automatiserat GitOps-flöde.
Syftet har inte varit att skapa en avancerad produkt, utan att visa hur man från grunden bygger och driftar en applikation i Azure Kubernetes Service enligt etablerade DevOps-principer. Jag har därför valt att genomföra varje moment manuellt för att förstå vad som faktiskt händer, istället för att utgå från färdiga mallar eller automatiserade guider.
Projektet innehåller följande huvudmoment:
Lokal utveckling: CRUD-app med MongoDB och Mongo Express, körd via Docker Compose.
Containerisering: Bygga och testa Docker-containers lokalt.
Push till ACR: Publicera images till Azure Container Registry.
Skapa AKS-kluster: Provisionera Kubernetes-miljö via Azure CLI och koppla klustret mot ACR.
Manifest-filer: Skapa egna deployment-, service-, PVC- och ingress-filer.
Deployment till AKS: Applicera YAML-filer med kubectl och verifiera pods och tjänster.
Nginx Ingress Controller: Installera ingress, styra extern trafik och konsolidera tjänster under en extern IP.
GitOps med ArgoCD: Automatisk deployment vid ändringar i GitHub.
CI med GitHub Actions: Bygga, tagga och pusha Docker-images automatiskt till ACR.
Sammanfattat visar projektet hur man tar en lokal applikation från utvecklingsmiljö → till container → till ACR → vidare till full drift i Kubernetes → och slutligen till ett automatiserat CI/CD-flöde med ArgoCD och GitHub Actions.
Jag valde Azure eftersom jag föredrar plattformen framför AWS i detta sammanhang och ville arbeta direkt med verktyg som Azure CLI, AKS och ACR. Där dokumentation saknades eller var otydlig använde jag LLM-stöd för felsökning och för att förstå specifika steg i processen.
