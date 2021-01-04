---
title: Azure AD Connect-Synchronisierungsendpunkt V2 | Microsoft-Dokumentation
description: In diesem Dokument werden Aktualisierungen der Synchronisierungsendpunkt-API V2 für Azure AD Connect behandelt.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07acd71eb1a77592c82b80aa574d79b98c48f60e
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608298"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Azure AD Connect-Synchronisierung: V2-Endpunkt-API 
Microsoft hat einen neuen Endpunkt (API) für Azure AD Connect bereitgestellt, mit dem die Leistung der Synchronisierungsdienstvorgänge für Azure Active Directory verbessert wird. Wenn Sie den neuen V2-Endpunkt verwenden, treten beim Exportieren aus und Importieren in Azure AD spürbare Leistungssteigerungen auf. Dieser neue Endpunkt unterstützt Folgendes:
    
 - Synchronisieren von Gruppen mit bis zu 250.000 Mitgliedern
 - Leistungssteigerungen beim Exportieren aus und Importieren in Azure AD
 
> [!NOTE]
> Der neue Endpunkt verfügt derzeit nicht über eine konfigurierte Beschränkung der Gruppengröße für Microsoft 365-Gruppen, die zurückgeschrieben werden. Dies hat möglicherweise Auswirkungen auf Ihr Active Directory und Synchronisierungszykluslatenzen. Es wird empfohlen, die Gruppengröße inkrementell zu erhöhen.  

## <a name="pre-requisites"></a>Voraussetzungen  
Um den neuen V2-Endpunkt verwenden zu können, müssen Sie [Azure AD Connect Version 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) oder höher verwenden und die unten angegebenen Bereitstellungsschritte ausführen, um den V2-Endpunkt für Ihren Azure AD Connect-Server zu aktivieren.   

## <a name="deployment-guidance"></a>Hinweise zur Bereitstellung 
Sie müssen [Azure AD Connect Version 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) oder höher bereitstellen, um den V2-Endpunkt zu verwenden. Verwenden Sie den bereitgestellten Link zum Herunterladen. 

