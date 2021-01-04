---
title: Aufgaben nach der Bereitstellung von OpenShift Container Platform 3.11 in Azure
description: Zusätzliche Aufgaben nach der Bereitstellung eines OpenShift Container Platform 3.11-Clusters.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: dd967ad08b628f9073edfe548033f7e97845d047
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016062"
---
# <a name="post-deployment-tasks"></a>Aufgaben nach der Bereitstellung

Nach der Bereitstellung eines OpenShift-Clusters können Sie zusätzliche Elemente konfigurieren. In diesem Artikel wird Folgendes behandelt:

- Konfigurieren des einmaligen Anmeldens mit Azure Active Directory (Azure AD)
- Konfigurieren von Azure Monitor-Protokollen zum Überwachen von OpenShift
- Konfigurieren von Metriken und Protokollierung
- Installieren von Open Service Broker für Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurieren des einmaligen Anmeldens mit Azure Active Directory

Um Azure Active Directory für die Authentifizierung zu verwenden, müssen Sie zuerst eine Azure AD-App-Registrierung erstellen. Dieser Prozess umfasst zwei Schritte: das Erstellen der App-Registrierung und das Konfigurieren von Berechtigungen.

### <a name="create-an-app-registration"></a>Erstellen einer App-Registrierung

Für diese Schritte wird die Azure-Befehlszeilenschnittstelle verwendet, um die App-Registrierung zu erstellen, und die grafische Benutzeroberfläche (das Portal), um die Berechtigungen festzulegen. Für die Erstellung der App-Registrierung werden die folgenden fünf Angaben benötigt:

- Anzeigename: Der Name der App-Registrierung (z.B. OCPAzureAD)
- Startseite: OpenShift-Konsolen-URL (z. B. `https://masterdns343khhde.westus.cloudapp.azure.com/console`)
- Bezeichner-URI: OpenShift-Konsolen-URL (z. B. `https://masterdns343khhde.westus.cloudapp.azure.com/console`)
- Antwort-URL: Öffentliche Master-URL und Name der App-Registrierung (z. B. `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD`)
- Password (Kennwort): Sicheres Kennwort (verwenden Sie ein sicheres Kennwort)

Im folgenden Beispiel wird eine App-Registrierung mit den obigen Informationen erstellt:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Nach erfolgreicher Befehlsausführung erhalten Sie eine JSON-Ausgabe wie die folgende:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Notieren Sie sich die zurückgegebene appId-Eigenschaft zur späteren Verwendung.

Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Klicken Sie auf **Azure Active Directory** > **App-Registrierung**.
2. Suchen Sie nach Ihrer App-Registrierung. (z. B. OCPAzureAD).
3. Klicken Sie in den Ergebnissen auf die App-Registrierung.
4. Klicken Sie unter **Einstellungen** auf **Erforderliche Berechtigungen**.
5. Wählen Sie unter **Erforderliche Berechtigungen** die Option **Hinzufügen** aus.

   ![App-Registrierung](media/openshift-post-deployment/app-registration.png)

6. Klicken Sie auf „Schritt 1: API auswählen“ und dann auf **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)** . Klicken Sie im unteren Bereich auf **Auswählen**.

   ![App-Registrierung: API auswählen](media/openshift-post-deployment/app-registration-select-api.png)

7. Wählen Sie in „Schritt 2: Berechtigungen auswählen“ unter **Delegierte Berechtigungen** die Berechtigung **Anmelden und Benutzerprofil lesen** aus, und klicken Sie anschließend auf **Auswählen**.

   ![App-Registrierung: Zugriff](media/openshift-post-deployment/app-registration-access.png)

8. Wählen Sie **Fertig** aus.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurieren von OpenShift für die Azure AD-Authentifizierung

Wenn Sie OpenShift für die Verwendung von Azure AD als Authentifizierungsanbieter konfigurieren möchten, muss die Datei „/etc/origin/master/master-config.yaml“ auf allen Masterknoten bearbeitet werden.

Die Mandanten-ID können Sie mit dem folgenden CLI-Befehl ermitteln:

```azurecli
az account show
```

Suchen Sie in der YAML-Datei nach folgenden Zeilen:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Fügen Sie unmittelbar nach den obigen Zeilen die folgenden Zeilen ein:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Stellen Sie sicher, dass der Text unter „identityProviders“ korrekt ausgerichtet ist. Die Mandanten-ID können Sie mit dem folgenden CLI-Befehl ermitteln: ```az account show```

Starten Sie die OpenShift-Masterdienste auf allen Masterknoten neu:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

In der OpenShift-Konsole werden nun zwei Authentifizierungsoptionen angezeigt: „htpasswd_auth“ und [App-Registrierung].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Überwachen von OpenShift mit Azure Monitor-Protokollen

Zum Hinzufügen des Log Analytics-Agents zu OpenShift stehen drei Möglichkeiten zur Verfügung.
- Installieren des Log Analytics-Agents für Linux direkt auf jedem OpenShift-Knoten
- Aktivieren der Azure Monitor-VM-Erweiterung auf jedem OpenShift-Knoten
- Installieren des Log Analytics-Agents als OpenShift-Daemongruppe

Ausführlichere Informationen finden Sie in den vollständigen [Anweisungen](../../azure-monitor/insights/containers.md#configure-a-log-analytics-agent-for-red-hat-openshift).

## <a name="configure-metrics-and-logging"></a>Konfigurieren von Metriken und Protokollierung

Basierend auf dem Branch können die Azure Resource Manager-Vorlagen für OpenShift Container Platform und OKD als Teil der Installation Eingabeparameter für die Aktivierung von Metriken und die Protokollierung enthalten.

Das Marketplace-Angebot für OpenShift Container Platform umfasst zudem eine Option für die Aktivierung von Metriken und der Protokollierung während der Clusterinstallation.

Wenn Metriken und die Protokollierung während der Installation des Clusters nicht aktiviert wurden, können sie später auf einfache Weise aktiviert werden.

### <a name="azure-cloud-provider-in-use"></a>Mit Azure-Cloudanbieter

Stellen Sie eine SSH-Verbindung mit dem Bastion-Knoten oder dem ersten Masterknoten her (basierend auf der verwendeten Vorlage und dem Branch), und verwenden Sie dazu die während der Bereitstellung angegebenen Anmeldeinformationen. Führen Sie den folgenden Befehl aus:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Ohne Azure-Cloudanbieter

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Installieren von Open Service Broker für Azure (OSBA)

Mit Open Service Broker für Azure (OSBA) können Sie Azure Cloud Services direkt über OpenShift bereitstellen. OSBA ist eine Open Service Broker-API-Implementierung für Azure. Die Open Service Broker-API ist eine Spezifikation einer gemeinsamen Sprache für Cloudanbieter, die cloudbasierte Anwendungen zur Verwaltung von Clouddiensten unabhängig verwenden können.

Anweisungen zum Installieren von OSBA auf OpenShift finden Sie hier: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 
> [!NOTE]
> Führen Sie nur die Schritte im Abschnitt zur OpenShift-Projektvorlage aus und nicht die Schritte des gesamten Abschnitts zur Installation.

## <a name="next-steps"></a>Nächste Schritte

- [Getting Started with OpenShift Container Platform](https://docs.openshift.com) (Erste Schritte mit OpenShift Container Platform)
