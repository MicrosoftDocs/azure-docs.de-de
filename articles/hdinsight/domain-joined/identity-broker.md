---
title: Azure HDInsight-Identitätsbroker (HIB)
description: Erfahren Sie etwas über den Azure HDInsight-Identitätsbroker zur Vereinfachung der Authentifizierung für in Domänen eingebundene Apache Hadoop-Cluster.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: c6bc5ca748a35b17c61d314e96f7284d30e7fc3b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338128"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Azure HDInsight-Identitätsbroker (HIB)

In diesem Artikel wird beschrieben, wie Sie das Feature „Azure HDInsight-Identitätsbroker“ einrichten und verwenden. Sie können dieses Feature verwenden, um die moderne OAuth-Authentifizierung in Apache Ambari zu erhalten, während Sie über die Erzwingung der mehrstufigen Authentifizierung verfügen, ohne Legacy-Kennworthashes in Azure Active Directory Domain Services (Azure AD DS) zu benötigen.

## <a name="overview"></a>Übersicht

Der HDInsight-Identitätsbroker vereinfacht die komplexe Einrichtung der Authentifizierung in den folgenden Szenarien:

* In Ihrer Organisation werden Benutzer basierend auf einem Verbund für den Zugriff auf Cloudressourcen authentifiziert. Bisher mussten Sie zur Verwendung von HDInsight-Clustern mit Enterprise-Sicherheitspaket in der lokalen Umgebung die Kennworthashsynchronisierung für Azure Active Directory (Azure AD) aktivieren. Diese Anforderung kann in einigen Organisationen schwierig oder unerwünscht sein.
* Ihre Organisation möchte die mehrstufige Authentifizierung für webbasierten oder HTTP-basierten Zugriff auf Apache Ambari und andere Clusterressourcen erzwingen.

HDInsight-Identitätsbroker stellt die Infrastruktur für die Authentifizierung bereit, die den Protokollübergang von OAuth (modern) zu Kerberos (Legacy) ermöglicht, ohne dass Kennworthashes mit Azure AD DS synchronisiert werden müssen. Diese Infrastruktur besteht aus Komponenten, die auf einem virtuellen Windows Server-Computer (VM) mit aktiviertem HDInsight-Identitätsbroker-Knoten sowie Clustergatewayknoten ausgeführt werden.

Verwenden Sie die folgende Tabelle, um die beste Authentifizierungsoption basierend auf den Anforderungen Ihrer Organisation zu bestimmen.

|Authentifizierungsoptionen |HDInsight-Konfiguration | Zu beachtende Faktoren |
|---|---|---|
| Vollständig OAuth | Enterprise-Sicherheitspaket + HDInsight-Identitätsbroker | Die sicherste Option. (Mehrstufige Authentifizierung wird unterstützt.) Die Synchronisierung von Kennworthashes ist *nicht* erforderlich. Kein ssh/kinit/keytab-Zugriff für lokale Konten, die keinen Kennworthash in Azure AD DS besitzen. Reine Cloudkonten können weiterhin ssh/kinit/keytab verwenden. Webbasierter Zugriff auf Ambari über OAuth. Erfordert die Aktualisierung von Legacy-Apps (z. B. JDBC/ODBC) für die Unterstützung von OAuth.|
| OAuth + Standardauthentifizierung | Enterprise-Sicherheitspaket + HDInsight-Identitätsbroker | Webbasierter Zugriff auf Ambari über OAuth. Legacy-Apps verwenden weiterhin die Standardauthentifizierung. Die mehrstufige Authentifizierung muss für den Zugriff mit Standardauthentifizierung deaktiviert werden. Die Synchronisierung von Kennworthashes ist *nicht* erforderlich. Kein ssh/kinit/keytab-Zugriff für lokale Konten, die keinen Kennworthash in Azure AD DS besitzen. Reine Cloudkonten können weiterhin ssh/kinit verwenden. |
| Vollständige Standardauthentifizierung | Enterprise-Sicherheitspaket | Entspricht am ehesten lokalen Einrichtungen. Die Synchronisierung von Kennworthashes für Azure AD DS ist erforderlich. Lokale Konten können per ssh/kinit oder keytab verwendet werden. Die mehrstufige Authentifizierung muss deaktiviert sein, wenn der Sicherungsspeicher Azure Data Lake Storage Gen2 entspricht. |

In der folgenden Abbildung ist der moderne auf OAuth basierende Authentifizierungsablauf für alle Benutzer, einschließlich Verbundbenutzer, nach der Aktivierung des HDInsight-Identitätsbrokers dargestellt:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Diagramm zum Authentifizierungsablauf mit HDInsight-Identitätsbroker.":::