Es wird empfohlen, die [Swingmigrationsmethode](./how-to-upgrade-previous-version.md#swing-migration) für das Rollout des neuen Endpunkts in Ihrer Umgebung zu befolgen. Dadurch wird ein klarer Notfallplan für den Fall bereitgestellt, dass ein umfangreiches Rollback erforderlich ist. Im folgenden Beispiel wird veranschaulicht, wie eine Swingmigration in diesem Szenario verwendet werden kann. Weitere Informationen zur Swingmigrations-Bereitstellungsmethode finden Sie unter dem bereitgestellten Link. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>Swingmigration zum Bereitstellen des V2-Endpunkts
Die folgenden Schritte führen Sie durch die Bereitstellung des V2-Endpunkts mithilfe der Swingmethode.

1. Stellen Sie den V2-Endpunkt auf dem aktuellen Stagingserver bereit. Dieser Server wird in den folgenden Schritten als **V2-Server** bezeichnet. Der aktuelle aktive Server verarbeitet die Produktionsworkloads weiterhin mithilfe des V1-Endpunkts, der im Folgenden als **V1-Server** bezeichnet wird.
1. Bestätigen Sie, dass der **V2-Server** Importe weiterhin erwartungsgemäß verarbeitet. Zu diesem Zeitpunkt werden große Gruppen nicht für Azure AD oder das lokale AD bereitgestellt, aber Sie können bestätigen, dass das Upgrade keine anderen unerwarteten Auswirkungen auf den vorhandenen Synchronisierungsprozess verursacht hat. 
2. Sobald die Überprüfung abgeschlossen ist, erklären Sie den **V2-Server** zum aktiven Server und den **V1-Server** zum Stagingserver. Zu diesem Zeitpunkt werden große Gruppen, die sich in einem zu synchronisierenden Bereich befinden, für Azure AD bereitgestellt, ebenso wie große vereinheitlichte Microsoft 365-Gruppen für AD bereitgestellt werden, wenn das Zurückschreiben von Gruppen aktiviert ist.
3. Bestätigen Sie, dass der **V2-Server** aktiv ist und große Gruppen erfolgreich verarbeitet. Sie können sich dafür entscheiden, es bei diesem Schritt zu belassen und den Synchronisationsprozess eine gewisse Zeit lang zu überwachen.
  >[!NOTE]
  > Wenn Sie zurück zur vorherigen Konfiguration wechseln müssen, können Sie eine Swingmigration vom **V2-Server** zurück zum **V1-Server** durchführen. Da der V1-Endpunkt keine Gruppen mit mehr als 50.000 Mitgliedern unterstützt, wird jede große Gruppe, die von Azure AD Connect in Azure AD oder in lokalem AD bereitgestellt wurde, anschließend gelöscht. 
4. Wenn Sie sicher sind, dass Sie den V2-Endpunkt verwenden möchten, aktualisieren Sie den **V1-Server**, um mit der Verwendung des V2-Endpunkts zu beginnen. 
 

## <a name="expectations-of-performance-impact"></a>Erwartete Leistungsauswirkungen  
Wenn Sie den V2-Endpunkt verwenden, sind Leistungssteigerungen von der Anzahl der synchronisierten Gruppen, der Größe dieser Gruppen und ihrer Gruppenfluktuation (Aktivität, die sich aus dem Hinzufügen und Entfernen von Benutzern als Mitglieder der Gruppe ergibt) abhängig. Wenn der neue Endpunkt verwendet wird, ohne die Anzahl, Größe oder Fluktuation der synchronisierten Gruppen zu erhöhen, sollte dies zu kürzeren Export- und Importzeiten in Azure AD führen. 
 
Die Leistungsgewinne können jedoch durch die zusätzliche Verarbeitung, die bei der Synchronisierung großer Gruppen erforderlich ist, zunichte gemacht werden. Es könnte dazu führen, dass Sie die Gesamtsynchronisierungszeit erhöhen, wenn Sie zu viele große Gruppen zum Synchronisierungsprozess hinzufügen.  

Um besser zu verstehen, wie sich das Hinzufügen der neuen Gruppen auf die Synchronisierungsleistung auswirkt, empfiehlt es sich, zunächst nur einige große Gruppen mit weniger als 100.000 Mitgliedern zu synchronisieren. Anschließend können Sie die Anzahl und die Größe von Gruppen erhöhen, indem Sie mehr von Ihnen in den Bereich über die Organisationseinheit, das Attribut oder Filterung der maximalen Gruppengröße einschließen. Die Leistungsverbesserungen werden bei den Export- und Importtasks für den Azure AD-Connector und nicht für den lokalen AD-Connector realisiert. 

## <a name="deployment-step-by-step"></a>Einzelne Schritte der Bereitstellung 
Die folgenden drei Phasen sind ein ausführliches Beispiel für die Bereitstellung des neuen V2-Endpunkts.  Verwenden Sie die Phasen als Richtlinie für Ihre Bereitstellung.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>Phase 1 – Installieren und Überprüfen von Azure AD Connect 
Es wird empfohlen, zunächst die Schritte zum Installieren oder Aktualisieren auf [Azure AD Connect Version 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) oder höher auszuführen und den Synchronisierungsprozess zu überprüfen, bevor Sie mit der zweiten Phase fortfahren, in der Sie den V2-Endpunkt aktivieren. Auf dem Server mit Azure AD Connect: 


1. [Optional] Erstellen einer Datenbanksicherung 
2. Installation oder Upgrade auf [Azure AD Connect Version 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) oder höher.
3. Überprüfen der Installation 

### <a name="phase-2--enable-the-v2-endpoint"></a>Phase 2 – Aktivieren des V2-Endpunkts 
Im nächsten Schritt wird der V2-Endpunkt aktiviert. 

> [!NOTE]
> Nachdem Sie den V2-Endpunkt für Ihren Server aktiviert haben, können Sie einige Leistungsverbesserungen für Ihre vorhandene Workload feststellen. Sie sind jedoch noch nicht in der Lage, Gruppen mit mehr als 50.000 Mitgliedern zu synchronisieren. 

Um zum V2-Endpunkt zu wechseln, führen Sie die folgenden Schritte aus: 

1. Öffnen Sie eine PowerShell-Eingabeaufforderung als Administrator. 
2. Deaktivieren Sie den Synchronisierungsplaner, nachdem Sie bestätigt haben, dass keine Synchronisierungsvorgänge ausgeführt werden: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. Importieren Sie das neue Modul: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  Wechseln Sie zum V2-Endpunkt:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
Sie haben jetzt den V2-Endpunkt für den Server aktiviert. Vergewissern Sie sich, dass nach dem Aktivieren des V2-Endpunkts keine unerwarteten Ergebnisse vorliegen, bevor Sie zur nächsten Phase wechseln, in der Sie die Beschränkung der Gruppengröße erhöhen. 
>[!NOTE]
>Die Datei-/Modulpfade können je nach Installationspfad, der bei der Installation von Azure AD Connect bereitgestellt wird, einen anderen Laufwerkbuchstaben verwenden. 


### <a name="phase-3--increase-the-group-membership-limit"></a>Phase 3 – Erhöhen der Beschränkung der Gruppenmitgliedschaft 
Nachdem Sie bestätigt haben, dass der Dienst ohne unerwartete Ergebnisse ausgeführt wird, können Sie mit der Erhöhung der Beschränkung der Gruppenmitgliedschaft fortfahren. Es wird empfohlen, die Mitgliedschaftsbeschränkung zunächst auf einen etwas höheren Wert (etwa auf 75.000 Mitglieder) zu erhöhen, um die größeren Gruppen, die mit Azure AD synchronisiert werden, anzuzeigen. Wenn Sie mit den Ergebnissen zufrieden sind, können Sie die Mitgliederbeschränkung weiter erhöhen.  

Der maximale Grenzwert beträgt 250.000 Mitglieder pro Gruppe. 

Die folgenden Schritte können verwendet werden, um die Mitgliedschaftsbeschränkung zu erhöhen:  

1. Öffnen Sie den Azure AD-Synchronisierungsregel-Editor. 
2. Wählen Sie im Editor **Ausgehend** als Richtung aus. 
3. Klicken Sie auf die Synchronisierungsregel **Out to AAD – Group Join**. 
4. Klicken Sie auf die Schaltfläche **Bearbeiten**. ![Screenshot des Fensters „Synchronisationsregeln anzeigen und verwalten“ mit der ausgewählten Regel „Out to AAD – Group Join“](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Klicken Sie auf die Schaltfläche **Ja**, um die Standardregel zu deaktivieren und eine bearbeitbare Kopie zu erstellen.
 ![Screenshot des Fensters „Bestätigung der reservierten Regel bearbeiten“ mit ausgewählter Schaltfläche „Ja“](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. Legen Sie im Popupfenster auf der Seite **Beschreibung** die Rangfolge auf einen verfügbaren Wert zwischen 1 und 99 fest. ![Screenshot des Fensters „Ausgehende Synchronisierungsregel bearbeiten“ mit hervorgehobener Option „Rangfolge“](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. Aktualisieren Sie auf der Seite **Transformationen** den Wert für **Quelle** für die **Mitglieder** transformationn und ersetzen Sie „50.000“ durch einen Wert zwischen 50.001 und 250.000. Diese Ersetzung erhöht die maximale Mitgliederzahl der Gruppen, die mit Azure AD synchronisiert werden. Es empfiehlt sich, mit einer Zahl von 100.000 zu beginnen, um zu verstehen, welche Auswirkungen die Synchronisierung großer Gruppen auf Ihre Synchronisierungsleistung hat. 
 
 **Beispiel** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Synchronisierungsregel bearbeiten](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Klicken Sie auf Speichern. 
10. Öffnen Sie eine PowerShell-Administratoreingabeaufforderung. 
11. Aktivieren Sie den Synchronisierungsplaner erneut. 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Wenn Azure AD Connect Health nicht aktiviert ist, ändern Sie die Einstellungen des Windows-Anwendungsereignisprotokolls, um die Protokolle zu archivieren, anstatt sie zu überschreiben. Die Protokolle können zur Unterstützung bei der zukünftigen Problembehandlung verwendet werden. 

>[!NOTE]
> Nachdem Sie den neuen Endpunkt aktiviert haben, werden möglicherweise zusätzliche Exportfehler für den AAD-Connector mit dem Namen „dn-attributes-failure“ angezeigt. Für jeden Fehler wird ein entsprechender Ereignisprotokolleintrag mit der ID 6949 angezeigt. Die Fehler sind informativ und weisen nicht auf ein Problem mit Ihrer Installation hin, sondern geben an, dass der Synchronisierungsprozess bestimmte Mitglieder nicht zu einer Gruppe in Azure AD hinzufügen konnte, weil das Mitgliedsobjekt selbst nicht mit Azure AD synchronisiert wurde. 

Der neue V2-Endpunktcode behandelt einige Arten von Exportfehlern geringfügig anders als der V1-Code.  Wenn Sie den V2-Endpunkt verwenden, werden möglicherweise weitere informative Fehlermeldungen angezeigt. 

>[!NOTE]
> Bei einem Upgrade von Azure AD Connect müssen Sie sicherstellen, dass die Schritte in Phase 2 erneut ausgeführt werden, da die Änderungen beim Upgradevorgang nicht beibehalten werden. 

Während nachfolgenden Erhöhungen des Gruppenmitgliedslimits in der Synchronisierungsregel **Out to AAD – Group Join** ist keine vollständige Synchronisierung erforderlich, sodass Sie die vollständige Synchronisierung unterdrücken können, indem Sie den folgenden Befehl in PowerShell ausführen. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Wenn Sie über vereinheitlichte Microsoft 365-Gruppen mit mehr als 50.000 Mitgliedern verfügen, werden die Gruppen in Azure AD Connect gelesen, und wenn Gruppenrückschreiben aktiviert ist, werden sie in das lokale AD geschrieben. 

## <a name="rollback"></a>Rollback 
Wenn Sie den V2-Endpunkt aktiviert haben und ein Rollback ausführen müssen, führen Sie die folgenden Schritte aus: 

1. Auf dem Server mit Azure AD Connect: a. [Optional] Erstellen einer Datenbanksicherung 
2. Öffnen Sie eine PowerShell-Administratoreingabeaufforderung:
3. Deaktivieren Sie den Synchronisierungsplaner, nachdem Sie bestätigt haben, dass keine Synchronisierungsvorgänge ausgeführt werden.
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Wechseln Sie zum V1-Endpunkt* 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Öffnen Sie den Azure AD-Synchronisierungsregel-Editor. 
6. Löschen Sie die bearbeitbare Kopie der Synchronisierungsregel **Out to AAD – Group Join**. 
7. Aktivieren Sie die Standardkopie der Synchronisierungsregel **Out to AAD – Group Join**. 
8. Öffnen Sie eine PowerShell-Administratoreingabeaufforderung. 
9. Aktivieren Sie den Synchronisierungsplaner erneut. 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> Wenn Sie vom V2- zum V1-Endpunkt zurückwechseln, werden Gruppen, die mit mehr als 50.000 Mitgliedern synchronisiert werden, nach der Ausführung einer vollständigen Synchronisierung gelöscht. Dies gilt sowohl für AD-Gruppen, die in Azure AD bereitgestellt werden, als auch für vereinheitlichte Microsoft 365-Gruppen, die in AD bereitgestellt werden. 

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen  
 
**Wann wird der neue Endpunkt zum Standard für Upgrades und Neuinstallationen?**   
</br>Wir planen ein neues Release von AAD Connect, das im Januar 2021 zum Download veröffentlicht werden soll. Dieses Release verwendet standardmäßig den V2-Endpunkt und ermöglicht die Synchronisierung von Gruppen mit mehr als 50.000 Mitgliedern ohne zusätzliche Konfigurationen. Anschließend wird das Release dann für das automatische Upgrade auf geeignete Server veröffentlicht.
 
## <a name="next-steps"></a>Nächste Schritte

* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
