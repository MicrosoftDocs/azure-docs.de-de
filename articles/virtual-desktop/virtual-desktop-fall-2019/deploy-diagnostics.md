---
title: Bereitstellen des Diagnosetools für Windows Virtual Desktop (klassisch) – Azure
description: So stellen Sie das Diagnose-UX-Tool für Windows Virtual Desktop (klassisch) bereit.
author: Heidilohr
ms.topic: how-to
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9672d18fe99d8baae3c73fa3782531eb84114713
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97515637"
---
# <a name="deploy-the-windows-virtual-desktop-classic-diagnostics-tool"></a>Bereitstellen des Diagnosetools für Windows Virtual Desktop (klassisch)

>[!IMPORTANT]
>Dieser Inhalt gilt für Windows Virtual Desktop (klassisch). Der Dienst unterstützt keine Windows Virtual Desktop-Objekte in Azure Resource Manager.

Folgende Aufgaben kann das Diagnosetool für Windows Virtual Desktop für Sie erledigen:

- Nachschlagen von Diagnoseaktivitäten (Verwaltung, Verbindung oder Feed) für einen einzelnen Benutzer über einen Zeitraum von einer Woche.
- Sammeln von Sitzungshostinformationen für Verbindungsaktivitäten aus Ihrem Log Analytics-Arbeitsbereich.
- Überprüfen der Leistungsdetails virtueller Computer (VM) für einen bestimmten Host.
- Anzeigen, welche Benutzer beim Sitzungshost angemeldet sind.
- Senden einer Nachricht an aktive Benutzer auf einem bestimmten Sitzungshost.
- Abmelden von Benutzer bei einem Sitzungshost.

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen eine Azure Active Directory-App-Registrierung und einen Log Analytics-Arbeitsbereich erstellen, bevor Sie die Azure Resource Manager-Vorlage für das Tool bereitstellen können. Sie oder der Administrator benötigen hierfür diese Berechtigungen:

- Besitzer des Azure-Abonnements
- Berechtigung zum Erstellen von Ressourcen in Ihrem Azure-Abonnement
- Berechtigung zum Erstellen einer Azure AD-App
- RDS-Rechte „Besitzer“ oder „Mitwirkender“

Außerdem müssen Sie diese beiden PowerShell-Module installieren, bevor Sie beginnen:

- [Azure PowerShell-Modul](/powershell/azure/install-az-ps?view=azps-2.4.0/&preserve-view=true)
- [Azure AD-Modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/&preserve-view=true)

Stellen Sie sicher, dass Ihre Abonnement-ID zur Hand haben, wenn Sie sich anmelden.

Nachdem Sie alles vorbereitet haben, können Sie die Azure AD-App-Registrierung erstellen.

## <a name="create-an-azure-active-directory-app-registration"></a>Erstellen einer Azure Active Directory-App-Registrierung

In diesem Abschnitt erfahren Sie, wie Sie PowerShell verwenden, um die Azure Active Directory-App mit einem Dienstprinzipal zu erstellen und für diesen API-Berechtigungen zu erhalten.

>[!NOTE]
>Die API-Berechtigungen sind Windows Virtual Desktop-, Log Analytics- und Microsoft Graph-API-Berechtigungen und werden der Azure Active Directory-Anwendung hinzugefügt.

1. Öffnen Sie PowerShell als Administrator.
2. Melden Sie sich bei Azure mit einem Konto an, das die Berechtigung „Besitzer“ oder „Mitwirkender“ für das Azure-Abonnement besitzt, das Sie für die Diagnosetools verwenden möchten:
   ```powershell
   Login-AzAccount
   ```
3. Melden Sie sich bei Azure AD mit dem gleichen Konto an:
   ```powershell
   Connect-AzureAD
   ```
4. Wechseln Sie zum [RDS-Templates-GitHub-Repository](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts), und führen Sie das Skript **CreateADAppRegistrationforDiagnostics.ps1** in PowerShell aus.
5.  Wenn Sie vom Skript aufgefordert werden, Ihre App zu benennen, geben Sie einen eindeutigen App-Namen ein.


Nachdem das Skript erfolgreich ausgeführt wurde, sollte Folgendes in seiner Ausgabe angezeigt werden:

-  Eine Meldung, die bestätigt, dass Ihre App jetzt über eine Dienstprinzipal-Rollenzuweisung verfügt.
-  Ihre Client-ID und der geheime Clientschlüssel, die Sie benötigen, wenn Sie das Diagnosetool bereitstellen.

