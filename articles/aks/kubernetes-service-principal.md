---
title: Dienstprinzipale für Azure Kubernetes Service (AKS)
description: Erstellen und Verwalten eines Azure Active Directory-Dienstprinzipals für einen Cluster im Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: e95eae3ab8d992bc169e54700e7e31715e72102e
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607822"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Dienstprinzipale mit Azure Kubernetes Service (AKS)

Für die Interaktion mit Azure-APIs benötigt ein AKS-Cluster einen [Azure AD-Dienstprinzipal (Active Directory)][aad-service-principal] oder eine [verwaltete Identität](use-managed-identity.md). Der Dienstprinzipal bzw. die verwaltete Identität wird für die dynamische Erstellung und Verwaltung anderer Azure-Ressourcen wie Azure Load Balancer oder Azure Container Registry (ACR) benötigt.

Dieser Artikel veranschaulicht das Erstellen und Verwenden eines Dienstprinzipals für Ihre AKS-Cluster.

## <a name="before-you-begin"></a>Voraussetzungen

Für die Erstellung eines Azure AD-Dienstprinzipals müssen Sie dazu berechtigt sein, eine Anwendung bei Ihrem Azure AD-Mandanten zu registrieren und die Anwendung einer Rolle in Ihrem Abonnement zuzuweisen. Sollten Sie nicht über die erforderlichen Berechtigungen verfügen, können Sie ggf. Ihren Azure AD- oder Abonnementadministrator bitten, Ihnen die erforderlichen Berechtigungen zuzuweisen oder vorab einen Dienstprinzipal für die Verwendung mit dem AKS-Cluster für Sie zu erstellen.

Wenn Sie einen Dienstprinzipal eines anderen Azure AD-Mandanten verwenden, ergeben sich zusätzliche Überlegungen zu den nach der Bereitstellung des Clusters verfügbaren Berechtigungen. Möglicherweise haben Sie nicht die entsprechenden Berechtigungen zum Lesen und Schreiben von Verzeichnisinformationen. Weitere Informationen finden Sie unter [Welche Standardbenutzerberechtigungen gibt es in Azure Active Directory?][azure-ad-permissions].

Außerdem muss mindestens die Version 2.0.59 der Azure CLI installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Automatisches Erstellen und Verwenden eines Dienstprinzipals

Beim Erstellen eines AKS-Clusters im Azure-Portal oder mithilfe des Befehls [az aks create][az-aks-create] kann Azure automatisch einen Dienstprinzipal generieren.

Im folgenden Azure CLI-Beispiel wird kein Dienstprinzipal angegeben. In diesem Szenario erstellt die Azure-Befehlszeilenschnittstelle einen Dienstprinzipal für den AKS-Cluster. Für den erfolgreichen Abschluss dieses Vorgangs muss Ihr Azure-Konto über die erforderlichen Rechte zum Erstellen eines Dienstprinzipals verfügen.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Manuelles Erstellen eines Dienstprinzipals

Verwenden Sie den Befehl [az ad sp create-for-rbac][az-ad-sp-create], um den Dienstprinzipal manuell über die Azure-Befehlszeilenschnittstelle zu erstellen. Im folgenden Beispiel verhindert der `--skip-assignment`-Parameter, dass zusätzliche Standardzuweisungen durchgeführt werden:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus: Notieren Sie sich Ihre persönlichen Angaben für `appId` und `password`. Diese Werte werden verwendet, wenn Sie im nächsten Abschnitt einen AKS-Cluster erstellen.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Angeben eines Dienstprinzipals für einen AKS-Cluster

Um bei der Erstellung eines AKS-Clusters mithilfe des Befehls [az aks create][az-aks-create] einen vorhandenen Dienstprinzipal zu verwenden, geben Sie mithilfe der Parameter `--service-principal` und `--client-secret` die App-ID (`appId`) und das Kennwort (`password`) aus der Ausgabe des Befehls [az ad sp create-for-rbac][az-ad-sp-create] an:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Wenn Sie einen bestehenden Dienstprinzipal mit benutzerdefiniertem Geheimnis verwenden, stellen Sie sicher, dass das Geheimnis nicht länger als 190 Bytes ist.

