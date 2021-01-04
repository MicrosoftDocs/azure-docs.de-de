---
title: Einrichten von Livedaten (Vorschau) mit Azure Monitor für Container | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die Echtzeitansicht von Containerprotokollen (stdout/stderr) und Ereignissen ohne Verwendung von kubectl mit Azure Monitor für Container einrichten.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: references_regions
ms.openlocfilehash: 45ed931f734e874e81af837fff5c4a326349cb21
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95530181"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Einrichten der Funktion für Livedaten (Vorschau)

Zum Anzeigen von Livedaten (Vorschau) von AKS-Clustern (Azure Kubernetes Service) mit Azure Monitor für Container müssen Sie die Authentifizierung mit Berechtigung für den Zugriff auf Ihre Kubernetes-Daten konfigurieren. Diese Sicherheitskonfiguration ermöglicht den Echtzeitzugriff auf Ihre Daten über die Kubernetes-API direkt im Azure-Portal.

Diese Funktion unterstützt die folgenden Methoden, um den Zugriff auf die Protokolle, Ereignisse und Metriken zu steuern:

- AKS ohne aktivierte Kubernetes RBAC-Autorisierung
- Mit Kubernetes RBAC-Autorisierung aktivierter AKS
    - Mit Clusterrollenbindung **[clusterMonitoringUser](/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0&preserve-view=true)** konfigurierter AKS
- Mit auf SAML basiertem SSO in Azure Active Directory (AD) aktivierter AKS

Für diese Anweisungen ist sowohl Administratorzugriff auf den Kubernetes-Cluster als auch Administratorzugriff auf Azure AD (wenn Azure Active Directory (AD) für die Benutzerauthentifizierung konfiguriert wird) erforderlich.

In diesem Artikel wird erläutert, wie Sie die Authentifizierung konfigurieren, um den Zugriff auf die Funktion für Livedaten (Vorschau) vom Cluster zu steuern:

- Für die rollenbasierte Zugriffssteuerung von Kubernetes (Kubernetes Role-Based Access Control, Kubernetes RBAC) aktivierter AKS-Cluster
- AKS-Cluster mit integriertem Azure Active Directory

