---
title: Beheben von Problemen mit der Sicherheit und Zugriffssteuerung
description: Hier erfahren Sie, wie Sie Probleme mit der Sicherheit und Zugriffssteuerung in Azure Data Factory beheben.
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: lle
ms.openlocfilehash: 0dac0dcb272b602be8b921bce0ffc68c05cb9cbd
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375169"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Beheben von Problemen mit der Sicherheit und Zugriffssteuerung in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden gängige Methoden zur Problembehandlung für die Sicherheit und Zugriffssteuerung in Azure Data Factory beschrieben.

## <a name="common-errors-and-messages"></a>Häufige Fehler und Meldungen

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>Konnektivitätsprobleme in der Kopieraktivität des Clouddatenspeichers

#### <a name="symptoms"></a>Symptome

Möglicherweise werden verschiedene Fehlermeldungen zurückgegeben, wenn Konnektivitätsprobleme im Quellen- oder Senkendatenspeicher auftreten.

#### <a name="cause"></a>Ursache 

Das Problem wird in der Regel durch einen der folgenden Faktoren verursacht:

* Die Proxyeinstellung im Knoten der selbstgehosteten Integration Runtime (IR), wenn Sie eine selbstgehostete IR verwenden.

* Die Firewalleinstellung im Knoten der selbstgehosteten Integration Runtime (IR), wenn Sie eine selbstgehostete IR verwenden.

* Die Firewalleinstellung im Clouddatenspeicher.

#### <a name="resolution"></a>Lösung

* Überprüfen Sie die folgenden Punkte, um sicherzustellen, dass ein Konnektivitätsproblem vorliegt:

   - Der Fehler wird von den Quellen- oder Senkenconnectors ausgelöst.
   - Der Fehler tritt am Anfang der Kopieraktivität auf.
   - Der Fehler ist für Azure IR oder die selbstgehostete IR mit einem Knoten konsistent, da es möglicherweise ein zufälliger Fehler in einer selbstgehosteten IR mit mehreren Knoten ist, wenn das Problem nur bei einigen der Knoten auftritt.

* Wenn Sie eine **selbstgehostete IR** verwenden, überprüfen Sie Ihre Proxy-, Firewall- und Netzwerkeinstellungen, da eine Verbindung mit dem gleichen Datenspeicher erfolgreich hergestellt werden könnte, wenn Sie eine Azure IR verwenden. Informationen zur Problembehandlung in diesem Szenario finden Sie hier:

   * [Ports und Firewalls für die selbstgehostete IR](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Kopieren von Daten nach und aus Azure Data Lake Storage Gen1 mithilfe von Azure Data Factory](./connector-azure-data-lake-store.md)
  
