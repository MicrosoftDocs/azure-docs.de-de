---
title: 'Azure AD Connect: Aktualisieren von einer früheren Version | Microsoft-Dokumentation'
description: Erfahren Sie etwas über die verschiedenen Methoden für das Upgrade auf die aktuelle Version von Azure Active Directory Connect, einschließlich des direkten Upgrades und der Swing-Migration.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a3e7373a8b0354a3d08debf944f2f77f1609382
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60347690"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Aktualisieren von einer früheren Version auf die aktuelle Version
In diesem Thema werden die verschiedenen Methoden beschrieben, die Sie verwenden können, um Ihre Azure Active Directory (Azure AD) Connect-Installation auf die aktuelle Version zu aktualisieren. Es wird empfohlen, immer die aktuelle Version von Azure AD Connect zu nutzen. Sie können auch die unter [Swing-Migration](#swing-migration) beschriebenen Schritte ausführen, wenn Sie wesentliche Änderungen an der Konfiguration vornehmen.

>[!NOTE]
> Derzeit kann ein Upgrade von jeder Version von Azure AD Connect auf die aktuelle Version durchgeführt werden. Direkte Upgrades von DirSync oder ADSync werden nicht unterstützt, stattdessen ist eine Swing-Migration erforderlich.  Wenn Sie ein Upgrade von DirSync durchführen möchten, finden Sie entsprechende Informationen unter [Upgrade von Azure AD-Synchronisierungstools (DirSync)](how-to-dirsync-upgrade-get-started.md) oder im Abschnitt [Swing-Migration](#swing-migration).  </br>In der Praxis treten bei Kunden mit außerordentlich alten Versionen möglicherweise Probleme auf, die nicht direkt mit Azure AD Connect zusammenhängen. Bei Servern, die seit mehreren Jahren im Einsatz sind, wurden normalerweise mehrere Patches angewandt, die nicht alle berücksichtigt werden können.  Im Allgemeinen sollten Kunden, die 12 bis 18 Monate lang kein Upgrade durchgeführt haben, stattdessen ein Swing-Upgrade durchführen, da dies die konservativste und mit am wenigsten Risiken verbundene Option ist.

Wenn Sie von DirSync aktualisieren möchten, lesen Sie stattdessen [Aktualisieren des Windows Azure Active Directory-Synchronisierungstools (DirSync)](how-to-dirsync-upgrade-get-started.md).

Es gibt verschiedene Strategien für das Upgrade von Azure AD Connect.

| Methode | BESCHREIBUNG |
| --- | --- |
| [Automatisches Upgrade](how-to-connect-install-automatic-upgrade.md) |Für Kunden mit einer Expressinstallation ist dies die einfachste Methode. |
| [Direktes Upgrade](#in-place-upgrade) |Wenn Sie über einen einzelnen Server verfügen, können Sie die Installation direkt auf demselben Server aktualisieren. |
| [Swing-Migration](#swing-migration) |Wenn Sie über zwei Server verfügen, können Sie einen der Server mit der neuen Version oder Konfiguration vorbereiten und den aktiven Server ändern, wenn Sie bereit sind. |

Informationen zu den Berechtigungen finden Sie unter [Erforderliche Berechtigungen für ein Upgrade](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Nachdem Sie Ihren neuen Azure AD Connect-Server aktiviert haben, um mit dem Synchronisieren von Änderungen mit Azure AD zu beginnen, dürfen Sie kein Rollback zur Verwendung von DirSync oder Azure AD Sync mehr durchführen. Ein Downgrade von Azure AD Connect auf Legacyclients, z.B. DirSync und Azure AD Sync, wird nicht unterstützt und kann zu Problemen führen, z.B. zu Datenverlust in Azure AD.

## <a name="in-place-upgrade"></a>Direktes Upgrade
Ein direktes Upgrade funktioniert für das Aktualisieren von Azure AD Sync oder Azure AD Connect. Es funktioniert nicht für einen Umstieg von DirSync oder für eine Lösung mit Forefront Identity Manager (FIM) + Azure AD-Connector.

Dies ist die bevorzugte Methode, wenn Sie über einen einzelnen Server und weniger als ca. 100.000 Objekte verfügen. Nach dem Upgrade werden ein vollständiger Import und eine vollständige Synchronisierung durchgeführt, wenn die standardmäßigen Synchronisierungsregeln geändert wurden. Mit dieser Methode wird sichergestellt, dass die neue Konfiguration auf alle vorhandenen Objekte im System angewendet wird. Diese Ausführung kann einige Stunden dauern. Die Dauer ist abhängig von der Anzahl der Objekte, die vom Synchronisierungsmodul berücksichtigt werden. Der normale Deltasynchronisierungsscheduler (der standardmäßig alle 30 Minuten synchronisiert) wird angehalten, aber die Kennwortsynchronisierung wird fortgesetzt. Sie sollten das direkte Upgrade am Wochenende durchführen. Wenn Sie an der Standardkonfiguration der neuen Azure AD Connect-Version keine Änderungen durchgeführt haben, wird stattdessen ein normaler Deltaimport/eine Deltasynchronisierung durchgeführt.  
![Direktes Upgrade](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Wenn Sie vordefinierte Synchronisierungsregeln geändert haben, werden diese Regeln beim Upgrade wieder auf die Standardkonfiguration zurückgesetzt. Um sicherzustellen, dass Ihre Konfiguration zwischen Upgrades erhalten bleibt, achten Sie darauf, dass die Änderungen wie in [Best Practices zum Ändern der Standardkonfiguration](how-to-connect-sync-best-practices-changing-default-configuration.md) beschrieben vorgenommen werden.

Bei einem direkten Upgrade werden unter Umständen Änderungen eingeführt, für die nach Abschluss des Upgrades bestimmte Synchronisierungsaktivitäten durchgeführt werden müssen (einschließlich vollständigem Import und vollständiger Synchronisierung). Wie Sie solche Aktivitäten zurückstellen, erfahren Sie im Abschnitt [Zurückstellen der vollständigen Synchronisierung nach dem Upgrade](#how-to-defer-full-synchronization-after-upgrade).

Bei Verwendung von Azure AD Connect mit nicht standardmäßigem Connector (z.B. Generic LDAP Connector und Generic SQL Connector) müssen Sie die entsprechende Connectorkonfiguration im [Synchronization Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) nach dem direkten Upgrade aktualisieren. Weitere Informationen zum Aktualisieren der Konfiguration des Connectors finden Sie im Artikelabschnitt [Troubleshooting](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting) (Problembehandlung). Wenn Sie die Konfiguration nicht aktualisieren, funktionieren die Import- und Exportausführungsschritte für den Connector nicht ordnungsgemäß. Sie erhalten die folgende Fehlermeldung im Ereignisprotokoll der Anwendung: *Assemblyversion in der AAD-Connector-Konfiguration („X.X.XXX.X“) ist älter als die aktuelle Version („X.X.XXX.X“) von „C:\Programme\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll.*

## <a name="swing-migration"></a>Swing-Migration
Wenn Sie über eine komplexe Bereitstellung oder viele Objekte verfügen, kann möglicherweise ein direktes Upgrade auf einem Livesystem nicht ausgeführt werden. Dieser Prozess könnte für einige Kunden mehrere Tage dauern, und während dieser Zeit werden keine Deltaänderungen verarbeitet. Sie können diese Methode auch anwenden, wenn Sie wesentliche Änderungen an Ihrer Konfiguration planen und diese vor der Übertragung in die Cloud testen möchten.

Die empfohlene Methode für diese Szenarien ist die Swing-Migration. Dafür benötigen Sie (mindestens) zwei Server: einen aktiven Server und einen Stagingserver. Der aktive Server (die durchgezogenen blauen Linien in der Abbildung unten) ist für die aktive Produktionslast verantwortlich. Der Stagingserver (mit violettem Strichlinien gezeigt) wird mit der neuen Version oder Konfiguration vorbereitet. Wenn er vollständig bereit ist, wird dieser Server zum aktiven Server gemacht. Der vorherige aktive Server, auf dem noch die alte Version oder Konfiguration installiert ist, wird jetzt zum Stagingserver und wird aktualisiert.

Die beiden Server können unterschiedliche Versionen verwenden. Sie können zum Beispiel auf dem aktiven Server, den Sie außer Betrieb nehmen möchten, Azure AD Sync und auf dem neuen Stagingserver Azure AD Connect verwenden. Wenn Sie für die Entwicklung einer neuen Konfiguration die Swing-Migration verwenden, ist es ratsam, auf beiden Servern dieselben Versionen auszuführen.  
![Stagingserver](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Einige Kunden bevorzugen für dieses Szenario die Verwendung von drei oder vier Servern. Wenn der Stagingserver aktualisiert wird, steht Ihnen in dieser Zeit kein Sicherungsserver für eine [Notfallwiederherstellung](how-to-connect-sync-staging-server.md#disaster-recovery) zur Verfügung. Mit drei oder vier Servern kann ein Satz aus primären/Standbyservern mit der neuen Version vorbereitet werden. Dadurch ist sichergestellt, dass immer ein Stagingserver zur Verfügung steht.

Diese Schritte funktionieren auch bei einem Wechsel von Azure AD Sync oder einer Lösung mit FIM und Azure AD-Connector. Diese Schritte gelten nicht für DirSync. Die entsprechende Methode einer Swing-Migration (auch parallele Bereitstellung genannt) mit den Schritten für DirSync finden Sie unter [Aktualisieren des Azure Active Directory-Synchronisierungstools (DirSync)](how-to-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Verwenden der Swing-Migration für das Upgraden
1. Wenn Sie Azure AD Connect auf beiden Servern verwenden und lediglich Konfigurationsänderungen vornehmen möchten, stellen Sie sicher, dass auf dem aktiven und dem Stagingserver die gleiche Version verwendet wird. So sind die Unterschiede später leichter zu erkennen. Wenn Sie ein Upgrade von Azure AD Sync durchführen, weisen diese Server unterschiedliche Versionen auf. Wenn Sie das Upgrade von einer älteren Version von Azure AD Connect durchführen, ist es ratsam, mit den beiden Servern zu beginnen, auf denen dieselbe Version installiert ist. Dies ist jedoch nicht zwingend erforderlich.
2. Wenn Sie eine benutzerdefinierte Konfiguration erstellt haben, die auf dem Stagingserver nicht vorhanden ist, führen Sie die Schritte unter [Verschieben einer benutzerdefinierten Konfiguration vom aktiven Server auf den Stagingserver](#move-a-custom-configuration-from-the-active-server-to-the-staging-server) aus.
3. Wenn Sie ein Upgrade von einer früheren Version von Azure AD Connect durchführen, aktualisieren Sie den Stagingserver auf die neueste Version. Wenn Sie eine Verschiebung von Azure AD Sync durchführen, installieren Sie Azure AD Connect auf Ihrem Stagingserver.
4. Führen Sie mit dem Synchronisierungsmodul einen vollständigen Import und eine vollständige Synchronisierung auf dem Stagingserver aus.
5. Stellen Sie mithilfe der Schritte unter „Überprüfen“ in [Überprüfen der Konfiguration eines Servers](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server) sicher, dass die neue Konfiguration nicht zu unerwarteten Änderungen führt. Wenn etwas nicht den Erwartungen entspricht, korrigieren Sie es, und führen Sie die Import- und Synchronisierungsschritte so lange anhand dieser Schritte erneut aus, bis die Daten stimmen.
6. Machen Sie den Stagingserver zum aktiven Server. Dies ist der letzte Schritt zum Wechseln des aktiven Servers in [Überprüfen der Konfiguration eines Servers](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Wenn Sie ein Upgrade von Azure AD Connect durchführen, aktualisieren Sie den jetzt im Stagingmodus befindlichen Server auf die aktuelle Version. Führen Sie die gleichen Schritte wie zuvor aus, um die Daten und die Konfiguration zu aktualisieren. Wenn Sie ein Upgrade von Azure AD Sync durchgeführt haben, können Sie den alten Server jetzt ausschalten und außer Betrieb nehmen.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Verschieben einer benutzerdefinierten Konfiguration vom aktiven Server auf den Stagingserver
Wenn Sie Änderungen an der Konfiguration des aktiven Servers vorgenommen haben, müssen Sie sicherstellen, dass die gleichen Änderungen auf dem Stagingserver angewendet werden. Hierbei kann Ihnen der [Azure AD Connect configuration documenter (Azure AD Connect-Konfigurationsdokumentierer)](https://github.com/Microsoft/AADConnectConfigDocumenter) behilflich sein.

Sie können die benutzerdefinierten Synchronisierungsregeln verschieben, die Sie mithilfe von PowerShell erstellt haben. Sie müssen weitere Änderungen auf beiden Systemen auf die gleiche Weise anwenden – die Änderungen können nicht migriert werden. Der [Konfigurationsdokumentierer](https://github.com/Microsoft/AADConnectConfigDocumenter) kann Ihnen dabei helfen, die beiden Systeme zu vergleichen, um sicherzustellen, dass sie identisch sind. Das Tool kann auch dabei helfen, die Schritte aus diesem Abschnitt zu automatisieren.

Sie müssen die folgenden Konfigurationen auf beiden Servern auf die gleiche Weise vornehmen:

* Verbindung mit den gleichen Gesamtstrukturen
* Filter von Domänen und Organisationseinheiten
* Die gleichen optionalen Features wie Kennwortsynchronisierung und Kennwortrückschreiben

**Verschieben benutzerdefinierter Synchronisierungsregeln**  
Gehen Sie folgendermaßen vor, um benutzerdefinierte Synchronisierungsregeln zu verschieben:

1. Öffnen Sie den **Synchronisierungsregel-Editor** auf dem aktiven Server.
2. Wählen Sie eine benutzerdefinierte Regel aus. Klicken Sie auf **Exportieren**. Ein Editor-Fenster wird geöffnet. Speichern Sie die temporäre Datei mit der Erweiterung „PS1“. Damit ist es ein PowerShell-Skript. Kopieren Sie die PS1-Datei auf den Stagingserver.  
   ![Exportieren von Synchronisierungsregeln](./media/how-to-upgrade-previous-version/exportrule.png)
3. Die Connector-GUID weicht auf dem Stagingserver ab und muss von Ihnen geändert werden. Um die GUID abzurufen, starten Sie den **Synchronisierungsregel-Editor**. Wählen Sie eine der vordefinierten Regeln aus, die das gleiche verbundene System darstellen, und klicken Sie auf **Exportieren**. Ersetzen Sie die GUID in der PS1-Datei durch die GUID des Stagingservers.
4. Führen Sie die PS1-Datei an einer PowerShell-Eingabeaufforderung aus. Dadurch wird die benutzerdefinierte Synchronisierungsregel auf dem Stagingserver erstellt.
5. Wiederholen Sie diesen Schritt für alle Ihre benutzerdefinierten Regeln.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Zurückstellen der vollständigen Synchronisierung nach dem Upgrade
Bei einem direkten Upgrade werden unter Umständen Änderungen eingeführt, für die bestimmte Synchronisierungsaktivitäten durchgeführt werden müssen (einschließlich vollständigem Import und vollständiger Synchronisierung). Bei Connectorschemaänderungen muss beispielsweise der Schritt **Vollständiger Import** und bei Änderungen an den standardmäßigen Synchronisierungsregeln der Schritt **Vollständige Synchronisierung** für die betroffenen Connectors ausgeführt werden. Während des Upgrades ermittelt Azure AD Connect die erforderlichen Synchronisierungsaktivitäten und erfasst sie als *Außerkraftsetzungen*. Im folgenden Synchronisierungszyklus greift der Synchronisierungsplaner diese Außerkraftsetzungen auf und führt sie aus. Erfolgreich ausgeführte Außerkraftsetzungen werden entfernt.

Manchmal sollen diese Außerkraftsetzungen aber möglicherweise nicht direkt nach dem Upgrade stattfinden. Beispielsweise, wenn Sie über zahlreiche synchronisierte Objekte verfügen und die Synchronisierungsschritte außerhalb der Geschäftszeiten ausgeführt werden sollen. Gehen Sie zum Entfernen dieser Außerkraftsetzungen wie folgt vor:

1. **Deaktivieren** Sie während des Upgrades die Option **Starten Sie den Synchronisierungsvorgang, nachdem die Konfiguration abgeschlossen wurde**. Dadurch wird der Synchronisierungsplaner deaktiviert und sichergestellt, dass der Synchronisierungszyklus nicht automatisch beginnt, bevor die Außerkraftsetzungen entfernt wurden.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Führen Sie nach Abschluss des Upgrades das folgende Cmdlet aus, um zu ermitteln, welche Außerkraftsetzungen hinzugefügt wurden: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Die Außerkraftsetzungen sind connectorspezifisch. Im folgenden Beispiel wurden die Schritte „Vollständiger Import“ und „Vollständige Synchronisierung“ dem lokalen AD-Connector und dem Azure AD-Connector hinzugefügt.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Notieren Sie sich die vorhandenen Außerkraftsetzungen, die hinzugefügt wurden.
   
4. Führen Sie das folgende Cmdlet aus, um die Außerkraftsetzungen für den vollständigen Import und die vollständige Synchronisierung von einem beliebigen Connector zu entfernen: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Wenn Sie die Außerkraftsetzungen für alle Connectors entfernen möchten, führen Sie das folgende PowerShell-Skript aus:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Führen Sie das folgende Cmdlet aus, um den Planer fortzusetzen: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Die erforderlichen Synchronisierungsschritte sollten zum frühestmöglichen Zeitpunkt ausgeführt werden. Sie können die Schritte entweder manuell mit dem Synchronization Service Manager ausführen oder die Außerkraftsetzungen mithilfe des Cmdlets „Set-ADSyncSchedulerConnectorOverride“ wieder hinzufügen.

Führen Sie das folgende Cmdlet aus, um die Außerkraftsetzungen für den vollständigen Import und die vollständige Synchronisierung eines beliebigen Connectors hinzuzufügen: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Problembehandlung
Der folgende Abschnitt enthält Details zur Problembehandlung sowie Informationen, die hilfreich sind, wenn beim Upgrade von Azure AD Connect Probleme auftreten.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Fehler aufgrund des fehlenden Azure Active Directory Connector während des Azure AD Connect-Upgrades

Wenn Sie Azure AD Connect von einer vorherigen Version aktualisieren, tritt zu Beginn des Upgrades unter Umständen der folgende Fehler auf: 

![Error](./media/how-to-upgrade-previous-version/error1.png)

Dieser Fehler tritt auf, weil Azure Active Directory Connector mit dem Bezeichner „b891884f-051e-4a83-95af-2544101c9083“ in der aktuellen Azure AD Connect-Konfiguration nicht vorhanden ist. Um zu überprüfen, ob dies der Fall ist, öffnen Sie ein PowerShell-Fenster, und führen Sie das Cmdlet `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083` aus:

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

Das PowerShell-Cmdlet meldet den Fehler, dass der **angegebene MA nicht gefunden** wurde.

Die Ursache dieses Fehlers ist, dass ein Upgrade der aktuellen Azure AD Connect-Konfiguration nicht unterstützt wird. 

Wenn Sie eine neuere Version von Azure AD Connect installieren möchten, gehen Sie wie folgt vor: Schließen Sie den Azure AD Connect-Assistenten, und deinstallieren Sie die vorhandene Azure AD Connect-Version. Führen Sie anschließend eine Neuinstallation der neueren Azure AD Connect-Version aus.



## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
