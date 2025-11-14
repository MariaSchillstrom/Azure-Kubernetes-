# Inledning



## Inledning

Jag har valt att skapa en applikation för svampentusiaster. Syftet med projektet är i sin enkelhet att implementera ett Kubernetes-kluster.

Jag valde att skapa klustret i **Azure**, eftersom jag personligen föredrar den plattformen framför **AWS**.

Eftersom det inte fanns någon komplett instruktion att följa, fick jag i vissa delar söka vägledning via **LLM** (Large Language Model) för att hitta lämpliga lösningar, samt söka information på Kubernetes egna hemsida, samt via portalen i Azure.

Projektets olika moment har genomförts i den ordning som presenteras nedan.;



<table><thead><tr><th>Steg</th><th>Lärarens moment</th><th width="212.7777099609375">Min motsvarighet (Azure)</th><th>Vad jag gör</th></tr></thead><tbody><tr><td>1</td><td>MongoDB Todo App Development</td><td>Utveckla Svampsidan lokalt</td><td>Bygg app, testa CRUD, containerisera med Docker Compose</td></tr><tr><td>2</td><td>Inbakat i deploy (AWS)</td><td>Push till ACR</td><td>Bygg och pusha image till Azure Container Registry</td></tr><tr><td>3</td><td>AWS EKS / Kubernetes GKE</td><td>Skapa AKS-kluster</td><td>Provisionera Kubernetes-kluster i Azure</td></tr><tr><td>4</td><td>Kubernetes EKS</td><td>Skapa Kubernetes Manifests</td><td>Skriv YAML-filer (deployment, service, secret, etc.)</td></tr><tr><td>5</td><td>Deploy Todo App / Kustomize</td><td>Deploya till AKS</td><td>Använd kubectl apply för manuell deploy</td></tr><tr><td>6</td><td>Ingress</td><td>Installera Nginx Ingress</td><td>Exponera appen externt via ingress.yaml</td></tr><tr><td>7</td><td>ArgoCD</td><td>Aktivera GitOps med ArgoCD</td><td>Installera ArgoCD, anslut repo, automatisk synk</td></tr></tbody></table>