* Wenn Sie eine **Azure IR** verwenden, versuchen Sie, die Firewalleinstellung des Datenspeichers zu deaktivieren. Diese Vorgehensweise kann die Probleme in den folgenden zwei Situationen beheben:
  
   * [IP-Adressen von Azure Integration Runtime](./azure-integration-runtime-ip-addresses.md) sind nicht in der Zulassungsliste enthalten.
   * Das Feature *Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben* ist für [Azure Blob Storage](./connector-azure-blob-storage.md#supported-capabilities) und [Azure Data Lake Storage Gen 2](./connector-azure-data-lake-storage.md#supported-capabilities) deaktiviert.
   * Die Einstellung *Zugriff auf Azure-Dienste zulassen* ist nicht für Azure Data Lake Storage Gen1 aktiviert.

Wenn keine der vorherigen Methoden funktioniert, wenden Sie sich an Microsoft, um Hilfe zu erhalten.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Problem durch ungültigen oder leeren Authentifizierungsschlüssel nach Deaktivierung des Zugriffs über das öffentliche Netzwerk

#### <a name="symptoms"></a>Symptome

Nachdem Sie den öffentlichen Netzwerkzugriff für Data Factory deaktiviert haben, gibt die selbstgehostete Integration Runtime folgende Fehlermeldung aus: „Der Authentifizierungsschlüssel ist ungültig oder leer.“

#### <a name="cause"></a>Ursache

Das Problem wird wahrscheinlich durch ein Problem bei der Domain Name System-Auflösung (DNS) verursacht, da die Deaktivierung der öffentlichen Konnektivität und das Einrichten eines privaten Endpunkts eine erneute Verbindung verhindert.

Gehen Sie folgendermaßen vor, um zu überprüfen, ob der vollständig qualifizierte Data Factory-Domänenname (FQDN) in die öffentliche IP-Adresse aufgelöst ist:

1. Vergewissern Sie sich, dass Sie den virtuellen Azure-Computer (VM) im gleichen virtuellen Netzwerk wie den privaten Data Factory-Endpunkt erstellt haben.

2. Führen Sie PsPing und Ping von der Azure-VM zum Data Factory-FQDN aus:

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > Sie müssen einen Port für den PsPing-Befehl angeben. Port 443 wird hier angezeigt, ist jedoch nicht erforderlich.

3. Überprüfen Sie, ob beide Befehle in eine öffentliche Azure Data Factory-IP-Adresse aufgelöst werden, die auf einer bestimmten Region basiert. Die IP-Adresse sollte das folgende Format aufweisen: `xxx.xxx.xxx.0`

#### <a name="resolution"></a>Lösung

Gehen Sie zur Lösung des Problems wie folgt vor:

- Als Option empfiehlt es sich, manuell eine „VNet-Verknüpfung“ unter der „Private Link-DNS-Zone“ von Data Factory hinzuzufügen. Lesen Sie für weitere Informationen den Artikel [Azure Private Link für Azure Data Factory](./data-factory-private-link.md#dns-changes-for-private-endpoints). Die Anweisung dient zum Konfigurieren der privaten DNS-Zone oder des benutzerdefinierten DNS-Servers, um den Data Factory-FQDN in eine private IP-Adresse aufzulösen. 

- Wenn Sie die private DNS-Zone oder den benutzerdefinierten DNS-Server jedoch nicht konfigurieren möchten, versuchen Sie es mit der folgenden temporären Lösung:

  1. Ändern Sie die Hostdatei in Windows, und ordnen Sie die private IP-Adresse (den privaten Azure Data Factory-Endpunkt) dem Azure Data Factory-FQDN zu.
  
     Wechseln Sie auf dem virtuellen Azure-Computer zu `C:\Windows\System32\drivers\etc`, und öffnen Sie dann die Datei *host* im Editor. Fügen Sie die Zeile hinzu, mit der die private IP-Adresse dem FQDN am Ende der Datei zugeordnet wird, und speichern Sie die Änderung.
     
     ![Screenshot der Zuordnung der privaten IP-Adresse zum Host.](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Führen Sie die gleichen Befehle wie in den vorherigen Überprüfungsschritten erneut aus, um die Antwort zu überprüfen, die die private IP-Adresse enthalten sollte.

  1. Registrieren Sie die selbstgehostete Integration Runtime erneut, und das Problem sollte gelöst sein.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>IR-Authentifizierungsschlüssel kann aufgrund eines privaten Links nicht auf selbstgehosteten VMs registriert werden

#### <a name="symptoms"></a>Symptome

Sie können den IR-Authentifizierungsschlüssel nicht auf der selbstgehosteten VM registrieren, da der private Link aktiviert ist. Sie erhalten die folgende Fehlermeldung:

„Fehler beim Abrufen des Diensttokens aus dem ADF-Dienst mit dem Schlüssel ***************. Zeitaufwand: 0,1250079 Sekunden, Fehlercode: InvalidGatewayKey, activityId: XXXXXXX Detaillierte Fehlermeldung: Die Client-IP-Adresse ist keine gültige private IP-Adresse, weil Data Factory nicht auf das öffentliche Netzwerk zugreifen und so nicht auf die Cloud zugreifen konnte, um die Verbindung herzustellen.“

#### <a name="cause"></a>Ursache

Das Problem könnte von dem virtuellen Computer verursacht worden sein, in dem Sie die selbstgehostete IR zu installieren versuchen. Um eine Verbindung mit der Cloud herzustellen, stellen Sie sicher, dass öffentlicher Netzwerkzugriff aktiviert ist.

#### <a name="resolution"></a>Lösung

**Lösung 1**
 
Gehen Sie zur Lösung des Problems wie folgt vor:

1. Wechseln Sie zur Seite [Factorys – Update](/rest/api/datafactory/Factories/Update).

1. Wählen Sie oben rechts die Schaltfläche **Jetzt testen** aus.
1. Geben Sie unter **Parameter** die erforderlichen Informationen an. 
1. Fügen Sie unter **Text** die folgende Eigenschaft ein:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. Wählen Sie **Ausführen** aus, um die Funktion auszuführen. 

1. Geben Sie unter **Parameter** die erforderlichen Informationen an. 

1. Fügen Sie unter **Text** die folgende Eigenschaft ein:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Wählen Sie **Ausführen** aus, um die Funktion auszuführen. 
1. Vergewissern Sie sich, dass **Antwortcode: 200** angezeigt wird. Die Eigenschaft, die Sie eingefügt haben, sollte auch in der JSON-Definition angezeigt werden.

1. Fügen Sie den IR-Authentifizierungsschlüssel erneut der Integration Runtime hinzu.


**Lösung 2**

Um das Problem zu lösen, gehen Sie zu [Azure Private Link für Azure Data Factory](./data-factory-private-link.md).

Versuchen Sie, den Zugriff über das öffentliche Netzwerk auf der Benutzeroberfläche zu aktivieren, wie im folgenden Screenshot gezeigt:

![Screenshot des Steuerelements „Aktiviert“ für „Zugriff über öffentliche Netzwerke zulassen“ im Bereich „Netzwerk“.](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Hilfe zur Problembehandlung finden Sie in den folgenden Ressourcen:

*  [Private Link für Data Factory](data-factory-private-link.md)
*  [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory-Funktionsanfragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
*  [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)