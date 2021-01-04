---
title: Anwendungsbereitstellungsstatus „Quarantäne“ | Microsoft-Dokumentation
description: Wenn Sie eine Anwendung für die automatische Benutzerbereitstellung konfiguriert haben, lesen Sie diesen Artikel, um zu erfahren, was der Bereitstellungsstatus „Quarantäne“ bedeutet und wie Sie ihn löschen können.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/24/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 6a716aef65cc81c5558a214c1ee5f93180810977
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91266682"
---
# <a name="application-provisioning-in-quarantine-status"></a>Anwendungsbereitstellung im Quarantänestatus

Der Azure AD-Bereitstellungsdienst überwacht die Integrität Ihrer Konfiguration und versetzt fehlerhafte Apps in einen „Quarantänestatus“. Wenn die meisten oder alle Aufrufe an das Zielsystem aufgrund eines Fehlers (z. B. bei ungültigen Administratoranmeldeinformationen) dauerhaft nicht erfolgreich sind, wird der Bereitstellungsauftrag in den Quarantänestatus versetzt.

In der Quarantäne wird die Häufigkeit der inkrementellen Zyklen allmählich auf einmal pro Tag reduziert. Die Quarantäne für den Bereitstellungsauftrag wird aufgehoben, nachdem alle Fehler behoben wurden, und der nächste Synchronisierungszyklus wird gestartet. Wenn der Bereitstellungsauftrag länger als vier Wochen in Quarantäne verbleibt, wird er deaktiviert (nicht mehr ausgeführt).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Wie kann ich prüfen, ob sich meine Anwendung in Quarantäne befindet?

Es gibt drei Möglichkeiten, um zu überprüfen, ob sich eine Anwendung in Quarantäne befindet:
  
- Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Unternehmensanwendungen** > &lt;*Anwendungsname*&gt; > **Bereitstellung**, und suchen Sie in der Statusanzeige nach einer Quarantänenachricht.   

  ![Statusanzeige für die Bereitstellung mit dem Quarantänestatus](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Überwachungsprotokolle**, und legen Sie den Filter auf **Aktivität: Quarantäne** fest, und überprüfen Sie den Quarantäneverlauf. Die Ansicht in der Statusanzeige zeigt zwar, wie oben beschrieben, an, ob sich die Bereitstellung derzeit in Quarantäne befindet, aber mit den Überwachungsprotokollen können Sie den Quarantäneverlauf für eine Anwendung anzeigen. 

- Verwenden Sie die Microsoft Graph-Anforderung [GET synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta), um den Status des Bereitstellungsauftrags programmgesteuert abzurufen:

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- Prüfen Sie Ihren Posteingang. Wird eine Anwendung unter Quarantäne gestellt, wird eine einmalige Benachrichtigungs-E-Mail gesendet. Wenn sich der Grund für die Quarantäne ändert, wird eine aktualisierte E-Mail mit dem neuen Grund für die Quarantäne gesendet. Wenn keine E-Mail angezeigt wird:

  - Stellen Sie sicher, dass Sie in der Bereitstellungskonfiguration für die Anwendung eine gültige **Benachrichtigungs-E-Mail** angegeben haben.
  - Stellen Sie sicher, dass im Posteingang für Benachrichtigungs-E-Mails keine Spamfilterung aktiviert ist.
  - Stellen Sie sicher, dass Sie keine E-Mails abbestellt haben.
  - Überprüfen Sie die E-Mails von azure-noreply@microsoft.com.

## <a name="why-is-my-application-in-quarantine"></a>Warum befindet sich meine Anwendung in Quarantäne?

|BESCHREIBUNG|Empfohlene Maßnahme|
|---|---|
|**SCIM-Konformitätsproblem:** Anstelle der erwarteten Antwort „HTTP/200 OK“ wurde die Antwort „HTTP/404 Nicht gefunden“ zurückgegeben. In diesem Fall hat der Azure AD-Bereitstellungsdienst eine Anforderung an die Zielanwendung gesendet und eine unerwartete Antwort empfangen.|Überprüfen Sie im Abschnitt „Administratoranmeldeinformationen“, ob die Anwendung das Angeben der Mandanten-URL erfordert, und stellen Sie sicher, dass die URL korrekt ist. Wenn Sie kein Problem feststellen können, wenden Sie sich an den Anwendungsentwickler, um sicherzustellen, dass der Dienst SCIM-konform ist. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Ungültige Anmeldeinformationen:** Beim Versuch, den Zugriff auf die Zielanwendung zu autorisieren, wurde eine Antwort von der Zielanwendung empfangen, derzufolge die angegebenen Anmeldeinformationen ungültig sind.|Navigieren Sie auf der Benutzeroberfläche für die Bereitstellungskonfiguration zum Abschnitt „Administratoranmeldeinformationen“, und autorisieren Sie den Zugriff noch mal mit gültigen Anmeldeinformationen. Wenn sich die Anwendung im Katalog befindet, sehen Sie sich das Tutorial zur Anwendungskonfiguration an, um etwaige weitere erforderliche Schritte auszuführen.|
|**Doppelte Rollen:** Rollen, die aus bestimmten Anwendungen wie Salesforce oder Zendesk importiert werden, müssen eindeutig sein. |Navigieren Sie im Azure-Portal zum [Manifest](../develop/reference-app-manifest.md) der Anwendung, und entfernen Sie die doppelte Rolle.|

 Eine Microsoft Graph-Anforderung zum Abrufen des Status des Bereitstellungsauftrags zeigt den folgenden Grund für die Quarantäne an:

- `EncounteredQuarantineException` gibt an, dass ungültige Anmeldeinformationen angegeben wurden. Der Bereitstellungsdienst kann keine Verbindung zwischen dem Quellsystem und dem Zielsystem herstellen.

- `EncounteredEscrowProportionThreshold` gibt an, dass die Bereitstellung den hinterlegten Schwellenwert überschritten hat. Dieser Zustand tritt auf, wenn mehr als 60 % der Bereitstellungsereignisse fehlgeschlagen sind.

- `QuarantineOnDemand` bedeutet, dass wir ein Problem bei Ihrer Anwendung erkannt und sie manuell in den Quarantänestatus versetzt haben.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Wie kann ich dafür sorgen, dass meine Anwendung aus der Quarantäne entfernt wird?

Beheben Sie zuerst das Problem, das dazu geführt hat, dass die Anwendung unter Quarantäne gestellt wurde.

- Überprüfen Sie die Bereitstellungseinstellungen der Anwendung, um sicherzustellen, dass Sie [gültige Administratoranmeldeinformationen eingegeben](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning) haben. Azure AD muss eine Vertrauensstellung mit der Zielanwendung herstellen können. Stellen Sie sicher, dass Sie gültige Anmeldeinformationen eingegeben haben und Ihr Konto über die erforderlichen Berechtigungen verfügt.

- Überprüfen Sie die [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md), um weiter zu untersuchen, welche Fehler zur Quarantäne geführt haben, und beheben Sie den Fehler. Greifen Sie im Azure-Portal auf die Bereitstellungsprotokolle zu, indem Sie im Abschnitt **Aktivität** zu **Azure Active Directory** &gt; **Unternehmensanwendungen** &gt; **Bereitstellungsprotokolle (Vorschau)** navigieren.

Nachdem Sie das Problem behoben haben, können Sie den Bereitstellungsauftrag neu starten. Durch bestimmte Änderungen an den Bereitstellungseinstellungen (wie Attributzuordnungen oder Bereichsfilter) der Anwendung wird die Bereitstellung automatisch neu gestartet. Die Statusanzeige auf der Seite **Bereitstellung** der Anwendung gibt an, wann die Bereitstellung zuletzt gestartet wurde. Wenn Sie den Bereitstellungsauftrag manuell neu starten müssen, verwenden Sie eine der folgenden Methoden:  

- Verwenden Sie das Azure-Portal, um den Bereitstellungsauftrag neu zu starten. Wählen Sie auf der Seite **Bereitstellung** der Anwendung unter **Einstellungen** die Option **Aktuellen Status löschen und Synchronisierung neu starten** aus, und legen Sie **Bereitstellungsstatus** auf **Ein** fest. Durch diese Aktion wird der Bereitstellungsdienst vollständig neu gestartet. Dieser Vorgang kann einige Zeit in Anspruch nehmen. Ein vollständiger Startzyklus wird erneut ausgeführt. Dabei werden Hinterlegungen gelöscht, die App wird aus der Quarantäne entfernt, und alle Wasserzeichen werden gelöscht.

- Verwenden Sie Microsoft Graph, um [den Bereitstellungsauftrag neu zu starten](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). Sie haben vollständige Kontrolle über die Elemente, die Sie neu starten. Sie können auswählen, ob Hinterlegungen (zum Neustarten des Hinterlegungszählers, der in Richtung Quarantänestatus läuft) gelöscht, die Quarantäne (zum Entfernen der Anwendung aus der Quarantäne) oder die Wasserzeichen gelöscht werden soll(en). Verwenden Sie die folgende Anforderung:
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

Ersetzen Sie "{id}" durch den Wert der Anwendungs-ID und "{jobId}" durch die [ID des Synchronisierungsauftrags](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta#list-synchronization-jobs-in-the-context-of-the-service-principal).