>[!NOTE]
>Als [private Cluster](https://azure.microsoft.com/updates/aks-private-cluster/) aktivierte AKS-Cluster werden mit dieser Funktion nicht unterstützt. Diese Funktion basiert auf dem direkten Zugriff auf die Kubernetes-API über einen Proxyserver im Browser. Durch die Aktivierung der Netzwerksicherheit zum Blockieren der Kubernetes-API über den Proxy wird der Datenverkehr blockiert.

## <a name="authentication-model"></a>Authentifizierungsmodell

Die Funktion für Livedaten (Vorschau) verwendet die Kubernetes-API, die dem Befehlszeilentool `kubectl` entspricht. Die Kubernetes-API-Endpunkte verwenden ein selbstsigniertes Zertifikat, das von Ihrem Browser nicht überprüft werden kann. Diese Funktion verwendet einen internen Proxy, um das Zertifikat mit dem AKS-Dienst zu überprüfen und einen vertrauenswürdigen Datenverkehr sicherzustellen.

Im Azure-Portal werden Sie aufgefordert, Ihre Anmeldeinformationen für einen Azure Active Directory-Cluster zu überprüfen, und während der Clustererstellung (und in diesem Artikel auch bei der Neukonfiguration) zur Einrichtung der Clientregistrierung umgeleitet. Dieses Verhalten ähnelt dem für `kubectl` erforderlichen Authentifizierungsprozess.

>[!NOTE]
>Die Autorisierung für Ihren Cluster wird von Kubernetes und dem Sicherheitsmodell verwaltet, mit dem es konfiguriert ist. Benutzer, die auf diese Funktion zugreifen, benötigen die Berechtigung zum Herunterladen der Kubernetes-Konfiguration (*kubeconfig*), ähnlich der Ausführung von `az aks get-credentials -n {your cluster name} -g {your resource group}`. Diese Konfigurationsdatei enthält das Autorisierungs- und Authentifizierungstoken für die **Benutzerrolle für Azure Kubernetes Service-Cluster** für AKS-Cluster mit aktivierter Azure RBAC und AKS-Cluster ohne aktivierte Kubernetes RBAC-Autorisierung. Sie enthält Informationen über Azure AD sowie Details zur Clientregistrierung, wenn AKS mit auf SAML basiertem SSO in Azure Active Directory (AD) aktiviert ist.

>[!IMPORTANT]
>Benutzer dieser Funktion müssen für den Cluster über die [Benutzerrolle für Azure Kubernetes Service-Cluster](../../role-based-access-control/built-in-roles.md) verfügen, um `kubeconfig` herunterladen und diese Funktion verwenden zu können. Benutzer müssen **nicht** über den Zugriff für Mitwirkende auf den Cluster verfügen, um diese Funktion zu nutzen.

## <a name="using-clustermonitoringuser-with-kubernetes-rbac-enabled-clusters"></a>Verwenden von clusterMonitoringUser mit Kubernetes bei RBAC-fähigen Clustern

Um zu vermeiden, dass zusätzliche Konfigurationsänderungen erforderlich sind, damit die Kubernetes-Benutzerrollenbindung **clusterUser** nach [Aktivierung der Kubernetes RBAC](#configure-kubernetes-rbac-authorization)-Autorisierung auf die Funktion für Livedaten (Vorschau) zugreifen kann, hat AKS eine neue Kubernetes-Clusterrollenbindung mit Namen **clusterMonitoringUser** hinzugefügt. Diese Clusterrollenbindung verfügt standardmäßig über alle erforderlichen Berechtigungen für den Zugriff auf die Kubernetes-API und die Endpunkte, um die Funktion für Livedaten (Vorschau) zu nutzen.

Damit die Funktion für Livedaten (Vorschau) mit diesem neuen Benutzer verwendet werden kann, müssen Sie Mitglied der Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) für die AKS-Clusterressource sein. Azure Monitor für Container ist bei Aktivierung standardmäßig für die Authentifizierung mit diesem Benutzer konfiguriert. Ist die Rollenbindung „clusterMonitoringUser“ in einem Cluster nicht vorhanden, wird stattdessen **clusterUser** für die Authentifizierung verwendet.

AKS hat diese neue Rollenbindung im Januar 2020 veröffentlicht, sodass Cluster, die vor Januar 2020 erstellt wurden, nicht darüber verfügen. Wenn Sie über einen Cluster verfügen, der vor Januar 2020 erstellt wurde, kann die neue Rollenbindung **clusterMonitoringUser** einem vorhandenen Cluster hinzugefügt werden, indem Sie einen PUT-Vorgang für den Cluster ausführen oder einen anderen Vorgang verwenden, mit dem ein PUT-Vorgang für den Cluster ausgeführt wird, wie etwa das Aktualisieren der Clusterversion.

## <a name="kubernetes-cluster-without-kubernetes-rbac-enabled"></a>Kubernetes-Cluster ohne aktiviertes Kubernetes RBAC

Wenn Sie über einen Kubernetes-Cluster verfügen, der nicht mit der Kubernetes RBAC-Autorisierung konfiguriert oder mit einmaligem Anmelden von Azure AD integriert ist, brauchen Sie diese Schritte nicht auszuführen. Der Grund dafür ist, dass Sie in einer Nicht-RBAC-Konfiguration standardmäßig über Administratorberechtigungen verfügen.

## <a name="configure-kubernetes-rbac-authorization"></a>Konfigurieren der Kubernetes RBAC-Autorisierung

Wenn Sie die Kubernetes RBAC-Autorisierung aktivieren, werden zwei Benutzer, **clusterUser** und **clusterAdmin**, für den Zugriff auf die Kubernetes-API verwendet. Dies ähnelt dem Ausführen von `az aks get-credentials -n {cluster_name} -g {rg_name}` ohne die Verwaltungsoption. Das bedeutet, dass **clusterUser** Zugriff auf die Endpunkte in der Kubernetes-API erteilt werden muss.

Die folgenden Beispielschritte zeigen, wie Sie über diese yaml-Konfigurationsvorlage die Clusterrollenbindung konfigurieren.

1. Kopieren Sie die yaml-Datei, fügen Sie sie anschließend ein, und speichern Sie sie als LogReaderRBAC.yaml.

    ```
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
       name: containerHealth-log-reader
    rules:
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"]
          resources:
             - "pods/log"
             - "events"
             - "nodes"
             - "pods"
             - "deployments"
             - "replicasets"
          verbs: ["get", "list"]
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
       name: containerHealth-read-logs-global
    roleRef:
       kind: ClusterRole
       name: containerHealth-log-reader
       apiGroup: rbac.authorization.k8s.io
    subjects:
    - kind: User
      name: clusterUser
      apiGroup: rbac.authorization.k8s.io
    ```

2. Führen Sie zum Aktualisieren Ihrer Konfiguration den Befehl `kubectl apply -f LogReaderRBAC.yaml` aus.

>[!NOTE]
> Wenn Sie eine frühere Version der Datei `LogReaderRBAC.yaml` auf Ihren Cluster angewendet haben, aktualisieren Sie sie. Dazu kopieren Sie den in Schritt 1 oben gezeigten neuen Code, fügen ihn ein, und führen dann den in Schritt 2 gezeigten Befehl aus, um den Code auf den Cluster anzuwenden.

## <a name="configure-ad-integrated-authentication"></a>Konfigurieren der AD-integrierten Authentifizierung

Ein AKS-Cluster, der zur Verwendung von Azure Active Directory (AD) für die Benutzerauthentifizierung konfiguriert ist, verwendet die Anmeldeinformationen der Person, die auf diese Funktion zugreift. Bei dieser Konfiguration können Sie sich mit Ihrem Azure AD-Authentifizierungstoken bei einem AKS-Cluster anmelden.

Die Azure AD-Clientregistrierung muss neu konfiguriert werden, damit das Azure-Portal die Autorisierungsseiten als vertrauenswürdige Umleitungs-URL umleiten kann. Benutzern aus Azure AD wird dann der direkte Zugriff auf dieselben Kubernetes-API-Endpunkte über **ClusterRoles** und **ClusterRoleBindings** gewährt.

Weitere Informationen zur Einrichtung für erweiterte Sicherheit in Kubernetes finden Sie in der [Kubernetes-Dokumentation](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

>[!NOTE]
>Wenn Sie einen neuen Kubernetes RBAC-fähigen Cluster erstellen, lesen Sie [Integrieren von Azure Active Directory in Azure Kubernetes Service](../../aks/azure-ad-integration-cli.md), und folgen Sie den Schritten zum Konfigurieren der Azure AD-Authentifizierung. Während der Schritte zum Erstellen der Clientanwendung werden in einem Hinweis in diesem Abschnitt zwei Umleitungs-URLs hervorgehoben, die Sie für Azure Monitor für Container erstellen müssen, die den in Schritt 3 unten angegebenen Containern entsprechen.

### <a name="client-registration-reconfiguration"></a>Neukonfiguration der Clientregistrierung

1. Suchen Sie die Clientregistrierung für Ihren Kubernetes-Cluster in Azure AD unter **Azure Active Directory > App-Registrierungen** im Azure-Portal.

2. Wählen Sie im linken Bereich die Option **Authentifizierung** aus.

3. Fügen Sie dieser Liste zwei Umleitungs-URLs als **Web**-Anwendungstypen hinzu. Der erste Basis-URL-Wert sollte `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` lauten und der zweite Basis-URL-Wert `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

    >[!NOTE]
    >Wenn Sie diese Funktion in Azure China verwenden, sollte der erste Basis-URL-Wert `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` und der zweite Basis-URL-Wert `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` lauten.

4. Nachdem Sie die Umleitungs-URLs registriert haben, wählen Sie unter **Implizite Genehmigung** die Optionen **Zugriffstoken** und **ID-Token** aus, und speichern Sie die Änderungen.

>[!NOTE]
>Die Konfiguration der Authentifizierung mit Azure Active Directory für einmaliges Anmelden kann nur während der anfänglichen Bereitstellung eines neuen AKS-Clusters durchgeführt werden. Sie können einmaliges Anmelden nicht für einen bereits bereitgestellten AKS-Cluster konfigurieren.

>[!IMPORTANT]
>Wenn Sie Azure AD mit dem aktualisierten URI erneut für die Benutzerauthentifizierung konfiguriert haben, löschen Sie den Cache Ihres Browsers, um sicherzustellen, dass das aktualisierte Authentifizierungstoken heruntergeladen und angewendet wird.

## <a name="grant-permission"></a>Erteilen einer Berechtigung

Jedem Azure AD-Konto muss die Berechtigung für die entsprechenden APIs in Kubernetes erteilt werden, um auf die Funktion für Livedaten (Vorschau) zugreifen zu können. Die entsprechenden Schritte für das Azure Active Directory-Konto ähneln den Schritten, die im Abschnitt [Kubernetes RBAC-Authentifizierung](#configure-kubernetes-rbac-authorization) beschrieben sind. Bevor Sie die YAML-Konfigurationsvorlage auf Ihren Cluster anwenden, ersetzen Sie **clusterUser** unter **ClusterRoleBinding** durch den gewünschten Benutzer.

>[!IMPORTANT]
>Wenn sich der Benutzer, für den die Kubernetes RBAC-Bindung gewährt wird, auf demselben Azure AD-Mandanten befindet, sollten Sie Berechtigungen gemäß userPrincipalName zuweisen. Befindet sich der Benutzer in einem anderen Azure AD-Mandanten, müssen Sie die Eigenschaft „objectId“ abfragen und verwenden.

Weitere Unterstützung beim Konfigurieren von **ClusterRoleBinding** für Ihren AKS-Cluster finden Sie unter [Erstellen einer Kubernetes RBAC-Bindung](../../aks/azure-ad-integration-cli.md#create-kubernetes-rbac-binding).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Authentifizierung eingerichtet haben, können Sie [Metriken](container-insights-livedata-metrics.md), [Bereitstellungen](container-insights-livedata-deployments.md) sowie [Ereignisse und Protokolle](container-insights-livedata-overview.md) von Ihrem Cluster in Echtzeit anzeigen.
