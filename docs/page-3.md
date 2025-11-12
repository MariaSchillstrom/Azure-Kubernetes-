# Page 3

### Del 3: Skapa AKS Kluster

Jag började att manuellt sätta upp detta i portalen men insåg snabbt att det skulle ta tid, speciellt då jag inte var helt 100 på vad jag behövde fylla i.&#x20;

Så jag gick över till CLI istället för att skapa ett kluster

```bash
az aks create \
  --resource-group mysvampsidaRG \
  --name myAKSCluster \
  --node-count 1 \
  --generate-ssh-keys \
  --attach-acr svampapp
```

#### **Vad gör vi**&#x20;

* Skapar ett Kubernetes-kluster med **1 node** (server)
* Aktiverar monitoring (så jag ser vad som händer)
* Genererar SSH-nycklar automatiskt
* **Kopplar ACR** (så AKS kan hämta min image!)

#### **Reslutat**

```
AAD role propagation done[############################################]  100.0000%{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "availabilityZones": null,
      "capacityReservationGroupId": null,
      "count": 1,
      "creationData": null,
      "currentOrchestratorVersion": "1.32.7",
      "eTag": null,
      "enableAutoScaling": false,
      "enableEncryptionAtHost": false,
      "enableFips": false,
      "enableNodePublicIp": false,
      "enableUltraSsd": false,
      "gpuInstanceProfile": null,
      "hostGroupId": null,
      "kubeletConfig": null,
      "kubeletDiskType": "OS",
      "linuxOsConfig": null,
      "maxCount": null,
      "maxPods": 250,
      "minCount": null,
      "mode": "System",
      "name": "nodepool1",
      "networkProfile": null,
      "nodeImageVersion": "AKSUbuntu-2204gen2containerd-202510.19.1",
      "nodeLabels": null,
      "nodePublicIpPrefixId": null,
      "nodeTaints": null,
      "orchestratorVersion": "1.32",
      "osDiskSizeGb": 128,
      "osDiskType": "Managed",
      "osSku": "Ubuntu",
      "osType": "Linux",
      "podSubnetId": null,
      "powerState": {
        "code": "Running"
      },
      "provisioningState": "Succeeded",
      "proximityPlacementGroupId": null,
      "scaleDownMode": "Delete",
      "scaleSetEvictionPolicy": null,
      "scaleSetPriority": null,
      "securityProfile": {
        "enableSecureBoot": false,
        "enableVtpm": false
      },
      "spotMaxPrice": null,
      "tags": null,
      "type": "VirtualMachineScaleSets",
      "upgradeSettings": {
        "drainTimeoutInMinutes": null,
        "maxSurge": "10%",
        "nodeSoakDurationInMinutes": null
      },
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null,
      "windowsProfile": null,
      "workloadRuntime": null
    }
  ],
  "apiServerAccessProfile": null,
  "autoScalerProfile": null,
  "autoUpgradeProfile": {
    "nodeOsUpgradeChannel": "NodeImage",
    "upgradeChannel": null
  },
  "azureMonitorProfile": null,
  "azurePortalFqdn": "myaksclust-mysvampsidarg-456993-l4eikpy1.portal.hcp.westeurope.azmk8s.io",
  "currentKubernetesVersion": "1.32.7",
  "disableLocalAccounts": false,
  "diskEncryptionSetId": null,
  "dnsPrefix": "myAKSClust-mysvampsidaRG-456993",
  "eTag": null,
  "enablePodSecurityPolicy": null,
  "enableRbac": true,
  "extendedLocation": null,
  "fqdn": "myaksclust-mysvampsidarg-456993-l4eikpy1.hcp.westeurope.azmk8s.io",
  "fqdnSubdomain": null,
  "httpProxyConfig": null,
  "id": "/subscriptions/456993d6-92bf-47d7-9310-25a1e3381be4/resourcegroups/mysvampsidaRG/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "identity": {
    "delegatedResources": null,
    "principalId": "3da407d4-97f9-4722-bd2e-b8249b4b34f1",
    "tenantId": "67f216cd-693d-4de9-8fc8-17089f128b95",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "identityProfile": {
    "kubeletidentity": {
      "clientId": "11d492d2-c7e3-4a10-9e3b-6a12eccda374",
      "objectId": "a1ae273d-02a6-49e3-b106-168bc3a8a037",
      "resourceId": "/subscriptions/456993d6-92bf-47d7-9310-25a1e3381be4/resourcegroups/MC_mysvampsidaRG_myAKSCluster_westeurope/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myAKSCluster-agentpool"
    }
  },
  "ingressProfile": null,
  "kubernetesVersion": "1.32",
  "linuxProfile": {
    "adminUsername": "azureuser",
    "ssh": {
      "publicKeys": [
        {
          "keyData": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDv4A/2WKIFXYcmQAGmu+zmZ/4hZ2LkLdJSUGbGgH0AUopID4sWL8vFbWWqKOV1f2G0agQRJ3XdUOFAvFGFCDyA/R46La2BqbSftXQg9zoFokCm5hWviP2aV5kLmwgzQm5YpjH4wNpKrDmsFSun2INhS2klLEg8clnkRQgzO4WHZahubd0ytGczX//u4Y0DvYJgLbByWf2rReJSGaExLsJadEHtMADGbuU3zM6h2sDCMeWABXRgVmIuL7NE/FQhxdQ4VXFGwBC9/Kv0HsHw/Qq1AzkWAxmWt4iamIp4hKlB8VUbRaUUos5gVbknM9ddcgGgtc4RR17sM1enakHCOGoMUO7DAl8Locqh2xShKuAqtEOGe9sbFD1vFQpa6xsoipGf8UjTUQ5Qqc06OhcPPb96PuQpER8W6GMUg+zVI92Vz2ZhkLj1uXBSD2tXoDqsA06ItKZYHeD8uAqfsL/0DoTuHq3Q7IfoxRZNnNW4e7/5mVgREGH/t8jRuXBoX/dPOnjHhCtHEWlJyhxbFJEP1TOXrk5tbxAyIXfeCEHKW/rsgUTZwCHTsNQheZy/UNmEjiw6aphT+yxKTJpMxJzotzE0WMqftL4zy1/hLihlQNXqXG5tGxF/6BEQVkBC75k4j31kaFiCxiUCIo+mVSP21h9qutl0cOjcfut5iI3mF+tM0Q== mariaschillstrom@hotmail.com\n"
        }
      ]
    }
  },
  "location": "westeurope",
  "maxAgentPools": 100,
  "metricsProfile": {
    "costAnalysis": {
      "enabled": false
    }
  },
  "name": "myAKSCluster",
  "networkProfile": {
    "advancedNetworking": null,
    "dnsServiceIp": "10.0.0.10",
    "ipFamilies": [
      "IPv4"
    ],
    "loadBalancerProfile": {
      "allocatedOutboundPorts": null,
      "backendPoolType": "nodeIPConfiguration",
      "effectiveOutboundIPs": [
        {
          "id": "/subscriptions/456993d6-92bf-47d7-9310-25a1e3381be4/resourceGroups/MC_mysvampsidaRG_myAKSCluster_westeurope/providers/Microsoft.Network/publicIPAddresses/d2bbfb89-1048-473e-9b84-76dcf315e5dc",
          "resourceGroup": "MC_mysvampsidaRG_myAKSCluster_westeurope"
        }
      ],
      "enableMultipleStandardLoadBalancers": null,
      "idleTimeoutInMinutes": null,
      "managedOutboundIPs": {
        "count": 1,
        "countIpv6": null
      },
      "outboundIPs": null,
      "outboundIpPrefixes": null
    },
    "loadBalancerSku": "standard",
    "natGatewayProfile": null,
    "networkDataplane": "azure",
    "networkMode": null,
    "networkPlugin": "azure",
    "networkPluginMode": "overlay",
    "networkPolicy": "none",
    "outboundType": "loadBalancer",
    "podCidr": "10.244.0.0/16",
    "podCidrs": [
      "10.244.0.0/16"
    ],
    "serviceCidr": "10.0.0.0/16",
    "serviceCidrs": [
      "10.0.0.0/16"
    ]
  },
  "nodeResourceGroup": "MC_mysvampsidaRG_myAKSCluster_westeurope",
  "nodeResourceGroupProfile": null,
  "oidcIssuerProfile": {
    "enabled": false,
    "issuerUrl": null
  },
  "podIdentityProfile": null,
  "powerState": {
    "code": "Running"
  },
  "privateFqdn": null,
  "privateLinkResources": null,
  "provisioningState": "Succeeded",
  "publicNetworkAccess": null,
  "resourceGroup": "mysvampsidaRG",
  "resourceUid": "691499fdc4fb3d0001da5271",
  "securityProfile": {
    "azureKeyVaultKms": null,
    "defender": null,
    "imageCleaner": null,
    "workloadIdentity": null
  },
  "serviceMeshProfile": null,
  "servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  },
  "sku": {
    "name": "Base",
    "tier": "Free"
  },
  "storageProfile": {
    "blobCsiDriver": null,
    "diskCsiDriver": {
      "enabled": true
    },
    "fileCsiDriver": {
      "enabled": true
    },
    "snapshotController": {
      "enabled": true
    }
  },
  "supportPlan": "KubernetesOfficial",
  "systemData": null,
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters",
  "upgradeSettings": null,
  "windowsProfile": null,
  "workloadAutoScalerProfile": {
    "keda": null,
    "verticalPodAutoscaler": null
  }
}
```

### Ansluta till AKS med kubectl

#### **Varfär;**&#x20;

* [x] Starta appar
* [x] Se vad som körs&#x20;
* [x] Stoppa/starta saker
* [x] Felsäk

#### **Kör**

```
az aks get-credentials --resource-group mysvampsidaRG --name myAKSCluster
```

#### Verifiera

```
kubectl get nodes
```

#### Resultat&#x20;

```
NAME                                STATUS   ROLES    AGE   VERSION
aks-nodepool1-14013471-vmss000000   Ready    <none>   26m   v1.32.7
```

