---
title: Problembehandlung der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zur Behandlung von Problemen mit der Kennworthashsynchronisierung.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6feed11fcfc597658f3ec148b5dd18bb7e3f8f83
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60383092"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Problembehandlung für die Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung

Dieses Thema enthält Schritte zum Behandeln von Problemen mit der Kennworthashsynchronisierung. Wenn Kennwörter nicht wie erwartet synchronisiert werden, kann dies für eine Teilmenge von Benutzern oder für alle Benutzer der Fall sein.

Verwenden Sie für die Bereitstellung von Azure Active Directory (Azure AD) Connect mit Version 1.1.614.0 oder höher die Problembehandlungsaufgabe im Assistenten, um Probleme bei der Kennworthashsynchronisierung zu behandeln:

* Wenn das Problem darin besteht, dass gar keine Kennwörter synchronisiert werden, lesen Sie den Abschnitt [Kennwörter werden nicht synchronisiert: Problembehandlung mit der Problembehandlungsaufgabe](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task).

* Wenn Sie ein Problem mit einzelnen Objekte haben, lesen Sie den Abschnitt [Ein einzelnes Objekt synchronisiert keine Kennwörter: Problembehandlung mit der Problembehandlungsaufgabe](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task).

Für die Bereitstellung mit Version 1.1.524.0 oder höher gibt es nun ein Diagnose-Cmdlet, mit dem Sie Probleme bei der Kennworthashsynchronisierung behandeln können:

* Wenn das Problem darin besteht, dass gar keine Kennwörter synchronisiert werden, lesen Sie den Abschnitt [Kennwörter werden nicht synchronisiert: Problembehandlung mit dem Diagnose-Cmdlet](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet).

* Wenn Sie ein Problem mit einzelnen Objekte haben, lesen Sie den Abschnitt [Ein einzelnes Objekt synchronisiert keine Kennwörter: Problembehandlung mit dem Diagnose-Cmdlet](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet).

Bei älteren Versionen von Azure AD Connect-Bereitstellungen:

