---
title: 'Tutorial: Azure AD Connect-Pilotcloudbereitstellung für eine vorhandene synchronisierte AD-Gesamtstruktur'
description: Hier wird erläutert, wie Sie eine Pilotcloudbereitstellung für eine Active Directory-Testgesamtstruktur durchführen, die bereits mit der Synchronisierung von Azure AD Connect (Azure Active Directory) synchronisiert wurde.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b510464145f434f761cc28ba4e1c1361f983f8b
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95237390"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Pilotcloudbereitstellung für eine vorhandene synchronisierte AD-Gesamtstruktur 

In diesem Tutorial wird Schritt für Schritt erläutert, wie Sie eine Pilotcloudbereitstellung für eine Active Directory-Testgesamtstruktur durchführen, die bereits mit der Azure Active Directory (Azure AD) Connect-Synchronisierung synchronisiert wurde.

![Erstellen](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Überlegungen
Beachten Sie Folgendes, bevor Sie dieses Tutorial durchführen:
1. Sie müssen mit den Grundlagen der Cloudbereitstellung vertraut sein. 
2. Sie müssen Version 1.4.32.0 oder höher der Azure AD Connect-Synchronisierung ausführen und die Synchronisierungsregeln entsprechend der Dokumentation konfiguriert haben. Bei der Pilotbereitstellung entfernen Sie eine Testorganisationseinheit oder -gruppe aus dem Azure AD Connect-Synchronisierungsbereich. Objekte, die Sie aus dem Bereich verschieben, werden in Azure AD gelöscht. Im Fall von Benutzerobjekten werden die Objekte in Azure AD vorläufig gelöscht, sodass sie wiederhergestellt werden können. Bei Gruppenobjekten werden die Objekte in Azure AD endgültig gelöscht, d. h. sie können nicht wiederhergestellt werden. Die Azure AD Connect-Synchronisierung verfügt über einen neuen Verknüpfungstyp, der das Löschen von Objekten in einem Pilotszenario verhindert. 
3. Stellen Sie sicher, dass „ms-ds-consistencyGUID“ für die Objekte im Pilotbereich aufgefüllt ist, damit bei der Cloudbereitstellung ein Hard Match (genaue Übereinstimmung) für die Objekte erzielt wird. 

   > [!NOTE]
   > Beachten Sie, dass die Azure AD Connect-Synchronisierung *ms-ds-consistencyGUID* für Gruppenobjekte nicht standardmäßig auffüllt.

4. Dies ist ein erweitertes Szenario. Halten Sie sich genau an die in diesem Tutorial beschriebenen Schritte.

## <a name="prerequisites"></a>Voraussetzungen
Im Folgenden finden Sie die erforderlichen Komponenten für die Durchführung dieses Tutorials:
- Eine Testumgebung mit Version 1.4.32.0 oder höher der Azure AD Connect-Synchronisierung.
- Eine Organisationseinheit oder Gruppe, die im Synchronisierungsbereich enthalten ist und für die Pilotbereitstellung verwendet werden kann. Wir empfehlen, mit einer kleinen Gruppe von Objekten zu beginnen.
- Ein Server unter Windows Server 2012 R2 oder höher, der den Bereitstellungs-Agent hostet.  Dies darf nicht der Azure AD Connect-Server sein.
- Als Quellanker für die Azure AD Connect-Synchronisierung muss entweder *objectGuid* oder *ms-ds-consistencyGUID* verwendet werden.

## <a name="update-azure-ad-connect"></a>Aktualisieren von Azure AD Connect

Sie sollten mindestens [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0 verwenden. Führen Sie die Schritte unter [Azure AD Connect: Aktualisieren von einer früheren Version auf die aktuelle Version](../hybrid/how-to-upgrade-previous-version.md) aus, um Azure AD Connect zu aktualisieren.  

## <a name="stop-the-scheduler"></a>Beenden des Schedulers
Die Azure AD Connect-Synchronisierung synchronisiert Änderungen in Ihrem lokalen Verzeichnis mithilfe eines Synchronisierungsplaners. Um benutzerdefinierte Regeln zu ändern und hinzuzufügen, möchten Sie den Synchronisierungsplaner deaktivieren, damit keine Synchronisierungen stattfinden, während Sie an den Regeln arbeiten.  Führen Sie die folgenden Schritte durch:

1.  Öffnen Sie auf dem Server, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, PowerShell mit Administratorrechten.
2.  Führen Sie `Stop-ADSyncSyncCycle` aus.  Drücken Sie die EINGABETASTE.
3.  Führen Sie `Set-ADSyncScheduler -SyncCycleEnabled $false` aus.

>[!NOTE] 
>Falls Sie Ihren eigenen benutzerdefinierten Scheduler für die Azure AD Connect-Synchronisierung ausführen, deaktivieren Sie den Scheduler. 

## <a name="create-custom-user-inbound-rule"></a>Erstellen einer benutzerdefinierten Eingangsregel für Benutzer

 1. Starten Sie den Synchronisierungsregel-Editor im Anwendungsmenü auf dem Desktop, wie unten dargestellt:</br>
 ![Menü „Synchronisierungsregel-Editor“](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. Wählen Sie in der Dropdownliste die Richtung **Eingehend** aus, und klicken Sie auf **Neue Regel hinzufügen**.
 ![Screenshot: Fenster „Synchronisationsregeln anzeigen und verwalten“ mit Hervorhebung der Option „Eingehend“ und der Schaltfläche „Neue Regel hinzufügen“](media/how-to-cloud-custom-user-rule/user1.png)</br>
 
 3. Geben Sie auf der Seite **Beschreibung** Folgendes ein, und klicken Sie anschließend auf **Weiter**:

    **Name:** Geben Sie einen aussagekräftigen Namen für die Regel ein.<br>
    **Beschreibung:** Geben Sie eine aussagekräftige Beschreibung ein.<br>
    **Verbundenes System:** Wählen Sie den AD-Connector aus, für den Sie die benutzerdefinierte Synchronisierungsregel erstellen.<br>
    **Objekttyp des verbundenen Systems:** Benutzer<br>
    **Metaverse-Objekttyp:** Person<br>
    **Verknüpfungstyp:** Join<br>
    **Rangfolge:** Geben Sie einen Wert an, der im System eindeutig ist.<br>
    **Tag:** Lassen Sie dieses Feld leer.<br>
    ![Screenshot: Seite „Eingehende Synchronisierungsregel erstellen“ (Beschreibung) mit eingegebenen Werten](media/how-to-cloud-custom-user-rule/user2.png)</br>
 
 4. Geben Sie auf der Seite **Bereichsfilter** die Organisationseinheit oder Sicherheitsgruppe ein, auf der die Pilotbereitstellung basieren soll.  Fügen Sie zum Filtern nach der Organisationseinheit den Teil des Distinguished Name (DN) hinzu, der für die Organisationseinheit steht. Die Regel wird auf alle Benutzer in dieser Organisationseinheit angewendet.  Wenn der DN auf „OU=CPUsers,DC=contoso,DC=com“ endet, fügen Sie den folgenden Filter hinzu.  Klicken Sie dann auf **Weiter**. 

    |Regel|attribute|Operator|Wert|
    |-----|----|----|-----|
    |Bereichsorganisationseinheit|DN|ENDSWITH|Distinguished Name der Organisationseinheit.|
    |Bereichsgruppe||ISMEMBEROF|Distinguished Name der Sicherheitsgruppe.|

    ![Screenshot: Seite „Eingehende Synchronisierungsregel erstellen“ (Bereichsfilter) mit eingegebenem Bereichsfilterwert](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. Klicken Sie auf der Seite **Verknüpfungsregeln** auf **Weiter**.
 6. Fügen Sie auf der Seite **Transformationen** eine Transformation vom Typ „Konstante“ hinzu, und setzen Sie das Attribut „cloudNoFlow“ auf „True“. Klicken Sie auf **Hinzufügen**.
 ![Screenshot: Seite „Eingehende Synchronisierungsregel erstellen“ (Transformationen) mit hinzugefügtem Konstantentransformationsfluss](media/how-to-cloud-custom-user-rule/user4.png)</br>

Diese Schritte müssen für alle Objekttypen (Benutzer, Gruppen und Kontakte) ausgeführt werden. Wiederholen Sie die Schritte pro konfiguriertem AD-Connector bzw. pro AD-Gesamtstruktur. 

## <a name="create-custom-user-outbound-rule"></a>Erstellen einer benutzerdefinierten Ausgangsregel für Benutzer

 1. Wählen Sie in der Dropdownliste die Richtung **Ausgehend** aus, und klicken Sie auf **Regel hinzufügen**.
 ![Screenshot: Richtungsauswahl „Ausgehend“ und hervorgehobene Schaltfläche „Neue Regel hinzufügen“](media/how-to-cloud-custom-user-rule/user5.png)</br>
 
 2. Geben Sie auf der Seite **Beschreibung** Folgendes ein, und klicken Sie anschließend auf **Weiter**:

    **Name:** Geben Sie einen aussagekräftigen Namen für die Regel ein.<br>
    **Beschreibung:** Geben Sie eine aussagekräftige Beschreibung ein.<br>
    **Verbundenes System:** Wählen Sie den Azure AD-Connector aus, für den Sie die benutzerdefinierte Synchronisierungsregel erstellen.<br>
    **Objekttyp des verbundenen Systems:** Benutzer<br>
    **Metaverse-Objekttyp:** Person<br>
    **Verknüpfungstyp:** JoinNoFlow<br>
    **Rangfolge:** Geben Sie einen Wert an, der im System eindeutig ist.<br>
    **Tag:** Lassen Sie dieses Feld leer.<br>
    
    ![Screenshot: Seite „Beschreibung“ mit eingegebenen Eigenschaften](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. Wählen Sie auf der Seite **Bereichsfilter** das Attribut **cloudNoFlow**, den Operator „Gleich“ und den Wert **True** aus. Klicken Sie dann auf **Weiter**.
 ![Benutzerdefinierte Regel](media/how-to-cloud-custom-user-rule/user7.png)</br>
 
 4. Klicken Sie auf der Seite **Verknüpfungsregeln** auf **Weiter**.
 5. Klicken Sie auf der Seite **Transformationen** auf **Hinzufügen**.

Diese Schritte müssen für alle Objekttypen (Benutzer, Gruppen und Kontakte) ausgeführt werden.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Installieren des Azure AD Connect-Bereitstellungs-Agents
1. Melden Sie sich bei dem Server an, den Sie mit den Berechtigungen eines Unternehmensadministrators verwenden werden.  Wenn Sie das Tutorial [Grundlegende Active Directory-Umgebung](tutorial-basic-ad-azure.md) ausführen, ist dies der Server CP1.
2. Laden Sie den Agent für die Azure AD Connect-Cloudbereitstellung herunter. Eine entsprechende Anleitung finden Sie [hier](how-to-install.md#install-the-agent).
3. Führen Sie den Azure AD Connect-Cloudbereitstellungs-Agent aus (AADConnectProvisioningAgent.Installer).
3. **Akzeptieren** Sie auf dem Begrüßungsbildschirm die Lizenzbedingungen, und klicken Sie auf **Installieren**.</br>
![Screenshot: Begrüßungsbildschirm des Microsoft Azure AD Connect-Bereitstellungs-Agents](media/how-to-install/install-1.png)</br>

4. Nach Abschluss dieses Vorgangs wird der Konfigurations-Assistent gestartet.  Melden Sie sich mit dem Konto Ihres globalen Azure AD-Administrators an.
5. Klicken Sie auf dem Bildschirm **Connect Active Directory** (Active Directory verbinden) auf **Verzeichnis hinzufügen**, und melden Sie sich dann mit Ihrem Active Directory-Administratorkonto an.  Dadurch wird Ihr lokales Verzeichnis hinzugefügt.  Klicken Sie auf **Weiter**.</br>
![Screenshot: Bildschirm „Active Directory verbinden“ mit eingegebenem Verzeichniswert](media/how-to-install/install-3a.png)</br>

6. Klicken Sie auf dem Bildschirm **Konfiguration abgeschlossen** auf **Bestätigen**.  Dadurch wird der Agent registriert und neu gestartet.</br>
![Screenshot: Bildschirm „Konfiguration abgeschlossen“ mit ausgewählter Schaltfläche „Bestätigen“](media/how-to-install/install-4a.png)</br>

7. Sobald dieser Vorgang abgeschlossen ist, sollte der Hinweis angezeigt werden, dass **die Agent-Konfiguration erfolgreich überprüft wurde**.  Sie können dann auf **Beenden** klicken.</br>
![Bildschirm „Willkommen“](media/how-to-install/install-5.png)</br>
8. Falls der erste Begrüßungsbildschirm weiterhin angezeigt wird, klicken Sie auf **Schließen**.

## <a name="verify-agent-installation"></a>Überprüfen der Agent-Installation
Die Agent-Überprüfung erfolgt im Azure-Portal und auf dem lokalen Server, auf dem der Agent ausgeführt wird.

### <a name="azure-portal-agent-verification"></a>Agent-Überprüfung im Azure-Portal
Führen Sie die folgenden Schritte aus, um zu überprüfen, ob der Agent von Azure erkannt wird:

1. Melden Sie sich beim Azure-Portal an.
2. Wählen Sie auf der linken Seite **Azure Active Directory** aus, klicken Sie auf **Azure AD Connect**, und wählen Sie im mittleren Bereich **Bereitstellung verwalten (Vorschau)** aus.</br>
![Azure portal](media/how-to-install/install-6.png)</br>

3.  Klicken Sie auf dem Bildschirm **Azure AD-Bereitstellung (Vorschau)** auf **Alle Agents überprüfen**.
![Azure AD-Bereitstellung](media/how-to-install/install-7.png)</br>
 
4. Auf dem Bildschirm **On-premises provisioning agents** (Lokale Bereitstellungs-Agents) werden die von Ihnen installierten Agents angezeigt.  Vergewissern Sie sich, dass der betreffende Agent aufgeführt wird und als **Deaktiviert** markiert ist.  Der Agent ist in der Liste der ![Bereitstellungs-Agents](media/how-to-install/verify-1.png) standardmäßig deaktiviert.</br>

### <a name="on-the-local-server"></a>Auf dem lokalen Server
Führen Sie die folgenden Schritte aus, um sicherzustellen, dass der Agent ausgeführt wird:

1.  Melden Sie sich beim Server mit einem Administratorkonto an.
2.  Öffnen Sie die Seite **Dienste**, indem Sie zu dieser Seite oder zu „Start/Run/Services.msc“ navigieren.
3.  Vergewissern Sie sich, dass unter **Dienste** die Dienste **Microsoft Azure AD Connect Agent Updater** und **Microsoft Azure AD Connect-Bereitstellungs-Agent** angezeigt werden und ihr Status **Wird ausgeführt** lautet.
![Dienste](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Konfigurieren der Azure AD Connect-Cloudbereitstellung
Gehen Sie wie folgt vor, um die Bereitstellung zu konfigurieren:

 1. Melden Sie sich beim Azure AD-Portal an.
 2. Klicken Sie auf **Azure Active Directory**.
 3. Klicken Sie auf **Azure AD Connect**.
 4. Wählen Sie **Bereitstellung verwalten (Vorschau)** 
 ![Screenshot mit dem Link „Bereitstellung verwalten (Vorschau)“ aus.](media/how-to-configure/manage1.png)</br>
 5.  Klicken Sie auf **Neue Konfiguration**
 ![Screenshot des Bildschirms „Azure AD-Bereitstellung (Vorschau)“ mit hervorgehobenem Link „Neue Konfiguration“.](media/tutorial-single-forest/configure1.png)</br>
 6.  Geben Sie im Konfigurationsbildschirm eine **E-Mail-Adresse für Benachrichtigungen** ein, verschieben Sie den Selektor auf **Aktivieren**, und klicken Sie auf **Speichern**.
 ![Screenshot des Bildschirms „Konfigurieren“ mit ausgefüllter Benachrichtigungs-E-Mail und ausgewählter Option „Aktivieren“](media/tutorial-single-forest/configure2.png)</br>
 7. Wählen Sie unter **Konfigurieren** die Option **Alle Benutzer** aus, um den Bereich der Konfigurationsregel zu ändern.
 ![Screenshot des Bildschirms „Konfigurieren“, in dem neben „Bereichsbenutzer“ die Option „Alle Benutzer“ hervorgehoben ist](media/how-to-configure/scope2.png)</br>
 8. Ändern Sie auf der rechten Seite den Bereich, sodass er die gerade erstellte Organisationseinheit „OU=CPUsers,DC=contoso,DC=com“ enthält.
 ![Screenshot des Bildschirms „Bereichsbenutzer“, in dem der in die von Ihnen erstellte OE geänderte Bereich hervorgehoben ist](media/tutorial-existing-forest/scope2.png)</br>
 9.  Klicken Sie auf **Fertig** und dann auf **Speichern**.
 10. Der Bereich sollte nun auf eine Organisationseinheit festgelegt sein. 
 ![Screenshot des Bildschirms „Konfigurieren“, in dem neben „Bereichsbenutzer“ die Option „1 Organisationseinheit“ hervorgehoben ist](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>Überprüfen, ob Benutzer durch die Cloudbereitstellung bereitgestellt werden
Nun überprüfen Sie, ob die Benutzer, die in unserem lokalen Verzeichnis enthalten waren, synchronisiert wurden und jetzt in unserem Azure AD-Mandanten vorhanden sind.  Seien Sie sich im Klaren darüber, dass dies einige Stunden dauern kann.  Gehen Sie wie folgt vor, um zu überprüfen, ob Benutzer durch die Cloudbereitstellung bereitgestellt werden:

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), und melden Sie sich mit einem Konto an, das über ein Azure-Abonnement verfügt.
2. Wählen Sie im linken Bereich **Azure Active Directory** aus.
3. Klicken Sie auf **Azure AD Connect**.
4. Klicken Sie auf **Bereitstellung verwalten (Vorschau)** .
5. Klicken Sie auf die Schaltfläche **Protokolle**.
6. Suchen Sie nach einem Benutzernamen, um zu überprüfen, ob der Benutzer durch die Cloudbereitstellung bereitgestellt wurde.

Außerdem können Sie überprüfen, ob der Benutzer und die Gruppe in Azure AD vorhanden sind.

## <a name="start-the-scheduler"></a>Starten des Schedulers
Die Azure AD Connect-Synchronisierung synchronisiert Änderungen in Ihrem lokalen Verzeichnis mithilfe eines Synchronisierungsplaners. Nachdem Sie die Regeln geändert haben, können Sie den Synchronisierungsplaner nun neu starten.  Führen Sie die folgenden Schritte durch:

1.  Öffnen Sie auf dem Server, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, PowerShell mit Administratorrechten.
2.  Führen Sie `Set-ADSyncScheduler -SyncCycleEnabled $true` aus.
3.  Führen Sie `Start-ADSyncSyncCycle` aus.  Drücken Sie die EINGABETASTE.  

>[!NOTE] 
>Wenn Sie Ihren eigenen benutzerdefinierten Scheduler für die Azure AD Connect-Synchronisierung ausführen, aktivieren Sie den Scheduler. 

Sobald der Scheduler aktiviert ist, beendet Azure AD Connect den Export jeglicher Änderungen an Objekten mit `cloudNoFlow=true` im Metaverse, es sei denn ein Verweisattribut (z. B. „manager“) wird aktualisiert. Falls eine Verweisattributaktualisierung für das Objekt vorhanden ist, ignoriert Azure AD Connect das `cloudNoFlow`-Signal und exportiert alle Aktualisierungen für das Objekt.

## <a name="something-went-wrong"></a>Wenn Fehler auftreten
Falls die Pilotbereitstellung nicht wie erwartet funktioniert, können Sie wie folgt zum Setup der Azure AD Connect-Synchronisierung zurückkehren:
1.  Deaktivieren Sie die Bereitstellungskonfiguration im Azure-Portal. 
2.  Deaktivieren Sie alle benutzerdefinierten Synchronisierungsregeln, die Sie mit dem Synchronisierungsregel-Editor für die Cloudbereitstellung erstellt haben. Durch das Deaktivieren sollten alle Connectors vollständig synchronisiert werden.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Konfigurieren der Azure AD Connect-Synchronisierung zum Ausschließen der Pilotorganisationseinheit
Wenn Sie sich vergewissert haben, dass Benutzer aus der Pilotorganisationseinheit in der Cloudbereitstellung erfolgreich verwaltet werden, können Sie Azure AD Connect neu konfigurieren, um die oben erstellte Pilotorganisationsheit auszuschließen.  Der Agent für die Cloudbereitstellung führt ab sofort die Synchronisierung dieser Benutzer aus.  Gehen Sie wie folgt vor, um den Bereich für Azure AD Connect festzulegen.

 1. Doppelklicken Sie auf dem Server, auf dem Azure AD Connect ausgeführt wird, auf das Symbol für Azure AD Connect.
 2. Klicken Sie auf **Konfigurieren**.
 3. Wählen Sie **Synchronisierungsoptionen anpassen** aus, und klicken Sie auf „Weiter“.
 4. Melden Sie sich bei Azure AD an, und klicken Sie auf **Weiter**.
 5. Klicken Sie auf dem Bildschirm **Verzeichnisse verbinden** auf **Weiter**.
 6. Wählen Sie auf dem Bildschirm **Filtern von Domänen und Organisationseinheiten** die Option **Ausgewählte Domänen und Organisationseinheiten synchronisieren** aus.
 7. Erweitern Sie Ihre Domäne, und **deaktivieren** Sie das Kontrollkästchen für die Organisationseinheit **CPUsers**.  Klicken Sie auf **Weiter**.
![scope](media/tutorial-existing-forest/scope1.png)</br>
 9. Klicken Sie auf dem Bildschirm **Optionale Features** auf **Weiter**.
 10. Klicken Sie auf dem Bildschirm **Bereit zur Konfiguration** auf **Konfigurieren**.
 11. Klicken Sie nach Abschluss des Vorgangs auf **Beenden**. 

## <a name="next-steps"></a>Nächste Schritte 

- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Was ist die Azure AD Connect-Cloudbereitstellung?](what-is-cloud-provisioning.md)

