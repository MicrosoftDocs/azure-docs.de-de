---
title: Beheben von Problemen mit der Sicherheit und Zugriffssteuerung
description: Hier erfahren Sie, wie Sie Probleme mit der Sicherheit und Zugriffssteuerung in Azure Data Factory beheben.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008723"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Beheben von Problemen mit der Sicherheit und Zugriffssteuerung in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden gängige Methoden zur Problembehandlung für die Sicherheit und Zugriffssteuerung in Azure Data Factory beschrieben.

## <a name="common-errors-and-messages"></a>Häufige Fehler und Meldungen

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>Problem durch ungültigen oder leeren Authentifizierungsschlüssel nach Deaktivierung des Zugriffs über das öffentliche Netzwerk

#### <a name="symptoms"></a>Symptome

Die selbstgehostete Integration Runtime löst den Fehler „Der Authentifizierungsschlüssel ist ungültig oder leer“ aus, nachdem der Zugriff über das öffentliche Netzwerk für Data Factory deaktiviert wurde.

#### <a name="cause"></a>Ursache

Der Fehler wird wahrscheinlich durch ein Problem mit der DNS-Auflösung verursacht, da die Deaktivierung der öffentlichen Verbindung und das Einrichten eines privaten Endpunkts keine Hilfe für eine erneute Verbindung bietet.

#### <a name="resolution"></a>Lösung

1. Bei einem PsPing-Vorgang für den FQDN von ADF wurde festgestellt, dass der Puffer auch dann zu einem öffentlichen Endpunkt von ADF führte, nachdem er deaktiviert wurde.

1. Ändern Sie die Hostdatei auf der VM, um dem FQDN eine private IP-Adresse zuzuordnen, und führen Sie erneut einen PsPing-Vorgang aus. Der Puffer kann anschließend zur richtigen privaten IP-Adresse von ADF wechseln.

1. Registrieren Sie die selbstgehostete Integration Runtime erneut, und sie wird wieder ordnungsgemäß ausgeführt.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>IR-Authentifizierungsschlüssel kann aufgrund eines privaten Links nicht auf selbstgehosteten VMs registriert werden

#### <a name="symptoms"></a>Symptome

Der IR-Authentifizierungsschlüssel kann aufgrund der Aktivierung eines privaten Links nicht auf einer selbstgehosteten VM registriert werden.

Die Fehlerinformationen werden wie folgt angezeigt:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Ursache

Das Problem kann durch die VM verursacht werden, auf der die selbstgehostete Integration Runtime installiert werden soll. Der Zugriff über das öffentliche Netzwerk muss für die Verbindung mit der Cloud aktiviert werden.

#### <a name="resolution"></a>Lösung

 **Lösung 1:** Zum Beheben des Problems können Sie die folgenden Schritte ausführen:

1. Navigieren Sie zum folgenden Link: 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. Klicken Sie auf die Option **Jetzt testen**, und machen Sie alle erforderlichen Angaben. Fügen Sie außerdem die folgende Eigenschaft in den **Textteil** ein:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Klicken Sie unten auf der Seite auf **Auszuführen**, um die Funktion auszuführen. Vergewissern Sie sich, dass Sie Antwortcode 200 erhalten. Die eingefügte Eigenschaft wird ebenfalls in der JSON-Definition angezeigt.

1. Anschließend können Sie versuchen, den IR-Authentifizierungsschlüssel erneut der Integration Runtime hinzuzufügen.


**Lösung 2:** Eine Lösung finden Sie in dem folgenden Artikel:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Versuchen Sie, den Zugriff aus öffentlichen Netzwerken über die Benutzeroberfläche zu aktivieren.

![Aktivieren des Zugriffs aus öffentlichen Netzwerken](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Hilfe zur Problembehandlung finden Sie in den folgenden Ressourcen:

*  [Private Link für Data Factory](data-factory-private-link.md)
*  [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory-Funktionsanfragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-data-factory.html)
*  [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)