In diesem Diagramm muss der Client (d. h. ein Browser oder eine App) zuerst das OAuth-Token abrufen. Anschließend präsentiert es dem Gateway das Token in einer HTTP-Anforderung. Wenn Sie bereits bei anderen Azure-Diensten angemeldet sind, z. B. im Azure-Portal, können Sie sich mittels einmaligem Anmelden bei Ihrem HDInsight-Cluster anmelden.

Möglicherweise gibt es noch viele Legacyanwendungen, die nur die Standardauthentifizierung (d. h. Benutzername und Kennwort) unterstützen. Für diese Szenarien können Sie weiterhin die HTTP-Standardauthentifizierung verwenden, um eine Verbindung mit den Clustergateways herzustellen. Bei diesem Setup müssen Sie die Netzwerkkonnektivität von den Gatewayknoten zum Endpunkt der Active Directory Federation Services (AD FS) sicherstellen, um für den direkten Zugriff von Gatewayknoten zu sorgen.

Das folgende Diagramm zeigt den Ablauf der Standardauthentifizierung für Verbundbenutzer. Zunächst versucht das Gateway, die Authentifizierung mithilfe des [ROPC-Flows](../../active-directory/develop/v2-oauth-ropc.md) abzuschließen. Falls keine Kennworthashes mit Azure AD synchronisiert sind, greift es auf die Ermittlung des AD FS-Endpunkts zurück und schließt die Authentifizierung durch Zugriff auf den AD FS-Endpunkt ab.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Diagramm, das eine Architektur mit Standardauthentifizierung anzeigt.":::


## <a name="enable-hdinsight-id-broker"></a>Aktivieren des HDInsight-Identitätsbrokers