Nachdem Sie Ihre App nun registriert haben, ist es an der Zeit, Ihren Log Analytics-Arbeitsbereich zu konfigurieren.

## <a name="configure-your-log-analytics-workspace"></a>Konfigurieren Ihres Log Analytics-Arbeitsbereichs

Für eine optimale Erfahrung empfehlen wir Ihnen, Ihren Log Analytics-Arbeitsbereich mit den folgenden Leistungsindikatoren zu konfigurieren, die es Ihnen gestatten, Aussagen über die Benutzererfahrung in einer Remotesitzung abzuleiten. Eine Liste der empfohlenen Indikatoren mit empfohlenen Schwellenwerten finden Sie unter [Schwellenwerte für Windows-Leistungsindikatoren](deploy-diagnostics.md#windows-performance-counter-thresholds).

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Erstellen eines Log Analytics-Arbeitsbereichs mithilfe von PowerShell

Sie können ein PowerShell-Skript ausführen, um einen Log Analytics-Arbeitsbereich zu erstellen und die empfohlenen Windows-Leistungsindikatoren zur Überwachung der Benutzererfahrung und App-Leistung zu konfigurieren.

>[!NOTE]
>Wenn Sie bereits über einen vorhandenen Log Analytics-Arbeitsbereich verfügen, den Sie ohne das PowerShell-Skript erstellt haben, den Sie verwenden möchten, fahren Sie mit [Überprüfen der Skriptergebnisse im Azure-Portal](#validate-the-script-results-in-the-azure-portal) fort.

So führen Sie das PowerShell-Skript aus

1.  Öffnen Sie PowerShell als Administrator.
2.  Wechseln Sie zum [RDS-Templates-GitHub-Repository](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts), und führen Sie das Skript **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** in PowerShell aus.
3. Geben Sie die folgende Werte für die Parameter ein:

    - Für **ResourceGroupName** geben Sie den Namen der Ressourcengruppe ein.
    - Für **LogAnalyticsWorkspaceName** geben Sie einen eindeutigen Namen für Ihren Log Analytics-Arbeitsbereich ein.
    - Für **Standort** geben Sie die Azure-Region ein, die Sie verwenden.
    - Geben Sie die **Azure-Abonnement-ID** ein, die Sie im Azure-Portal unter **Abonnements** finden können.

4. Geben Sie die Anmeldeinformationen eines Benutzers ein, der über delegierten Administratorzugriff verfügt.
5. Melden Sie sich beim Azure-Portal mit den Anmeldeinformationen desselben Benutzers an.
6. Notieren Sie sich die LogAnalyticsWorkspace-ID für einen späteren Zeitpunkt, oder merken Sie sich diese.
7. Wenn Sie den Log Analytics-Arbeitsbereich mit dem PowerShell-Skript einrichten, sollten Ihre Leistungsindikatoren bereits konfiguriert sein, und Sie können mit [Überprüfen der Skriptergebnisse im Azure-Portal](#validate-the-script-results-in-the-azure-portal) fortfahren. Fahren Sie andernfalls mit dem nächsten Abschnitt fort.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Konfigurieren der Windows-Leistungsindikatoren in Ihrem vorhandenen Log Analytics-Arbeitsbereich

Dieser Abschnitt richtet sich an Benutzer, die einen vorhandenen Azure Log Analytics-Arbeitsbereich verwenden möchten, der ohne das PowerShell-Skript aus dem vorherigen Abschnitt erstellt wurde. Wenn Sie das Skript nicht verwendet haben, müssen Sie die empfohlenen Windows-Leistungsindikatoren manuell konfigurieren.

Im Folgenden wird erläutert, wie die empfohlenen Leistungsindikatoren manuell konfiguriert werden:

1. Öffnen Sie Ihren Internetbrowser, und melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit Ihrem Administratorkonto an.
2. Wechseln Sie als Nächstes zu **Log Analytics-Arbeitsbereiche**, um die konfigurierten Windows-Leistungsindikatoren zu überprüfen.
3. Wählen Sie im Abschnitt **Einstellungen** die Option **Erweiterte Einstellungen** aus.
4. Navigieren Sie anschließend zu **Daten** > **Windows-Leistungsindikatoren**, und fügen Sie die folgenden Indikatoren hinzu:

    -   LogicalDisk(\*)\\%Freier Speicher
    -   LogicalDisk(C:)\\Durchschn. Warteschlangenlänge des Datenträgers
    -   Arbeitsspeicher(\*)\\Verfügbare MB
    -   Prozessorinformationen(\*)\\Prozessorzeit
    -   Benutzereingabeverzögerung pro Sitzung(\*)\\Maximale Eingabeverzögerung

Weitere Informationen zu den Leistungsindikatoren finden Sie unter [Windows- und Linux-Leistungsdatenquellen in Azure Monitor](../../azure-monitor/platform/data-sources-performance-counters.md).

>[!NOTE]
>Alle weiteren Indikatoren, die Sie konfigurieren, werden im Diagnosetool selbst nicht angezeigt. Damit sie im Diagnosetool angezeigt werden, müssen Sie die Konfigurationsdatei des Tools konfigurieren. Anleitungen dazu, wie Sie dies mit der erweiterten Verwaltung durchführen, werden zu einem späteren Zeitpunkt auf GitHub verfügbar sein.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Überprüfen der Skriptergebnisse im Azure-Portal

Bevor Sie mit der Bereitstellung des Diagnosetools fortfahren, sollten Sie überprüfen, ob Ihre Azure Active Directory Anwendung über API-Berechtigungen und Ihr Log Analytics-Arbeitsbereich über die vorkonfigurierten Windows-Leistungsindikatoren verfügt.

### <a name="review-your-app-registration"></a>Überprüfen Ihrer App-Registrierung

So stellen Sie sicher, dass Ihre App-Registrierung API-Berechtigungen besitzt

1. Öffnen Sie einen Browser, und stellen Sie mit Ihrem Administratorkonto eine Verbindung mit dem [Azure-Portal](https://portal.azure.com/) her.
2. Wechseln Sie zu **Azure Active Directory**.
3. Navigieren Sie zu **App-Registrierungen**, und wählen Sie **Alle Anwendungen** aus.
4. Suchen Sie nach Ihrer Azure AD-App-Registrierung mit dem gleichen App-Namen, den Sie in Schritt 5 von [Erstellen einer Azure Active Directory-App-Registrierung](deploy-diagnostics.md#create-an-azure-active-directory-app-registration) eingegeben haben.

### <a name="review-your-log-analytics-workspace"></a>Überprüfen Ihres Log Analytics-Arbeitsbereichs

So stellen Sie sicher, dass Ihr Log Analytics-Arbeitsbereich über die vorkonfigurierten Windows-Leistungsindikatoren verfügt

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com/) zu **Log Analytics-Arbeitsbereiche**, um die konfigurierten Windows-Leistungsindikatoren zu überprüfen.
2. Wählen Sie unter **Einstellungen** die Option **Erweiterte Einstellungen** aus.
3. Wechseln Sie anschließend zu **Daten** > **Windows-Leistungsindikatoren**.
4. Stellen Sie sicher, dass die folgenden Indikatoren vorkonfiguriert sind:

   - LogicalDisk(\*)\\%Freier Speicher: Zeigt die Menge des freien Speicherplatzes des gesamten nutzbaren Speicherplatzes auf dem Datenträger als Prozentsatz an.
   - LogicalDisk(C:)\\Durchschn. Warteschlangenlänge des Datenträgers: Die Länge der Datenübertragungsanforderung für Ihr Laufwerk „C“. Der Wert sollte 2 nicht länger als für einen kurzen Zeitraum überschreiten.
   - Arbeitsspeicher(\*)\\Verfügbare MB: Der verfügbare Arbeitsspeicher für das System in MB.
   - Prozessorinformationen(\*)\\Prozessorzeit: der Prozentsatz der verstrichenen Zeit, die der Prozessor mit dem Ausführen eines nicht im Leerlauf befindlichen Threads verbringt.
   - Benutzereingabeverzögerung pro Sitzung(\*)\\Maximale Eingabeverzögerung

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Verbinden mit VMs in Ihrem Log Analytics-Arbeitsbereich

Um den Status von VMS anzeigen zu können, müssen Sie die Log Analytics-Verbindung aktivieren. Führen Sie diese Schritte aus, um Ihre VMs zu verbinden:

1. Öffnen Sie einen Browser, und melden Sie sich mit Ihrem Administratorkonto beim [Azure-Portal](https://portal.azure.com/) an.
2. Wechseln Sie zu Ihrem Log Analytics-Arbeitsbereich.
3. Wählen Sie im linken Bereich unter „Arbeitsbereichsdatenquellen“ **Virtuelle Computer** aus.
4. Wählen Sie den Namen des virtuellen Computers aus, den Sie verbinden möchten.
5. Wählen Sie **Verbinden**.

## <a name="deploy-the-diagnostics-tool"></a>Bereitstellen des Diagnosetools

So stellen Sie die Azure Ressource Manager-Vorlage für das Diagnosetool bereit

1.  Wechseln Sie zur GitHub-Seite [Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Stellen Sie die Vorlage in Azure bereit, und befolgen Sie die Anleitung in der Vorlage. Stellen Sie sicher, dass die folgenden Informationen verfügbar sind:

    -   Client-ID
    -   Geheimer Clientschlüssel (client-secret)
    -   ID des Log Analytics-Arbeitsbereichs

3.  Nachdem die Eingabeparameter bereitgestellt wurden, akzeptieren Sie die Geschäftsbedingungen, und wählen Sie **Kaufen** aus.

Die Bereitstellung dauert 2 bis 3 Minuten. Wechseln Sie nach erfolgreicher Bereitstellung zur Ressourcengruppe, und stellen Sie sicher, dass die Web-App- und App-Dienstplanressourcen vorhanden sind.

Danach müssen Sie den Umleitungs-URI festlegen.

### <a name="set-the-redirect-uri"></a>Festlegen des Umleitungs-URIs

So legen Sie den Umleitungs-URI fest

1.  Wechseln Sie im [Azure-Portal](https://portal.azure.com/) zu **App Services**, und suchen Sie die Anwendung, die Sie erstellt haben.
2.  Wechseln Sie zur Übersichtsseite, und kopieren Sie die URL, die Sie dort finden.
3.  Navigieren Sie zu **App-Registrierungen**, und wählen Sie die App aus, die Sie bereitstellen möchten.
4.  Wählen Sie im linken Bereich unter dem Abschnitt „Verwalten“ **Authentifizierung** aus.
5.  Geben Sie den gewünschten Umleitungs-URI in das Textfeld **Umleitungs-URI** ein, und wählen Sie dann in der oberen linken Ecke des Menüs die Option **Speichern** aus.
6. Wählen Sie im Dropdownmenü unter „Typ“ **Web** aus.
7. Geben Sie die URL von der Übersichtseite der App ein, und fügen Sie an deren Ende **/security/signin-callback** hinzu. Beispiel: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   > [!div class="mx-imgBorder"]
   > ![Die Seite „Umleitungs-URI“](../media/redirect-uri-page.png)

8. Wechseln Sie nun zu Ihren Azure-Ressourcen, wählen Sie die Azure App Services-Ressource mit dem Namen aus, den Sie in der Vorlage angegeben haben, und navigieren Sie zu der damit verknüpften URL. (Wenn der in der Vorlage verwendete Name beispielsweise `contosoapp45` war, dann lautet Ihre zugeordnete URL <http://contoso.azurewebsites.net>.)
9. Melden Sie sich mit dem entsprechenden Azure Active Directory-Benutzerkonto an.
10.   Wählen Sie **Akzeptieren** aus.

## <a name="distribute-the-diagnostics-tool"></a>Verteilen des Diagnosetools

Bevor Sie das Diagnosetool für Ihre Benutzer verfügbar machen, stellen Sie sicher, dass diese über die folgenden Berechtigungen verfügen:

- Benutzer benötigen Lesezugriff für Log Analytics. Weitere Informationen finden Sie unter [Erste Schritte mit Rollen, Berechtigungen und Sicherheit in Azure Monitor](../../azure-monitor/platform/roles-permissions-security.md).
-  Benutzer benötigen ferner Lesezugriff für den Windows Virtual Desktop-Mandanten (RDS-Rolle „Leser“). Weitere Informationen finden Sie unter [Delegierter Zugriff in Windows Virtual Desktop](delegated-access-virtual-desktop-2019.md).

Sie müssen Ihren Benutzern außerdem noch folgende Informationen zur Verfügung stellen:

- Die URL der App.
- Die Namen der einzelnen Mandanten der Mandantengruppe, auf die Sie zugreifen können.

## <a name="use-the-diagnostics-tool"></a>Verwenden des Diagnosetools

Nachdem Sie sich mit den Informationen, die Sie von Ihrer Organisation erhalten haben, bei Ihrem Konto angemeldet haben, halten Sie den UPN für den Benutzer bereit, für den Sie Aktivitäten abfragen möchten. Eine Suche liefert Ihnen alle Aktivitäten unter dem angegebenen Aktivitätstyp, der innerhalb der letzten Woche aufgetreten sind.

### <a name="how-to-read-activity-search-results"></a>Lesen von Aktivitätssuchergebnissen

Aktivitäten werden nach Zeitstempel sortiert, beginnend mit der aktuellsten Aktivität. Wenn die Ergebnisse einen Fehler zurückgeben, überprüfen Sie zunächst, ob es sich um einen Dienstfehler handelt. Erstellen Sie bei Dienstfehlern ein Supportticket mit den Aktivitätsinformationen, um uns beim Debuggen des Problems zu unterstützen. Alle anderen Fehlertypen können normalerweise vom Benutzer oder Administrator behoben werden. Eine Liste der häufigsten Fehlerszenarien und deren Lösungsmöglichkeiten finden Sie unter [Identifizieren und Diagnostizieren von Problemen](diagnostics-role-service-2019.md#common-error-scenarios).

>[!NOTE]
>Dienstfehler werden in der verlinkten Dokumentation als „externe Fehler“ bezeichnet. Dies wird geändert, wenn wir die PowerShell-Referenz aktualisieren.

Verbindungsaktivitäten können mehr als einen Fehler aufweisen. Sie können den Aktivitätstyp erweitern, um alle anderen Fehler anzuzeigen, auf die der Benutzer gestoßen ist. Wählen Sie den Namen des Fehlercodes aus, um ein Dialogfeld zu öffnen, in dem weitere Informationen dazu angezeigt werden.

### <a name="investigate-the-session-host"></a>Untersuchen des Sitzungshosts

Suchen Sie in den Suchergebnissen den Sitzungshost, über den Sie Informationen abrufen möchten, und wählen Sie ihn aus.

Sie können den Status des Sitzungshosts analysieren:

- Basierend auf einem vordefinierten Schwellenwert können Sie die Statusinformationen des Sitzungshosts abrufen, die Log Analytics abruft.
- Wenn keine Aktivitäten vorliegen oder der Sitzungshost nicht mit Log Analytics verbunden ist, sind die Informationen nicht verfügbar.

Sie können auch mit Benutzern auf dem Sitzungshost interagieren:

- Sie können sich entweder abmelden oder eine Nachricht an angemeldete Benutzer senden.
- Der Benutzer, nach dem Sie ursprünglich gesucht haben, ist standardmäßig ausgewählt. Sie können aber auch zusätzliche Benutzer auswählen, um Nachrichten zu senden, oder mehrere Benutzer gleichzeitig abmelden.

### <a name="windows-performance-counter-thresholds"></a>Schwellenwerte für Windows-Leistungsindikatoren

- LogicalDisk(\*)\\%Freier Speicher:

    - Zeigt den Prozentsatz des insgesamt nutzbaren Speicherplatzes auf dem logischen Datenträger an, der frei ist.
    - Schwellenwert: Weniger als 20 % werden als fehlerhaft markiert.

- LogicalDisk(C:)\\Durchschn. Warteschlangenlänge des Datenträgers:

    - Stellt die Speichersystembedingungen dar.
    - Schwellenwert: Mehr als 5 wird als fehlerhaft markiert.

- Arbeitsspeicher(\*)\\Verfügbare MB:

    - Der verfügbare Arbeitsspeicher für das System.
    - Schwellenwert: Weniger als 500 MB wird als fehlerhaft markiert.

- Prozessorinformationen(\*)\\Prozessorzeit:

    - Schwellenwert: Mehr als 80 % werden als fehlerhaft markiert.

- [Benutzereingabeverzögerung pro Sitzung(\*)\\Maximale Eingabeverzögerung](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/):

    - Schwellenwert: Mehr als 2000 ms werden als fehlerhaft markiert.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Überwachung von Aktivitätsprotokollen finden Sie unter [Verwenden von Diagnosedaten mit Log Analytics](diagnostics-log-analytics-2019.md).
- Informationen zu den häufigsten Fehlerszenarien und deren Behebung finden Sie unter [Identifizieren und Diagnostizieren von Problemen](diagnostics-role-service-2019.md).