Wenn Sie einen AKS-Cluster über das Azure-Portal bereitstellen, wählen Sie auf der Seite *Authentication (Authentifizierung)* des **Create Kubernetes cluster (Kubernetes-Cluster erstellen)** -Dialogfelds **Configure service principal (Dienstprinzipal konfigurieren)** aus. Wählen Sie **Use existing (Vorhandene verwenden)** aus, und geben Sie die folgenden Werte an:

- **Service principal client ID (Client-ID des Dienstprinzipals)** ist Ihre *appId*.
- **Service principal client secret (Clientgeheimnis des Dienstprinzipals)** ist der Wert *password*.

![Abbildung der Navigation zu Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegieren des Zugriffs auf andere Azure-Ressourcen

Der Dienstprinzipal für den AKS-Cluster kann verwendet werden, um auf andere Ressourcen zuzugreifen. Wenn Sie beispielsweise Ihren AKS-Cluster in ein bestehendes Subnetz eines virtuellen Azure-Netzwerks einbinden oder eine Verbindung mit Azure Container Registry (ACR) herstellen möchten, müssen Sie den Zugriff auf diese Ressourcen an den Dienstprinzipal delegieren.

Um Berechtigungen zu delegieren, erstellen Sie mit dem Befehl [az role assignment create][az-role-assignment-create] eine Rollenzuweisung. Weisen Sie `appId` einem bestimmten Bereich zu, beispielsweise einer Ressourcengruppe oder einer virtuellen Netzwerkressource. Eine Rolle definiert dann wie im folgenden Beispiel gezeigt die Berechtigungen, die der Dienstprinzipal für diese Ressource besitzt:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

Bei dem Bereich (`--scope`) für eine Ressource muss es sich um eine vollständige Ressourcen-ID handeln (beispielsweise */subscriptions/\<guid\>/resourceGroups/myResourceGroup* oder */subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*).

> [!NOTE]
> Wenn Sie die Rollenzuweisung „Mitwirkender“ aus der Knotenressourcengruppe entfernt haben, können die folgenden Vorgänge möglicherweise nicht erfolgreich ausgeführt werden.  

In den folgenden Abschnitten werden allgemeine Delegierungen, die Sie möglicherweise vornehmen müssen, ausführlicher erläutert.

### <a name="azure-container-registry"></a>Azure Container Registry

Wenn Sie Azure Container Registry (ACR) als Speicher für Containerimages verwenden, müssen Sie dem Dienstprinzipal für Ihren AKS-Cluster Berechtigungen zum Lesen und Pullen von Images erteilen. Aktuell wird die folgende Konfiguration empfohlen: Verwenden Sie den Befehl [az aks create][az-aks-create] bzw. [az aks update][az-aks-update], um eine Registrierungsintegration vorzunehmen und die entsprechende Rolle für den Dienstprinzipal zuzuweisen. Eine ausführliche Anleitung finden Sie unter [Authentifizieren per Azure Container Registry über Azure Kubernetes Service][aks-to-acr].

### <a name="networking"></a>Netzwerk

Möglicherweise verwenden Sie erweiterte Netzwerke, in denen sich das virtuelle Netzwerk und das Subnetz oder öffentliche IP-Adressen in einer anderen Ressourcengruppe befinden. Weisen Sie eine der folgenden Gruppen von Rollenberechtigungen zu:

- Erstellen Sie eine [benutzerdefinierte Rolle][rbac-custom-role], und definieren Sie die folgenden Rollenberechtigungen:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/publicIPAddresses/join/action*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
  - Fügen Sie bei Verwendung [benutzerdefinierter Routingtabellen in Kubenet-Clustern](configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) die folgenden Zusatzberechtigungen hinzu:
    - *Microsoft.Network/routeTables/write*
    - *Microsoft.Network/routeTables/read*
- Alternativ können Sie die integrierte Rolle [Netzwerkmitwirkender][rbac-network-contributor] für das Subnetz im virtuellen Netzwerk zuweisen.

### <a name="storage"></a>Storage

Möglicherweise müssen Sie auf vorhandene Datenträgerressourcen in einer anderen Ressourcengruppe zugreifen. Weisen Sie eine der folgenden Gruppen von Rollenberechtigungen zu:

- Erstellen Sie eine [benutzerdefinierte Rolle][rbac-custom-role], und definieren Sie die folgenden Rollenberechtigungen:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Alternativ können Sie die integrierte Rolle [Speicherkontomitwirkender][rbac-storage-contributor] für die Ressourcengruppe zuweisen.

### <a name="azure-container-instances"></a>Azure Container Instances

Wenn Sie Virtual Kubelet für die Integration in AKS verwenden und Azure Container Instances (ACI) in einer Ressourcengruppe außerhalb des AKS-Clusters ausführen, muss dem AKS-Dienstprinzipal in der ACI-Ressourcengruppe die Berechtigung *Mitwirkender* gewährt werden.

## <a name="additional-considerations"></a>Weitere Überlegungen

Beachten Sie bei Verwendung von AKS und Azure AD-Dienstprinzipalen die folgenden Kriterien.

- Der Dienstprinzipal für Kubernetes ist Bestandteil der Clusterkonfiguration. Verwenden Sie diese Identität jedoch nicht zum Bereitstellen des Clusters.
- Standardmäßig sind die Anmeldeinformationen des Dienstprinzipals ein Jahr gültig. Sie können jederzeit die [Anmeldeinformationen des Dienstprinzipals aktualisieren oder rotieren][update-credentials].
- Jeder Dienstprinzipal ist einer Azure AD-Anwendung zugeordnet. Der Dienstprinzipal für einen Kubernetes-Cluster kann einem beliebigen gültigen Azure AD-Anwendungsnamen zugeordnet sein (z.B. *https://www.contoso.org/example* ). Bei der URL für die Anwendung muss es sich nicht um einen realen Endpunkt handeln.
- Geben Sie als **Client-ID** des Dienstprinzipals den `appId`-Wert an.
- Auf den Agent-Knoten-VMs im Kubernetes-Cluster werden die Anmeldeinformationen des Dienstprinzipals in der Datei `/etc/kubernetes/azure.json` gespeichert.
- Wenn Sie den Dienstprinzipal mithilfe des Befehls [az aks create][az-aks-create] automatisch generieren, werden die Dienstprinzipal-Anmeldeinformationen auf dem Computer, auf dem der Befehl ausgeführt wird, in die Datei `~/.azure/aksServicePrincipal.json` geschrieben.
- Wenn Sie keinen Dienstprinzipal explizit in zusätzlichen AKS-CLI-Befehlen übergeben, wird der Standarddienstprinzipal verwendet, der sich unter `~/.azure/aksServicePrincipal.json` befindet.  
- Optional können Sie auch die Datei „aksServicePrincipal.json“ entfernen, woraufhin AKS einen neuen Dienstprinzipal erstellt.
- Beim Löschen eines AKS-Clusters, der mit [az aks create][az-aks-create] erstellt wurde, wird der automatisch erstellte Dienstprinzipal nicht gelöscht.
    - Fragen Sie zum Löschen des Dienstprinzipals Ihren Cluster *servicePrincipalProfile.clientId* ab, und verwenden Sie [az ad app delete][az-ad-app-delete], um ihn zu löschen. Ersetzen Sie die folgenden Namen für Ressourcengruppe und Cluster durch Ihre eigenen Werte:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Problembehandlung

Die Dienstprinzipal-Anmeldeinformationen für einen AKS-Cluster werden von der Azure CLI zwischengespeichert. Wenn diese Anmeldeinformationen abgelaufen sind, treten Fehler bei der Bereitstellung des AKS-Clusters auf. Sollte während der Ausführung von [az aks create][az-aks-create] die folgende Fehlermeldung angezeigt werden, kann dies auf ein Problem mit den zwischengespeicherten Anmeldeinformationen des Dienstprinzipals hindeuten:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Überprüfen Sie das Alter der Anmeldeinformationsdatei mithilfe des folgenden Befehls:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

Die Standardablaufzeit für die Anmeldeinformationen des Dienstprinzipals beträgt ein Jahr. Wenn Ihre Datei *aksServicePrincipal.json* älter als ein Jahr ist, löschen Sie die Datei, und versuchen Sie, erneut einen AKS-Cluster bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Active Directory-Dienstprinzipalen finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory][service-principal].

Informationen zum Aktualisieren der Anmeldeinformationen finden Sie unter [Aktualisieren oder Rotieren der Anmeldeinformationen für einen Dienstprinzipal in Azure Kubernetes Service (AKS)][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
