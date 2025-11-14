# Skapa AKS Kluster

## 3. Skapa AKS-kluster

Jag började att manuellt sätta upp detta i portalen men insåg snabbt att det skulle ta tid, speciellt då jag inte var helt 100% på vad jag behövde fylla i.

Så jag gick över till CLI istället för att skapa ett kluster.

### 3.1 Kommando

```bash
az aks create \
  --resource-group mysvampsidaRG \
  --name myAKSCluster \
  --node-count 1 \
  --generate-ssh-keys \
  --attach-acr svampapp
```

**Observera:** Jag tog bort `--enable-addons monitoring` eftersom Azure for Students-subscriptionen blockerade monitoring-funktionen.

### 3.2 Vad kommandot gör

* Skapar ett Kubernetes-kluster med **1 node** (server)
* Genererar **SSH-nycklar** automatiskt
* **Kopplar ACR** (så AKS kan hämta min Docker image!)

### 3.3 Resultat från skapandet

**Viktiga detaljer från output:**

* **Status:** `"provisioningState": "Succeeded"` ✅
* **Kubernetes version:** 1.32.7
* **Node pool:** 1 node (Standard\_DS2\_v2, Ubuntu)
* **Location:** West Europe
* **ACR kopplad:** Via attach-acr parametern

### 3.4 Ansluta till AKS med kubectl

**Vad är kubectl?**

kubectl är kommandoradsverktyget för att styra Kubernetes-kluster. Med kubectl kan jag:

* ✅ Starta appar (deployments)
* ✅ Se vad som körs (pods)
* ✅ Stoppa/starta saker
* ✅ Felsöka problem

**Anslutning:**

```bash
az aks get-credentials --resource-group mysvampsidaRG --name myAKSCluster
```

Detta kommando konfigurerar kubectl att prata med mitt AKS-kluster.

### 3.5 Verifiering

**Kommando:**

```bash
kubectl get nodes
```

**Resultat:**

```
NAME                                STATUS   ROLES    AGE   VERSION
aks-nodepool1-14013471-vmss000000   Ready    <none>   26m   v1.32.7
```

✅ **Ett fungerande Kubernetes-kluster med 1 node i status "Ready"!**