* Wenn das Problem darin besteht, dass gar keine Kennwörter synchronisiert werden, lesen Sie den Abschnitt [Kennwörter werden nicht synchronisiert: Schritte zur manuellen Problembehandlung](#no-passwords-are-synchronized-manual-troubleshooting-steps).

* Wenn Sie ein Problem mit einzelnen Objekte haben, lesen Sie den Abschnitt [Ein einzelnes Objekt synchronisiert keine Kennwörter: Schritte zur manuellen Problembehandlung](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps).



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Kennwörter werden nicht synchronisiert: Problembehandlung mit der Problembehandlungsaufgabe

Mithilfe der Problembehandlungsaufgabe können Sie herausfinden, warum keine Kennwörter synchronisiert werden.

> [!NOTE]
> Die Problembehandlungsaufgabe ist nur für Azure AD Connect Version 1.1.614.0 oder höher verfügbar.

### <a name="run-the-troubleshooting-task"></a>Ausführen der Problembehandlungsaufgabe

So behandeln Sie Probleme für den Fall, dass keine Kennwörter synchronisiert werden:

1. Öffnen Sie mit der Option **Als Administrator ausführen** eine neue Windows PowerShell-Sitzung auf Ihrem Azure AD Connect-Server.

2. Führen Sie `Set-ExecutionPolicy RemoteSigned` oder `Set-ExecutionPolicy Unrestricted` aus.

3. Starten Sie den Azure AD Connect-Assistenten.

4. Navigieren Sie zur Seite **Weitere Aufgaben**, wählen Sie **Problembehandlung**, und klicken Sie auf **Weiter**.

5. Klicken Sie auf der Seite „Problembehandlung“ auf **Starten**, um das Menü zur Problembehandlung in PowerShell zu starten.

6. Wählen Sie im Hauptmenü **Problembehandlung bei der Kennworthashsynchronisierung**.

7. Wählen Sie im Untermenü **Die Kennworthashsynchronisierung funktioniert überhaupt nicht**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Grundlegendes zu den Ergebnissen der Problembehandlungsaufgabe

Die Problembehandlungsaufgabe führt die folgenden Überprüfungen durch:

* Überprüft, ob die Kennworthashsynchronisierungsfunktion für Ihren Azure AD-Mandanten aktiviert ist.

* Überprüft, dass der Azure AD Connect-Server sich nicht im Stagingmodus befindet.

* Bei jedem vorhandenen lokalen Active Directory-Connector (entsprechend einer vorhandenen Active Directory-Gesamtstruktur):

   * Überprüft, ob die Kennworthashsynchronisierungsfunktion aktiviert ist.
   
   * Sucht nach Kennworthashsynchronisierungs-Taktereignissen in den Windows-Anwendungsereignisprotokollen.

   * Bei jeder Active Directory-Domäne unter dem lokalen Active Directory-Connector:

      * Überprüft, dass die Domäne über den Azure AD Connect-Server erreichbar ist.

      * Überprüft, ob der Benutzername, das Kennwort und die für die Kennworthashsynchronisierung erforderlichen Berechtigungen für die AD DS-Konten (Active Directory Domain Services), die vom lokalen Active Directory-Connector verwendet werden, korrekt sind.

Die folgende Abbildung zeigt die Ergebnisse des Cmdlets für eine einzelne Domäne in einer lokalen Active Directory-Topologie:

![Diagnoseausgabe für die Kennworthashsynchronisierung](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

Im restlichen Teil dieses Abschnitts werden bestimmte Ergebnisse, die von der Aufgabe zurückgegeben werden, und die entsprechenden Probleme beschrieben.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>Kennworthashsynchronisierungs-Feature ist nicht aktiviert

Wenn Sie die Kennworthashsynchronisierung mithilfe des Azure AD Connect-Assistenten nicht aktiviert haben, wird folgende Fehlermeldung zurückgegeben:

![Kennworthashsynchronisierung ist nicht aktiviert](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Azure AD Connect-Server befindet sich im Stagingmodus

Wenn sich der Azure AD Connect-Server im Stagingmodus befindet, ist die Kennworthashsynchronisierung vorübergehend deaktiviert, und folgende Fehlermeldung wird zurückgegeben:

![Azure AD Connect-Server befindet sich im Stagingmodus](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Keine Kennworthashsynchronisierungs-Taktereignisse

Jeder lokale Active Directory-Connector ist mit einem eigenen Kennworthashsynchronisierungskanal ausgestattet. Wenn der Kennworthashsynchronisierungs-Kanal eingerichtet ist und keine Kennwortänderungen synchronisiert werden, wird alle 30 Minuten ein Taktereignis (EventId 654) unter dem Windows-Anwendungsereignisprotokoll generiert. Bei jedem lokalen Active Directory-Connector sucht das Cmdlet für die letzten drei Stunden nach entsprechenden Taktereignissen. Wenn kein Taktereignis gefunden wird, wird folgende Fehlermeldung zurückgegeben:

![Kein Kennworthashsynchronisierungs-Taktereignis](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS-Konto weist nicht die richtigen Berechtigungen auf

Wenn das AD DS-Konto, das vom lokalen Active Directory-Connector zum Synchronisieren von Kennworthashes verwendet wird, nicht über die entsprechenden Berechtigungen verfügt, wird folgende Fehlermeldung zurückgegeben:

![Falsche Anmeldeinformationen](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Falscher Benutzername oder falsches Kennwort für das AD DS-Konto

Wenn das AD DS-Konto, das vom lokalen Active Directory-Connector zum Synchronisieren von Kennworthashes verwendet wird, mit einem falschen Benutzernamen oder einem falschen Kennwort versehen ist, wird folgende Fehlermeldung zurückgegeben:

![Falsche Anmeldeinformationen](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Ein einzelnes Objekt synchronisiert keine Kennwörter: Problembehandlung mit der Problembehandlungsaufgabe

Mithilfe der Problembehandlungsaufgabe können Sie bestimmen, warum ein Objekt keine Kennwörter synchronisiert.

> [!NOTE]
> Die Problembehandlungsaufgabe ist nur für Azure AD Connect Version 1.1.614.0 oder höher verfügbar.

### <a name="run-the-diagnostics-cmdlet"></a>Ausführen des Diagnose-Cmdlets

So behandeln Sie Probleme für ein bestimmtes Benutzerobjekt:

1. Öffnen Sie mit der Option **Als Administrator ausführen** eine neue Windows PowerShell-Sitzung auf Ihrem Azure AD Connect-Server.

2. Führen Sie `Set-ExecutionPolicy RemoteSigned` oder `Set-ExecutionPolicy Unrestricted` aus.

3. Starten Sie den Azure AD Connect-Assistenten.

4. Navigieren Sie zur Seite **Weitere Aufgaben**, wählen Sie **Problembehandlung**, und klicken Sie auf **Weiter**.

5. Klicken Sie auf der Seite „Problembehandlung“ auf **Starten**, um das Menü zur Problembehandlung in PowerShell zu starten.

6. Wählen Sie im Hauptmenü **Problembehandlung bei der Kennworthashsynchronisierung**.

7. Wählen Sie im Untermenü **Kennwort ist für ein bestimmtes Benutzerkonto nicht synchronisiert**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Grundlegendes zu den Ergebnissen der Problembehandlungsaufgabe

Die Problembehandlungsaufgabe führt die folgenden Überprüfungen durch:

* Überprüft den Status des Active Directory-Objekts im Active Directory-Connectorbereich, in der Metaverse und im Azure AD-Connectorbereich.

* Überprüft, ob Synchronisierungsregeln für die Kennworthashsynchronisierung aktiviert sind und auf das Active Directory-Objekt angewendet werden.

* Versucht, die Ergebnisse des letzten Versuchs abzurufen und anzuzeigen, bei dem das Kennwort für das Objekt synchronisiert wurde.

Die folgende Abbildung zeigt die Ergebnisse des Cmdlets bei der Behandlung von Problemen mit der Kennworthashsynchronisierung für ein einzelnes Objekt:

![Diagnoseausgabe für die Kennworthashsynchronisierung – einzelnes Objekt](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

Im restlichen Teil dieses Abschnitts werden bestimmte Ergebnisse, die vom Cmdlet zurückgegeben werden, und die entsprechenden Probleme beschrieben.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Active Directory-Objekt wird nicht nach Azure AD exportiert

Bei der Kennworthashsynchronisierung für dieses lokale Active Directory-Konto ist ein Fehler aufgetreten, da kein entsprechendes Objekt im Azure AD-Mandanten vorhanden ist. Die folgende Fehlermeldung wird zurückgegeben:

![Azure AD-Objekt fehlt](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Benutzer besitzt ein temporäres Kennwort

Gegenwärtig bietet Azure AD Connect keine Unterstützung für die Synchronisierung temporärer Kennwörter mit Azure AD. Ein Kennwort gilt als temporär, wenn die Option **Kennwort bei der nächsten Anmeldung ändern** für den lokalen Active Directory-Benutzer festgelegt wird. Die folgende Fehlermeldung wird zurückgegeben:

![Temporäres Kennwort wurde nicht exportiert](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Ergebnisse des letzten Versuchs, das Kennwort zu synchronisieren, sind nicht verfügbar

Standardmäßig speichert Azure AD Connect sieben Tage lang die Ergebnisse der Kennworthashsynchronisierungs-Versuche. Wenn keine Ergebnisse für das ausgewählten Active Directory-Objekt verfügbar sind, wird die folgende Warnung zurückgegeben:

![Diagnoseausgabe für ein einzelnes Objekt – kein Kennwortsynchronisierungsverlauf](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Kennwörter werden nicht synchronisiert: Problembehandlung mit dem Diagnose-Cmdlet

Mithilfe des Cmdlets `Invoke-ADSyncDiagnostics` können Sie herausfinden, warum keine Kennwörter synchronisiert werden.

> [!NOTE]
> Das Cmdlet `Invoke-ADSyncDiagnostics` ist nur für Azure AD Connect Version 1.1.524.0 oder höher verfügbar.

### <a name="run-the-diagnostics-cmdlet"></a>Ausführen des Diagnose-Cmdlets

So behandeln Sie Probleme für den Fall, dass keine Kennwörter synchronisiert werden:

1. Öffnen Sie mit der Option **Als Administrator ausführen** eine neue Windows PowerShell-Sitzung auf Ihrem Azure AD Connect-Server.

2. Führen Sie `Set-ExecutionPolicy RemoteSigned` oder `Set-ExecutionPolicy Unrestricted` aus.

3. Führen Sie `Import-Module ADSyncDiagnostics`aus.

4. Führen Sie `Invoke-ADSyncDiagnostics -PasswordSync`aus.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Ein einzelnes Objekt synchronisiert keine Kennwörter: Problembehandlung mit dem Diagnose-Cmdlet

Mithilfe des Cmdlets `Invoke-ADSyncDiagnostics` können Sie bestimmen, warum ein Objekt keine Kennwörter synchronisiert.

> [!NOTE]
> Das Cmdlet `Invoke-ADSyncDiagnostics` ist nur für Azure AD Connect Version 1.1.524.0 oder höher verfügbar.

### <a name="run-the-diagnostics-cmdlet"></a>Ausführen des Diagnose-Cmdlets

So behandeln Sie Probleme für den Fall, dass keine Kennwörter für einen Benutzer synchronisiert werden:

1. Öffnen Sie mit der Option **Als Administrator ausführen** eine neue Windows PowerShell-Sitzung auf Ihrem Azure AD Connect-Server.

2. Führen Sie `Set-ExecutionPolicy RemoteSigned` oder `Set-ExecutionPolicy Unrestricted` aus.

3. Führen Sie `Import-Module ADSyncDiagnostics`aus.

4. Führen Sie das folgende Cmdlet aus:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Beispiel:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Kennwörter werden nicht synchronisiert: Schritte zur manuellen Problembehandlung

Führen Sie folgende Schritte durch, um festzustellen, warum keine Kennwörter synchronisiert werden:

1. Ist der Connect-Server im [Stagingmodus](how-to-connect-sync-staging-server.md)? Ein Server im Stagingmodus synchronisiert keine Kennwörter.

2. Führen Sie das Skript im Abschnitt [Abrufen des Status der Kennwortsynchronisierungseinstellungen](#get-the-status-of-password-sync-settings) aus. Dadurch erhalten Sie eine Übersicht über die Konfiguration der Kennwortsynchronisierung.  

    ![PowerShell-Skriptausgabe aus den Kennwortsynchronisierungseinstellungen](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Wenn die Funktion nicht in Azure AD aktiviert ist oder der Synchronisierungskanalstatus nicht aktiviert ist, führen Sie den Assistenten zum Installieren von Connect aus. Wählen Sie **Synchronisierungsoptionen anpassen** aus, und deaktivieren Sie die Kennwortsynchronisierung. Durch diese Änderung wird das Feature vorübergehend deaktiviert. Führen Sie anschließend den Assistenten erneut aus, und aktivieren Sie die Kennwortsynchronisierung wieder. Führen Sie das Skript noch einmal aus, um sicherzustellen, dass die Konfiguration korrekt ist.

4. Suchen Sie im Ereignisprotokoll nach Fehlern. Suchen Sie nach den folgenden Ereignissen, die auf ein Problem hinweisen würden:
    * Quelle: „Verzeichnissynchronisierung“ ID: 0, 611, 652, 655 Wenn diese Ereignisse angezeigt werden, liegt ein Konnektivitätsproblem vor. Die Ereignisprotokollmeldung enthält Gesamtstrukturinformationen, die ein Problem aufweisen. Weitere Informationen finden Sie unter [Konnektivitätsproblem](#connectivity problem).

5. Wenn kein Takt angezeigt wird oder nichts funktioniert hat, führen Sie das Skript unter [Auslösen einer vollständigen Synchronisierung aller Kennwörter](#trigger-a-full-sync-of-all-passwords) aus. Führen Sie das Skript nur einmal aus.

6. Lesen Sie den Abschnitt „Behandlung eines Problems mit einem einzelnen Objekt, das keine Kennwörter synchronisiert“.

### <a name="connectivity-problems"></a>Konnektivitätsprobleme

Besteht eine Konnektivität mit Azure AD?

Verfügt das Konto über die erforderlichen Berechtigungen zum Lesen des Kennworthashes in allen Domänen? Wenn Sie Connect mit Express-Einstellungen installiert haben, sollten die Berechtigungen bereits richtig sein. 

Wenn Sie eine benutzerdefinierte Installation verwendet haben, legen Sie die Berechtigungen wie folgt manuell fest:
    
1. Um nach dem vom Active Directory-Connector verwendete Konto zu suchen, starten Sie den **Synchronization Service Manager**. 
 
2. Navigieren Sie zu **Connectors**, und suchen Sie dann nach der lokalen Active Directory-Gesamtstruktur, für die Sie eine Problembehandlung durchführen. 
 
3. Wählen Sie den Connector aus, und klicken Sie dann auf **Eigenschaften**. 
 
4. Navigieren Sie zu **Mit Active Directory-Gesamtstruktur verbinden**.  
    
    ![Vom Active Directory-Connector verwendetes Konto](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Notieren Sie sich den Benutzernamen und die Domäne, in der sich das Konto befindet.
    
5. Starten Sie **Active Directory-Benutzer und -Computer**, und überprüfen Sie dann, ob für das zuvor gesuchte Konto in der Gesamtstruktur auf der Stammebene aller Domänen die folgenden Berechtigungen festgelegt sind:
    * Verzeichnisänderungen replizieren
    * Verzeichnisänderungen replizieren: Alle

6. Sind die Domänencontroller durch Azure AD Connect erreichbar? Wenn der Connect-Server mit keinem der Domänencontroller eine Verbindung herstellen kann, konfigurieren Sie **Nur bevorzugten Domänencontroller verwenden**.  
    
    ![Vom Active Directory-Connector verwendeter Domänencontroller](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  
    
7. Navigieren Sie zurück zum **Synchronization Service Manager** und **Verzeichnispartition konfigurieren**. 
 
8. Wählen Sie unter **Verzeichnispartitionen auswählen** Ihre Domäne aus, aktivieren Sie das Kontrollkästchen **Nur bevorzugten Domänencontroller verwenden**, und klicken Sie auf **Konfigurieren**. 

9. Geben Sie in der Liste die Domänencontroller ein, die Connect zur Kennwortsynchronisierung verwenden soll. Die gleiche Liste wird ebenfalls für den Import und Export verwendet. Führen Sie diese Schritte für alle Ihre Domänen aus.

10. Wenn der Skriptausgabe zufolge kein Takt vorhanden ist, führen Sie das Skript unter [Auslösen einer vollständigen Synchronisierung aller Kennwörter](#trigger-a-full-sync-of-all-passwords) aus.

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Ein einzelnes Objekt synchronisiert keine Kennwörter: Schritte zur manuellen Problembehandlung

Sie können Probleme mit der Kennworthashsynchronisierung einfach beheben, indem Sie den Status eines Objekts überprüfen.

1. Suchen Sie unter **Active Directory-Benutzer und -Computer** nach dem Benutzer, und überprüfen Sie dann, ob das Kontrollkästchen **Benutzer muss Kennwort bei der nächsten Anmeldung ändern** deaktiviert ist.  

    ![Produktive Active Directory-Kennwörter](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Falls das Kontrollkästchen aktiviert ist, fordern Sie den Benutzer auf, sich anzumelden und das Kennwort zu ändern. Temporäre Kennwörter werden nicht mit Azure AD synchronisiert.

2. Wenn das Kennwort in Active Directory korrekt aussieht, folgen Sie dem Benutzer im Synchronisierungsmodul. Indem Sie dem Benutzer vom lokalen Active Directory nach Azure AD folgen, können Sie feststellen, ob eine beschreibende Fehlermeldung für das Objekt vorhanden ist.

    a. Starten Sie den [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

    b. Klicken Sie auf **Connectors**.

    c. Wählen Sie den **Active Directory-Connector** aus, in dem sich der Benutzer befindet.

    d. Wählen Sie **Search Connector Space**(Connectorbereich durchsuchen) aus.

    e. Wählen Sie im Feld **Bereich** die Option **DN oder Anker** aus, und geben Sie dann den vollständigen DN des Benutzers ein, für den Sie eine Problembehandlung durchführen.

    ![Suchen nach dem Benutzer im Connectorbereich anhand des DN](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Machen Sie den gesuchten Benutzer ausfindig, und klicken Sie auf **Eigenschaften**, um alle Attribute zu sehen. Wenn der Benutzer nicht im Suchergebnis aufgeführt wird, dann überprüfen Sie Ihre [Filterregeln](how-to-connect-sync-configure-filtering.md), und stellen Sie sicher, dass Sie die Schritte unter [Anwenden und Überprüfen von Änderungen](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) für den Benutzer durchführen, der in Connect angezeigt werden soll.

    g. Klicken Sie auf **Protokoll**, um die Details zur Kennwortsynchronisierung der letzten Woche für das Objekt anzuzeigen.  

    ![Objektprotokolldetails](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Wenn das Objektprotokoll leer ist, konnte Azure AD Connect den Kennworthash aus Active Directory nicht lesen. Setzen Sie Ihre Problembehandlung mit Verbindungsfehlern fort. Wenn Sie einen anderen Wert als **Erfolg** sehen, dann schauen Sie in der Tabelle [Kennwortsynchronisierungsprotokoll](#password-sync-log) nach.

    h. Klicken Sie auf die Registerkarte **Herkunft**, und stellen Sie sicher, dass mindestens eine Synchronisierungsregel in der Spalte **Kennwortsynchronisierung** auf **Wahr** festgelegt ist. In der Standardkonfiguration lautet der Name der Synchronisierungsregel **Eingehend von AD – Benutzerkonto aktiviert**.  

    ![Herkunftsinformationen zu einem Benutzer](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Klicken Sie auf **Metaverseobjekteigenschaften**, um eine Liste von Benutzerattributen anzuzeigen.  

    ![Metaverseinformationen](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Stellen Sie sicher, dass kein **cloudFiltered**-Attribut vorhanden ist. Stellen Sie sicher, dass die Domänenattribute (domainFQDN und domainNetBios) über die erwarteten Werte verfügen.

    j. Klicken Sie auf die Registerkarte **Connectors**. Stellen Sie sicher, dass Connectors für lokale Active Directory- und Azure AD-Benutzer angezeigt werden.

    ![Metaverseinformationen](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Wählen Sie die Zeile aus, die Azure AD darstellt, und klicken Sie auf **Eigenschaften** und dann auf die Registerkarte **Herkunft**. Das Connectorbereichsobjekt sollte über eine ausgehende Regel verfügen, für die die Spalte **Kennwortsynchronisierung** auf **Wahr** festgelegt ist. In der Standardkonfiguration lautet der Name der Synchronisierungsregel **Out to AAD - User Join**(Ausgehend an AAD – Benutzerverknüpfung).  

    ![Dialogfeld „Eigenschaften von Connectorbereichsobjekten“](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Kennwortsynchronisierungsprotokoll

Die Statusspalte kann die folgenden Werte enthalten:

| Status | BESCHREIBUNG |
| --- | --- |
| Erfolgreich |Das Kennwort wurde erfolgreich synchronisiert. |
| FilteredByTarget |Das Kennwort wird auf **Benutzer muss Kennwort bei der nächsten Anmeldung ändern**festgelegt. Das Kennwort wurde nicht synchronisiert. |
| NoTargetConnection |Im Metaverse oder im Azure AD-Connectorbereich befindet sich kein Objekt. |
| SourceConnectorNotPresent |Im lokalen Active Directory Connector-Bereich wurde kein Objekt gefunden. |
| TargetNotExportedToDirectory |Das Objekt im Azure AD-Connectorbereich wurde noch nicht exportiert. |
| MigratedCheckDetailsForMoreInfo |Der Protokolleintrag wurde vor Build 1.0.9125.0 erstellt und wird im Zustand der Vorversion angezeigt. |
| Error |Dienst hat einen unbekannten Fehler zurückgegeben. |
| Unknown |Fehler beim Versuch, einen Batch von Kennworthashes zu verarbeiten.  |
| MissingAttribute |Bestimmte Attribute (z.B. Kerberos-Hash), die von Azure Active Directory Domain Services angefordert werden, sind nicht verfügbar. |
| RetryRequestedByTarget |Bestimmte Attribute (z.B. Kerberos-Hash), die von Azure Active Directory Domain Services angefordert werden, waren zuvor nicht verfügbar. Es wird versucht, den Kennworthash des Benutzers neu zu synchronisieren. |

## <a name="scripts-to-help-troubleshooting"></a>Hilfe zur Problembehandlung durch Skripts

### <a name="get-the-status-of-password-sync-settings"></a>Abrufen des Status der Kennwortsynchronisierungseinstellungen

```powershell
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Auslösen einer vollständigen Synchronisierung aller Kennwörter

> [!NOTE]
> Führen Sie dieses Skript nur einmal aus. Wenn Sie es mehrere Male ausführen müssen, ist etwas anderes das Problem. Wenden Sie sich an den Microsoft-Support, um Hilfe bei der Problembehandlung zu erhalten.

Mit dem folgenden Skript können Sie eine vollständige Synchronisierung aller Kennwörter auslösen:

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Nächste Schritte

* [Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](how-to-connect-password-hash-synchronization.md)
* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)