So erstellen Sie einen Enterprise-Sicherheitspaketcluster mit aktiviertem HDInsight-Identitätsbroker:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Befolgen Sie die grundlegenden Erstellungsschritte für einen Enterprise-Sicherheitspaketcluster. Weitere Informationen finden Sie unter [Erstellen eines HDInsight-Clusters mit Enterprise-Sicherheitspaket](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Wählen Sie **HDInsight-Identitätsbroker aktivieren (Vorschau)** aus.

Das Feature HDInsight-Identitätsbroker fügt dem Cluster einen weiteren virtuellen Computer hinzu. Dieser virtuelle Computer ist der HDInsight-Identitätsbrokerknoten, der Serverkomponenten zur Unterstützung der Authentifizierung enthält. Der HDInsight-Identitätsbrokerknoten ist in die Azure AD DS-Domäne eingebunden.

![Diagramm, das die Option zum Aktivieren des HDInsight-Identitätsbrokers anzeigt.](./media/identity-broker/identity-broker-enable.png)

### <a name="use-azure-resource-manager-templates"></a>Verwenden von Azure-Ressourcen-Manager-Vorlagen

Wenn Sie dem Computeprofil Ihrer Vorlage eine neue Rolle namens `idbrokernode` mit den folgenden Attributen hinzufügen, wird der Cluster mit aktiviertem HDInsight-Identitätsbrokerknoten erstellt:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 2,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

Ein vollständiges Beispiel einer ARM-Vorlage finden Sie in der [hier](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template) veröffentlichten Vorlage.


## <a name="tool-integration"></a>Toolintegration

HDInsight-Tools werden aktualisiert, um OAuth nativ zu unterstützen. Verwenden Sie diese Tools für den modernen OAuth-basierten Zugriff auf die Cluster. Das HDInsight [IntelliJ-Plug-In](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib) kann für Java-basierte Anwendungen wie Scala verwendet werden. [Spark- und Hive-Tools für Visual Studio Code](../hdinsight-for-vscode.md) können für PySpark- und Hive-Aufträge verwendet werden. Die Tools unterstützen Batch- und interaktive Aufträge.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-Zugriff ohne Kennworthash in Azure AD DS

|SSH-Optionen |Zu beachtende Faktoren |
|---|---|
| Lokales VM-Konto (z. B. sshuser) | Sie haben dieses Konto zum Zeitpunkt der Clustererstellung bereitgestellt. Für dieses Konto gibt es keine Kerberos-Authentifizierung. |
| Reines Cloudkonto (z. B. alice@contoso.onmicrosoft.com) | Der Kennworthash ist in Azure AD DS verfügbar. Die Kerberos-Authentifizierung ist über SSH Kerberos möglich. |
| Lokales Konto (z. B. alice@contoso.com) | Die SSH Kerberos-Authentifizierung ist nur möglich, wenn ein Kennworthash in Azure AD DS verfügbar ist. Andernfalls kann dieser Benutzer kein SSH für den Cluster verwenden. |

Sie müssen ein Kennwort angeben, um eine SSH-Verbindung mit einem in eine Domäne eingebundenen virtuellen Computer herzustellen oder um den Befehl `kinit` auszuführen. Für die SSH Kerberos-Authentifizierung muss der Hash in Azure AD DS verfügbar sein. Wenn Sie SSH nur für administrative Szenarien verwenden möchten, können Sie ein ausschließliches Cloudkonto erstellen und für die SSH-Verbindung mit dem Cluster verwenden. Andere lokale Benutzer können weiterhin Ambari- oder HDInsight-Tools oder die HTTP-Standardauthentifizierung verwenden, ohne dass der Kennworthash in Azure AD DS verfügbar ist.

Wenn Ihre Organisation keine Kennworthashes mit Azure AD DS synchronisiert, erstellen Sie als bewährte Methode einen einzelnen reinen Cloudbenutzer in Azure AD. Weisen Sie ihn dann beim Erstellen des Clusters als Clusteradministrator zu, und verwenden Sie ihn für Verwaltungszwecke. Sie können über ihn den Root-Zugriff auf die virtuellen Computer über SSH abrufen.

Informationen zum Behandeln von Authentifizierungsfehlern finden Sie in diesem [Handbuch](./domain-joined-authentication-issues.md).

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>Clients, die OAuth zum Herstellen einer Verbindung mit einem HDInsight-Gateway mithilfe des HDInsight-Identitätsbrokers verwenden

Im HDInsight-Identitätsbrokersetup können benutzerdefinierte Apps und Clients aktualisiert werden, die eine Verbindung mit dem Gateway herstellen, um zuerst das erforderliche OAuth-Token abzurufen. Führen Sie die Schritte in diesem [Dokument](../../storage/common/storage-auth-aad-app.md) aus, um das Token mit den folgenden Informationen abzurufen:

*   OAuth-Ressourcen-URI: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Berechtigung: (Name: Cluster.ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Nachdem Sie das OAuth-Token abgerufen haben, verwenden Sie es im Autorisierungsheader der HTTP-Anforderung für das Clustergateway (z. B. https://<clustername>-int.azurehdinsight.net). Ein Beispiel für einen curl-Befehl für die Apache Livy-API könnte wie dieses Beispiel aussehen:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

Für die Verwendung von Beeline und Livy können Sie auch die [hier](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples) angegebenen Codebeispiele verwenden, um Ihren Client so einzurichten, dass er OAuth verwendet und eine Verbindung mit dem Cluster herstellt.

## <a name="faq"></a>Häufig gestellte Fragen
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>Welche App wird von HDInsight in AAD erstellt?
Für jeden Cluster wird in AAD eine Drittanbieteranwendung mit dem Cluster-URI als identifierUri registriert (z. B. `https://clustername.azurehdinsight.net`).

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>Warum werden Benutzer vor der Verwendung von HIB-aktivierten Clustern zur Einwilligung aufgefordert?
In AAD ist die Einwilligung für alle Drittanbieteranwendungen erforderlich, bevor Benutzer authentifiziert werden können oder auf Daten zugegriffen werden kann.

### <a name="can-the-consent-be-approved-programatically"></a>Kann die Einwilligung programmgesteuert gegeben werden?
Mit der Microsoft Graph-API können Sie die Einwilligung automatisieren. Siehe dazu die [API-Dokumentation](/graph/api/resources/oauth2permissiongrant?view=graph-rest-1.0). Zum Automatisieren der Einwilligung sind folgende Schritte erforderlich:

* Registrieren Sie eine App, und erteilen Sie Application.ReadWrite.All-Berechtigungen für die App, um auf Microsoft Graph zuzugreifen.
* Führen Sie nach dem Erstellen eines Clusters eine Abfrage der Cluster-App basierend auf dem Bezeichner-URI durch.
* Registrieren Sie die Einwilligung für die App.

Wenn der Cluster gelöscht wird, löscht HDInsight die App. Es ist nicht erforderlich, die Einwilligung zu bereinigen.

 


## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren eines HDInsight-Clusters mit Enterprise-Sicherheitspaket (Enterprise Security Package, ESP) mithilfe von Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synchronisieren von Azure Active Directory-Benutzern in einen HDInsight-Cluster](../hdinsight-sync-aad-users-to-cluster.md)
* [Überwachen der Clusterleistung](../hdinsight-key-scenarios-to-monitor.md)