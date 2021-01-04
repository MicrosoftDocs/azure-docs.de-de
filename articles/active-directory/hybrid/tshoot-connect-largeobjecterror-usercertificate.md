---
title: 'Azure AD Connect: LargeObject-Fehler, die auf das userCertificate-Attribut zurückzuführen sind | Microsoft-Dokumentation'
description: Dieses Thema enthält Schritte, mit denen LargeObject-Fehler behoben werden können, die durch das userCertificate-Attribut verursacht wurden.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b419e0f24201d661ad0f5f1373022ea6e9e9f
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861747"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect-Synchronisierung: Beheben von LargeObject-Fehlern, die auf das userCertificate-Attribut zurückzuführen sind

Azure AD erzwingt eine Obergrenze von **15** Zertifikatwerten für das **userCertificate**-Attribut. Wenn Azure AD Connect ein Objekt mit mehr als 15 Werten in Azure AD exportiert, gibt Azure AD einen **LargeObject**-Fehler mit folgender Meldung zurück:

>*Das bereitgestellte Objekt ist zu groß. Verringern Sie die Anzahl der Attributwerte für dieses Objekt. Der Vorgang wird bei der nächsten Synchronisierung wiederholt...*

Der LargeObject-Fehler wird möglicherweise von anderen AD-Attributen verursacht. Um sicherzustellen, dass er tatsächlich durch das userCertificate-Attribut verursacht wurde, müssen Sie entweder in der lokalen AD-Instanz oder in [Synchronization Service Manager – Metaverse Search](./how-to-connect-sync-service-manager-ui-mvsearch.md) (Synchronization Service Manager – Metaversesuche) eine Überprüfung dieses Attribut anhand des Objekts vornehmen.

Verwenden Sie zum Abrufen der Liste mit Objekten in Ihrem Mandanten mit LargeObject-Fehlern eine der folgenden Methoden:

 * Wenn Ihr Mandant für Azure AD Connect Health zur Synchronisierung verwendet werden kann, können Sie den bereitgestellten [Fehlerbericht zur Synchronisierung](./how-to-connect-health-sync.md) zurate ziehen.
 
 * Die Benachrichtigungs-E-Mail zu Verzeichnissynchronisierungsfehlern, die am Ende der einzelnen Synchronisierungszyklen gesendet wird, enthält die Liste der Objekte mit LargeObject-Fehlern. 
 * Auf der Registerkarte [Synchronization Service Manager Operations](./how-to-connect-sync-service-manager-ui-operations.md) (Synchronization Service Manager-Vorgänge) wird die Liste der Objekte mit LargeObject-Fehlern angezeigt, wenn Sie auf den Vorgang „Letzter Export nach Azure AD“ klicken.
 
