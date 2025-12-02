---
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
---

# Innehållsförteckning

**1. Lokal utveckling (MongoDB Todo App Development)**

* 1.1 Applikationsbeskrivning
* 1.2 Filstruktur
* 1.3 Funktionalitet
* 1.4 Åtkomst
* 1.5 Frontend-struktur
* 1.6 Fördelar med denna separation
* 1.7 Bilder

**2. Push till ACR**

* 2.1 Flöde
* 2.2 Steg som utfördes
* 2.3 Problem: ACR Tasks blockerat
* 2.4 Resultat

**3. Skapa AKS-kluster**

* 3.1 Kommando
* 3.2 Vad kommandot gör
* 3.3 Resultat
* 3.4 Ansluta till AKS med kubectl
* 3.5 Verifiering

**4. Kunernetes manifests**

* 4.1 Analogi
* 4.2 För Svampappen
  * 4.2.1 Deployment (svampapp-deployment.yaml)
  * 4.2.2 Service (svampapp-service.yaml)
  * 4.2.3 Persistent Volume Claim (mongodb-pvc.yaml)

**5. Deploy till AKS**

* 5.1 Applicera manifests
* 5.2 Verifiera deployment
* 5.3 Hämta extern IP-adress
* 5.4 Testa applikationen

**6. Nginx Ingress Controller**

* 6.1 Installera Nginx Ingress Controller
* 6.2 Vänta tills det är klart
* 6.3 Kontrollera att Ingress controller har en extern IP
* 6.4 Skapa Ingress-regel
* 6.5 Ändra svampapp-service till ClusterIP
* 6.6 Applicera ändringarna
* 6.7 Testa sidan

**7. ArgoCD (GitOps)**

* 7.1 Installera ArgoCD
* 7.2 Exponera ArgoCD
* 7.3 Logga in
* 7.4 Skapa Application
* 7.5 Synka applikationen
* 7.6 Testa automatisk deployment
* 7.7 Fördelar med GitOps

**8. GitHub Actions**

* 8.1 Skapa Azure Service Principal
* 8.2 Lägg till GitHub Secrets
* 8.3 Skapa GitHub Actions Workflow
* 8.4 Verifiera Github Actions
* 8.5 Uppdatera Deployment för CI/CD
* 8.6 Testa hela CI/CD flödet&#x20;
* 8.7 Sammanfattning

**9. Förbättringsmöjligheter**

**10. Länkar till slutgiltiga filer**

&#x20;**11.Resuser-websidor samt LLM**

