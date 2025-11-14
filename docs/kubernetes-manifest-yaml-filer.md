# Kubernetes manifest (yaml-filer)

## 4. Kubernets manifest

Just nu finns applikationen endast lokalt. Min docker image finns i molnet, och jag har AKS klustret som körs i Azure.&#x20;

#### 4.1 Analogi

* **Lokal app** = Du bakar pizza hemma ✅
* **ACR** = Du fotar receptet och lägger online ✅
* **AKS-kluster** = Du hyr en restaurang i molnet ✅
* **YAML-filer** = Instruktioner: "Baka 2 pizzor i restaurangen" ❌ (saknas)
* **kubectl apply** = Ge instruktionerna till restaurangen ❌ (inte gjort)

Det jag gör nu är att jag ska tala om för min Kubernetes vad den ska köra, genom att skapa följande yaml-filer.&#x20;

#### **4.1 För Svampappen:**

**4.1.1** **Deployment** (svampapp-deployment.yaml)

* Startar x antal containers med min Docker image&#x20;
* Säkerställer att de alltid körs (vid krash startaes en ny automatiskt)
* Hanterar uppdateringar

_**" Kör två kopior av min svamp-app container "**_

**4.1.2** **Service** (svampapp-service.yaml)

* Skapar intern IP i klustert (så att MongoDB kan nå appen)
* Skapar extern IP(LoadBalancer) så jag kan nå appen från internet

_**" Öppna dörren så vi kan komma in "**_

**4.3.3** **Persistent Volume Claim** (mongo-pvc.yaml)

* Beställer disk-space i Azure
* Monterar den i MongoDB container
* Data överlever omstart

_**" Ge MongoDB en hårddisk så data inte försvinner "**_



