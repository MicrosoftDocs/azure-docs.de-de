---
title: 'Tutorial: Erstellen eines Azure Red Hat OpenShift-Clusters | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie einen Microsoft Azure Red Hat OpenShift-Cluster mithilfe der Azure-Befehlszeilenschnittstelle erstellen
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/14/2019
ms.openlocfilehash: 651236c25ed912ebd7399d351677a67e3826278c
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306185"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Tutorial: Erstellen eines Azure Red Hat OpenShift-Clusters

Dieses Tutorial ist der erste Teil einer Serie. Sie erfahren, wie Sie einen Microsoft Azure Red Hat OpenShift-Cluster mithilfe der Azure-Befehlszeilenschnittstelle erstellen, skalieren und anschließend löschen, um die Ressourcen zu bereinigen.

Im ersten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Azure Red Hat OpenShift-Clusters

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen eines Azure Red Hat OpenShift-Clusters
> * [Skalieren eines Azure Red Hat OpenShift-Clusters](tutorial-scale-cluster.md)
> * [Löschen eines Azure Red Hat OpenShift-Clusters](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Voraussetzungen

> [!IMPORTANT]
> Für dieses Tutorial ist die Azure CLI-Version 2.0.65 erforderlich.
>    
> Bevor Sie Azure Red Hat OpenShift verwenden können, müssen Sie mindestens 4 reservierte Azure Red Hat OpenShift-Anwendungsknoten gekauft haben, wie in [Einrichten Ihrer Azure Red Hat OpenShift-Entwicklungsumgebung](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances) beschrieben.

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

Vergewissern Sie sich, dass [Ihre Entwicklungsumgebung eingerichtet](howto-setup-environment.md) ist. Dies umfasst:
- Installieren der aktuellen CLI (Version 2.0.65 oder höher)
- Erstellen eines Mandanten, wenn Sie noch keinen besitzen
- Erstellen eines Azure-Anwendungsobjekts, wenn Sie noch keins besitzen
- Erstellen einer Sicherheitsgruppe
- Erstellen eines Active Directory-Benutzers, zum Anmelden beim Cluster.

## <a name="step-1-sign-in-to-azure"></a>Schritt 1: Anmelden bei Azure

Wenn Sie die Azure-Befehlszeilenschnittstelle lokal ausführen, öffnen Sie eine Bash-Befehlsshell, und führen Sie `az login` aus, um sich bei Azure anzumelden.

```bash
az login
```

 Wenn Sie Zugriff auf mehrere Abonnements haben, führen Sie `az account set -s {subscription ID}` aus, und ersetzen Sie `{subscription ID}` durch das zu verwendende Abonnement.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Schritt 2: Erstellen eines Azure Red Hat OpenShift-Clusters

Legen Sie in einem Bash-Befehlsfenster die folgenden Variablen fest:

> [!IMPORTANT]
> Wählen Sie einen Namen für Ihren Cluster aus, der eindeutig ist und nur aus Kleinbuchstaben besteht, da andernfalls die Clustererstellung fehlschlägt.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Wählen Sie einen Speicherort zum Erstellen des Clusters aus. Eine Liste der Azure-Regionen, die OpenShift in Azure unterstützen, finden Sie unter [Unterstützte Regionen](supported-resources.md#azure-regions). Beispiel: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Legen Sie `APPID` auf den Wert fest, den Sie in Schritt 5 von [Erstellen einer Azure AD-App-Registrierung](howto-aad-app-configuration.md#create-an-azure-ad-app-registration) gespeichert haben.  

```bash
APPID=<app ID value>
```

Legen Sie „GROUPID“ auf den Wert fest, den Sie in Schritt 10 von [Erstellen einer Azure AD-Sicherheitsgruppe](howto-aad-app-configuration.md#create-an-azure-ad-security-group) gespeichert haben.

```bash
GROUPID=<group ID value>
```

Legen Sie `SECRET` auf den Wert fest, den Sie in Schritt 8 von [Erstellen eines geheimen Clientschlüssels](howto-aad-app-configuration.md#create-a-client-secret) gespeichert haben.  

```bash
SECRET=<secret value>
```

Legen Sie `TENANT` auf den Wert der Mandanten-ID fest, den Sie in Schritt 7 von [Erstellen eines neuen Mandanten](howto-create-tenant.md#create-a-new-azure-ad-tenant) gespeichert haben.  

```bash
TENANT=<tenant ID>
```

Erstellen Sie die Ressourcengruppe für den Cluster. Führen Sie in derselben Bash-Shell den folgenden Befehl aus, den Sie oben zum Definieren der Variablen verwendet haben:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Optional: Verbinden des virtuellen Netzwerks des Clusters mit einem vorhandenen virtuellen Netzwerk

Wenn Sie keine Peeringverbindung zwischen dem virtuellen Netzwerk (VNET) des von Ihnen erstellten Clusters und einem vorhandenen VNET herstellen müssen, überspringen Sie diesen Schritt.

Rufen Sie zuerst die ID des vorhandenen VNETs ab. Die ID weist diese Form auf: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Wenn Sie den Netzwerknamen oder die Ressourcengruppe des vorhandenen VNETs nicht kennen, wechseln Sie zum Blatt [Virtuelle Netzwerke](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks), und klicken Sie auf Ihr virtuelles Netzwerk. Die Seite mit den virtuellen Netzwerken wird angezeigt und listet den Namen des Netzwerks und die Ressourcengruppe auf, zu der es gehört.

Definieren Sie eine VNET_ID-Variable mithilfe des folgenden CLI-Befehls in einer BASH-Shell:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Beispiel: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Erstellen des Clusters

Sie sind jetzt bereit, einen Cluster zu erstellen. Mit Folgendem wird der Cluster im angegebenen Azure AD-Mandanten erstellt, das Azure AD-App-Objekt und der geheime Schlüssel, die als Sicherheitsprinzipal verwendet werden sollen, werden angegeben, sowie die Sicherheitsgruppe, die die Mitglieder enthält, die Administratorzugriff auf den Cluster haben.

Wenn Sie **kein**  Peering Ihres Clusters mit einem virtuellen Netzwerk vornehmen, verwenden Sie den folgenden Befehl:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Wenn Sie ein Peering Ihres Clusters mit einem virtuellen Netzwerk **vornehmen**, verwenden Sie den folgenden Befehl, der den Flag `--vnet-peer` hinzufügt:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Wenn Sie eine Fehlermeldung erhalten, dass der Hostname nicht verfügbar ist, liegt das möglicherweise daran, dass der Name Ihres Clusters nicht eindeutig ist. Versuchen Sie, Ihre ursprüngliche App-Registrierung zu löschen und die Schritte in [Erstellen einer neuen App-Registrierung] (howto-aad-app-configuration.md#create-a-new-app-registration) mit einem anderen Clusternamen erneut auszuführen, wobei Sie den Schritt des Erstellens eines neuen Benutzers und einer Sicherheitsgruppe auslassen.

Nach ein paar Minuten wird `az openshift create` abgeschlossen.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Abrufen der Anmelde-URL für Ihren Cluster

Rufen Sie die URL für die Anmeldung bei Ihrem Cluster ab, indem Sie folgenden Befehl ausführen:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Suchen Sie in der Ausgabe nach `publicHostName`, z. B.: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

Die Anmelde-URL für Ihren Cluster ist `https://`, gefolgt von dem Wert `publicHostName`.  Beispiel: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Sie verwenden diesen URI im nächsten Schritt als Teil des Umleitungs-URI für die App-Registrierung.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Schritt 3: Aktualisieren des Umleitungs-URI für die App-Registrierung

Nachdem Sie nun die Anmelde-URL für den Cluster haben, legen Sie den Umleitungs-URI für die App-Registrierung fest:

1. Öffnen Sie das Blatt [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Klicken Sie auf Ihr App-Registrierungsobjekt.
3. Klicken Sie auf **Umleitungs-URI hinzufügen**.
4. Stellen Sie sicher, dass **Typ** gleich **Web** ist, und legen Sie den **UMLEITUNGS-URI** mithilfe des folgenden Musters fest: `https://<public host name>/oauth2callback/Azure%20AD`. Beispiel: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Klicken Sie unten auf der Seite auf **Speichern**.

## <a name="step-4-sign-in-to-the-openshift-console"></a>Schritt 4: Anmelden bei der OpenShift-Konsole

Sie sind jetzt bereit, sich bei der OpenShift-Konsole für Ihren neuen Cluster anzumelden. Die [OpenShift-Webkonsole](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) ermöglicht Ihnen das Visualisieren, Durchsuchen und Verwalten der Inhalte Ihrer OpenShift-Projekte.

Sie benötigen eine neue Browserinstanz, in der die Identität, die Sie normalerweise für die Anmeldung beim Azure-Portal verwenden, nicht zwischengespeichert ist.

1. Öffnen Sie ein *Incognito*-Fenster (Chrome) oder ein *InPrivate*-Fenster (Microsoft Edge).
2. Navigieren Sie zu der Anmelde-URL, die Sie weiter oben abgerufen haben, z. B.: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Melden Sie sich mit dem Benutzernamen an, den Sie in Schritt 3 von [Erstellen eines neuen Azure Active Directory-Benutzers](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user) erstellt haben.

Ein Dialogfeld **Berechtigungen erforderlich** wird angezeigt. Klicken Sie auf **Zustimmung im Namen Ihrer Organisation**, und klicken Sie dann auf **Annehmen**.

Sie werden jetzt bei der Clusterkonsole angemeldet.

![Screenshot der OpenShift-Clusterkonsole](./media/aro-console.png)

 In der [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html)-Dokumentation finden Sie weitere Informationen zur [Verwendung der OpenShift-Konsole](https://docs.openshift.com/aro/getting_started/developers_console.html) zum Erstellen von Images.

## <a name="step-5-install-the-openshift-cli"></a>Schritt 5: Installieren der OpenShift-CLI

Die [OpenShift-Befehlszeilenschnittstelle](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (oder *OC Tools*) stellt Befehle zum Verwalten Ihrer Anwendungen und allgemeineren Hilfsprogramme für die Interaktion mit den verschiedenen Komponenten des OpenShift-Clusters bereit.

Klicken Sie in der OpenShift-Konsole auf das Fragezeichen in der oberen rechten Ecke neben Ihrem Anmeldenamen, und wählen Sie **Befehlszeilentools** aus.  Folgen Sie dem Link **Neueste Version**, um die unterstützte oc-Befehlszeilenschnittstelle für Linux, MacOS oder Windows herunterzuladen.

> [!NOTE]
> Wenn Sie das Fragezeichensymbol in der oberen rechten Ecke nicht sehen, wählen Sie in der oberen linken Dropdownliste *Dienstkatalog* oder *Anwendungskonsole* aus.
>
> Alternativ können Sie die [oc-CLI direkt herunterladen](https://www.okd.io/download.html).

Auf der Seite **Befehlszeilentools** steht ein Befehl in der Form `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>` zur Verfügung.  Klicken Sie auf die Schaltfläche *In die Zwischenablage kopieren*, um diesen Befehl zu kopieren.  In einem Terminalfenster können Sie [Ihren Pfad festlegen](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli), um Ihre lokale Installation der oc-Tools einzubinden. Melden Sie sich anschließend mithilfe des kopierten oc-CLI-Befehls beim Cluster an.

Wenn Sie den Tokenwert nicht mithilfe der oben beschriebenen Schritte abrufen konnten, rufen Sie den Tokenwert hier ab: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Azure Red Hat OpenShift-Clusters

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Skalieren eines Azure Red Hat OpenShift-Clusters](tutorial-scale-cluster.md)