## <a name="mitigation-options"></a>Abhilfeoptionen
Bis zur Behebung des LargeObject-Fehlers können weitere Attributänderungen am gleichen Objekt nicht nach Azure AD exportiert werden. Berücksichtigen Sie bei der Fehlerbehebung die folgenden Optionen:

 * Upgrade von Azure AD Connect auf Build 1.1.524.0 oder höher. In Azure AD Connect Build 1.1.524.0 wurden die Standardregeln für die Synchronisierung aktualisiert, sodass die Attribute „UserCertificate“ und „UserSMIMECertificate“ nicht exportiert werden, wenn sie mehr als 15 Werte haben. Weitere Informationen zum Aktualisieren von Azure AD Connect finden Sie im Artikel [Azure AD Connect: Aktualisieren von einer früheren Version auf die aktuelle Version](./how-to-upgrade-previous-version.md).

 * Implementieren Sie eine **ausgehende Synchronisierungsregel** in Azure AD Connect, die einen **NULL-Wert anstelle der tatsächlichen Werte für Objekte mit mehr als 15 Zertifikatwerten** exportiert. Diese Option eignet sich dann, wenn keiner der Zertifikatwerte für Objekte mit mehr als 15 Werten in Azure AD exportiert werden muss. Ausführliche Informationen zur Implementierung dieser Synchronisierungsregel finden Sie im nächsten Abschnitt: [Implementieren der Synchronisierungsregel zum Begrenzen des Exports auf das userCertificate-Attribut](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Verringern Sie die Anzahl von Zertifikatwerten für das lokale AD-Objekt (maximal 15 Werte), indem Sie Werte entfernen, die von Ihrer Organisation nicht mehr verwendet werden. Dies ist nützlich, wenn die Überfrachtung mit Attributen durch abgelaufene oder nicht verwendete Zertifikate verursacht wird. Sie können mit dem [hier verfügbaren PowerShell-Skript](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) abgelaufene Zertifikate im lokalen AD-Verzeichnis ermitteln, sichern und löschen. Vor dem Löschen der Zertifikate sollten Sie jedoch mit den Administratoren der Public Key-Infrastruktur Rücksprache halten.

 * Konfigurieren Sie Azure AD Connect so, dass das userCertificate-Attribut vom Export nach Azure AD ausgeschlossen wird. Im Allgemeinen wird diese Option nicht empfohlen, da Microsoft Online Services mit diesem Attribut möglicherweise bestimmte Szenarien ermöglicht. Dies gilt insbesondere für:
    * Das userCertificate-Attribut für das User-Objekt wird von Exchange Online- und Outlook-Clients für Nachrichtensignatur und -verschlüsselung verwendet. Weitere Informationen zu dieser Funktion finden Sie im Artikel [S/MIME für die Nachrichtensignierung und -verschlüsselung](/microsoft-365/security/office-365-security/s-mime-for-message-signing-and-encryption).

    * Das userCertificate-Attribut für das Computer-Objekt wird von Azure AD dazu verwendet, lokalen in die Domäne eingebundenen Windows 10-Geräten das Herstellen einer Verbindung mit Azure AD zu ermöglichen. Weitere Informationen zu dieser Funktion finden Sie im Artikel [Verbinden von in die Domäne eingebundenen Geräten mit Azure AD für Windows 10-Funktionen](../devices/hybrid-azuread-join-plan.md).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementieren der Synchronisierungsregel zum Begrenzen des Exports auf das userCertificate-Attribut
Zum Beheben des durch das userCertificate-Attribut verursachten LargeObject-Fehlers können Sie eine ausgehende Synchronisierungsregel in Azure AD Connect implementieren, die einen **NULL-Wert anstelle der tatsächlichen Werte für Objekte mit mehr als 15 Zertifikatwerten** exportiert. Dieser Abschnitt beschreibt die erforderlichen Schritte zum Implementieren der Synchronisierungsregel für **User**-Objekte. Die Schritte können für **Contact**- und **Computer**-Objekte angepasst werden.

> [!IMPORTANT]
> Durch den Export eines NULL-Werts werden Zertifikatwerte entfernt, die zuvor in Azure AD exportiert wurden.

Diese Schritte können wie folgt zusammengefasst werden:

1. Deaktivieren des Synchronisierungsplaners und Sicherstellen, dass derzeit keine Synchronisierung ausgeführt wird
3. Suchen der vorhandenen ausgehenden Synchronisierungsregel für das userCertificate-Attribut
4. Erstellen der erforderlichen ausgehenden Synchronisierungsregel
5. Überprüfen der neuen Synchronisierungsregel anhand eines vorhandenen Objekts mit einem LargeObject-Fehler
6. Anwenden der neuen Synchronisierungsregel auf die übrigen Objekte mit LargeObject-Fehler
7. Vergewissern, dass keine unerwarteten Änderungen auf den Export nach Azure AD warten
8. Exportieren der Änderungen in Azure AD
9. Erneutes Aktivieren des Synchronisierungsplaners

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Schritt 1: Deaktivieren des Synchronisierungsplaners und Sicherstellen, dass derzeit keine Synchronisierung ausgeführt wird
Stellen Sie sicher, dass keine Synchronisierung ausgeführt wird, während Sie eine neue Synchronisierungsregel implementieren. So vermeiden Sie, dass unbeabsichtigte Änderungen nach Azure AD exportiert werden. So deaktivieren Sie den integrierten Synchronisierungsplaner:
1. Starten Sie eine PowerShell-Sitzung auf dem Azure AD Connect-Server.

2. Deaktivieren Sie die geplante Synchronisierung durch Ausführung des Cmdlets `Set-ADSyncScheduler -SyncCycleEnabled $false`.

> [!Note]
> Die vorhergehenden Schritte gelten nur für neuere Versionen (1.1.xxx.x) von Azure AD Connect mit dem integrierten Planer. Wenn Sie ältere Versionen (1.0.xxx.x) von Azure AD Connect verwenden, die die Windows-Aufgabenplanung nutzen, oder einen eigenen benutzerdefinierten Scheduler (nicht üblich) verwenden, um eine regelmäßige Synchronisierung auszulösen, müssen Sie sie bzw. ihn deaktivieren.

1. Starten Sie **Synchronization Service Manager**, indem Sie zu „START“ > „Synchronization Service“ navigieren.

1. Wechseln Sie zur Registerkarte **Vorgänge**, und vergewissern Sie sich, dass kein Vorgang mit dem Status *In Arbeit* angezeigt wird.

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Schritt 2: Suchen der vorhandenen ausgehenden Synchronisierungsregel für das userCertificate-Attribut
Eine Synchronisierungsregel sollte vorhanden sein, die aktiviert und so konfiguriert ist, dass sie das userCertificate-Attribut für User-Objekte in Azure AD exportiert. Suchen Sie diese Synchronisierungsregel, um ihre Konfiguration für **Rangfolge** und **Bereichsfilter** zu ermitteln:

1. Starten Sie den **Synchronisierungsregel-Editor**, indem Sie zu „START“ > „Synchronisierungsregel-Editor“ navigieren.

2. Konfigurieren Sie die Suchfilter mit den folgenden Werten:

    | attribute | Wert |
    | --- | --- |
    | Direction |**Ausgehend** |
    | MV-Objekttyp |**Person** |
    | Connector |*Name des Azure AD-Connectors* |
    | Connector-Objekttyp |**user** |
    | MV-Attribut |**userCertificate** |

3. Wenn Sie Standardsynchronisierungsregeln für den Azure AD-Connector verwenden, um das userCertficate-Attribut für User-Objekte zu exportieren, sollte die Regel *Out to AAD – User ExchangeOnline* (Ausgehend an AAD – ExchangeOnline (Benutzer)) zurückgegeben werden.
4. Notieren Sie sich den Wert der **Rangfolge** dieser Synchronisierungsregel.
5. Wählen Sie die Synchronisierungsregel aus, und klicken Sie auf **Bearbeiten**.
6. Klicken Sie im Popupdialogfeld *Edit Reserved Rule Confirmation* (Bestätigung der reservierten Regel bearbeiten) auf **Nein**. (Keine Sorge, an dieser Synchronisierungsregel werden keine Änderungen vorgenommen.)
7. Wählen Sie auf dem Bearbeitungsbildschirm die Registerkarte **Bereichsfilter**.
8. Notieren Sie sich die Konfiguration des Bereichsfilters. Wenn Sie die Standardsynchronisierungsregel verwenden, sollte genau **eine Bereichsfiltergruppe mit zwei Klauseln** und folgenden Werten angezeigt werden:

    | attribute | Operator | Wert |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Benutzer |
    | cloudMastered | NOTEQUAL | True |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Schritt 3: Erstellen der erforderlichen ausgehenden Synchronisierungsregel
Die neue Synchronisierungsregel muss über denselben **Bereichsfilter** und eine **höhere Rangfolge** als die vorhandene Synchronisierungsregel verfügen. Dadurch wird sichergestellt, dass die neue Synchronisierungsregel für den gleichen Satz von Objekten gilt wie die vorhandene Synchronisierungsregel und die vorhandene Synchronisierungsregel für das UserCertificate-Attribut überschreibt. So löschen Sie die Synchronisierungsregel:
1. Klicken Sie im Synchronisierungsregel-Editor auf die Schaltfläche **Neue Regel hinzufügen**.
2. Geben Sie auf der Registerkarte **Beschreibung** die folgende Konfiguration an:

    | attribute | Wert | Details |
    | --- | --- | --- |
    | Name | *Geben Sie einen Namen ein.* | Beispiel: *Ausgehend an AAD – Benutzerdefinierte Überschreibung für „userCertificate“* |
    | BESCHREIBUNG | *Geben Sie eine Beschreibung ein.* | Beispiel: *Wenn das userCertificate-Attribut mehr als 15 Werte enthält, soll NULL exportiert werden.* |
    | Verbundenes System | *Wählen Sie den Azure AD-Connector aus.* |
    | Objekttyp des verbundenen Systems | **user** | |
    | Metaverse-Objekttyp | **person** | |
    | Verknüpfungstyp | **Join** | |
    | Rangfolge | *Wählen eine Zahl zwischen 1 und 99.* | Die ausgewählte Zahl darf nicht von einer vorhandenen Synchronisierungsregel verwendet werden und muss einen niedrigeren Wert (und damit eine höhere Rangfolge) als die vorhandene Synchronisierungsregel besitzen. |

3. Wechseln Sie zur Registerkarte **Bereichsfilter**, und implementieren Sie den gleichen Bereichsfilter, den auch die vorhandene Synchronisierungsregel verwendet.
4. Überspringen Sie die Registerkarte **Verknüpfungsregeln**.
5. Wechseln Sie zur Registerkarte **Transformationen**, um eine neue Transformation mit der folgenden Konfiguration hinzuzufügen:

    | attribute | Wert |
    | --- | --- |
    | Flowtyp |**Ausdruck** |
    | Zielattribut |**userCertificate** |
    | Quellattribut |*Verwenden Sie den folgenden Ausdruck*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Klicken Sie auf die Schaltfläche **Hinzufügen**, um die Synchronisierungsregel zu erstellen.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Schritt 4. Überprüfen der neuen Synchronisierungsregel anhand eines vorhandenen Objekts mit einem LargeObject-Fehler
Mit den folgenden Schritten können Sie sicherstellen, dass die Synchronisierungsregel für ein vorhandenes AD-Objekt mit LargeObject-Fehler ordnungsgemäß funktioniert, bevor Sie sie auf andere Objekte anwenden:
1. Wechseln Sie in Synchronization Service Manager zur Registerkarte **Vorgänge**.
2. Wählen Sie den neuesten Vorgang für den Export nach Azure AD, und klicken Sie auf eines der Objekte mit LargeObject-Fehlern.
3.  Klicken Sie im Popupbildschirm mit den Eigenschaften der Objekte des Connectorbereichs auf die Schaltfläche **Vorschau**.
4. Wählen Sie auf dem Popupbildschirm „Vorschau“ die Option **Full synchronization** (Vollständige Synchronisierung), und klicken Sie auf **Commit Preview** (Commitvorschau).
5. Schließen Sie den Bildschirm „Vorschau“ und den Bildschirm mit den Eigenschaften der Objekte des Connectorbereichs.
6. Wechseln Sie in Synchronization Service Manager zur Registerkarte **Connectors**.
7. Klicken Sie mit der rechten Maustaste auf den **Azure AD**-Connector, und wählen Sie **Run...** (Ausführen).
8. Wählen Sie im Popupfenster „Run Connector“ (Connector ausführen) den Schritt **Exportieren**, und klicken Sie auf **OK**.
9. Warten Sie, bis der Export nach Azure AD abgeschlossen ist, und vergewissern Sie sich, dass kein weiterer LargeObject-Fehler für dieses bestimmte Objekt vorliegt.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Schritt 5: Anwenden der neuen Synchronisierungsregel auf die übrigen Objekte mit LargeObject-Fehler
Nachdem die Synchronisierungsregel hinzugefügt wurde, müssen Sie eine vollständige Synchronisierung für den AD-Connector ausführen:
1. Wechseln Sie in Synchronization Service Manager zur Registerkarte **Connectors**.
2. Klicken Sie mit der rechten Maustaste auf den **AD**-Connector, und wählen Sie **Run...** (Ausführen).
3. Wählen Sie im Popupfenster „Run Connector“ (Connector ausführen) den Schritt **Full Synchronization** (Vollständige Synchronisierung), und klicken Sie auf **OK**.
4. Warten Sie, bis die vollständige Synchronisierung abgeschlossen ist.
5. Wiederholen Sie die oben genannten Schritte für die übrigen AD-Connectors, sofern mehrere AD-Connectors vorhanden sind. In der Regel sind mehrere Connectors erforderlich, wenn mehrere lokale Verzeichnisse eingerichtet sind.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Schritt 6: Vergewissern, dass keine unerwarteten Änderungen auf den Export nach Azure AD warten
1. Wechseln Sie in Synchronization Service Manager zur Registerkarte **Connectors**.
2. Klicken Sie mit der rechten Maustaste auf den **Azure AD**-Connector, und wählen Sie **Search Connector Space** (Connectorbereich durchsuchen).
3. Gehen Sie im Popupfenster „Search Connector Space“ (Connectorbereich durchsuchen) wie folgt vor:
    1. Legen Sie für den Bereich **Export steht aus** fest.
    2. Aktivieren Sie alle drei Kontrollkästchen: **Hinzufügen**, **Ändern** und **Löschen**.
    3. Klicken Sie auf die Schaltfläche **Suchen**, um alle Objekte mit Änderungen zurückzugeben, die auf den Export in Azure AD warten.
    4. Stellen Sie sicher, dass keine unerwarteten Änderungen vorhanden sind. Um die Änderungen für ein bestimmtes Objekt zu untersuchen, doppelklicken Sie auf das Objekt.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Schritt 7. Exportieren der Änderungen in Azure AD
So exportieren Sie die Änderungen in Azure AD:
1. Wechseln Sie in Synchronization Service Manager zur Registerkarte **Connectors**.
2. Klicken Sie mit der rechten Maustaste auf den **Azure AD**-Connector, und wählen Sie **Run...** (Ausführen).
4. Wählen Sie im Popupfenster „Run Connector“ (Connector ausführen) den Schritt **Exportieren**, und klicken Sie auf **OK**.
5. Warten Sie, bis der Export nach Azure AD abgeschlossen ist, und vergewissern Sie sich, dass keine weiteren LargeObject-Fehler vorliegen.

### <a name="step-8-re-enable-sync-scheduler"></a>Schritt 8: Erneutes Aktivieren des Synchronisierungsplaners
Das Problem ist nun behoben. Aktivieren Sie den Synchronisierungsplaner erneut:
1. Starten Sie eine PowerShell-Sitzung.
2. Aktivieren Sie die geplante Synchronisierung durch Ausführung des Cmdlets `Set-ADSyncScheduler -SyncCycleEnabled $true` wieder.

> [!Note]
> Die vorhergehenden Schritte gelten nur für neuere Versionen (1.1.xxx.x) von Azure AD Connect mit dem integrierten Planer. Wenn Sie ältere Versionen (1.0.xxx.x) von Azure AD Connect verwenden, die die Windows-Aufgabenplanung nutzen, oder einen eigenen benutzerdefinierten Scheduler (nicht üblich) verwenden, um eine regelmäßige Synchronisierung auszulösen, müssen Sie sie bzw. ihn deaktivieren.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
