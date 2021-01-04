---
title: Verwalten von Office 365-Diensten mit Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie Azure Automation zum Verwalten von Office 365-Abonnementdiensten verwenden.
services: automation
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: 70c8892969a3b13175c60a4e20e0cf9086112abe
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398044"
---
# <a name="manage-office-365-services"></a>Verwalten von Office 365-Diensten

Sie können Azure Automation für die Verwaltung von Office 365-Abonnementdiensten und für Produkte wie Microsoft Word und Microsoft Outlook verwenden. Interaktionen mit Office 365 werden durch [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) ermöglicht. Siehe [Verwenden von Azure AD in Azure Automation für die Authentifizierung bei Azure](automation-use-azure-ad.md).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes zum Verwalten von Office 365-Abonnementdiensten in Azure Automation.

* Ein Azure-Abonnement. Siehe [Leitfaden zur Entscheidungsfindung für Abonnements](/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Ein Automation-Objekt in Azure, das die Anmeldeinformationen des Benutzerkontos und Runbooks enthält. Siehe [Einführung in Azure Automation](./automation-intro.md).
* Azure AD. Siehe [Verwenden von Azure AD in Azure Automation für die Authentifizierung bei Azure](automation-use-azure-ad.md).
* Einen Office 365-Mandanten mit einem Konto. Siehe [Einrichten Ihres Office 365-Mandanten](/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="install-the-msonline-and-msonlineext-modules"></a>Installieren der Module MSOnline und MSOnlineExt

Die Verwendung von Office 365 in Azure Automation erfordert Microsoft Azure Active Directory für Windows PowerShell (`MSOnline`-Modul). Außerdem benötigen Sie das Modul [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35), das die Azure AD-Verwaltung in Umgebungen mit einem und mehreren Mandanten vereinfacht. Installieren Sie das Modul wie unter [Verwenden von Azure AD in Azure Automation für die Authentifizierung bei Azure](automation-use-azure-ad.md) beschrieben.

>[!NOTE]
>Um MSOnline PowerShell verwenden zu können, müssen Sie Mitglied von Azure AD sein. Gastbenutzer können das Modul nicht verwenden.

## <a name="create-an-azure-automation-account"></a>Erstellen eines Azure Automation-Kontos

Für die Durchführung der in diesem Artikel aufgeführten Schritte benötigen Sie ein Konto in Azure Automation. Siehe [Erstellen eines Azure Automation-Kontos](automation-quickstart-create-account.md).
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>Hinzufügen von MSOnline und MSOnlineExt als Objekte

Fügen Sie nun die installierten Module MSOnline und MSOnlineExt hinzu, um die Funktionalität von Office 365 zu aktivieren. Informationen finden Sie unter [Verwalten von Modulen in Azure Automation](shared-resources/modules.md).

1. Wählen Sie im Azure-Portal **Automation-Konten** aus.
2. Wählen Sie Ihr Automation-Konto aus.
3. Wählen Sie unter **Freigegebene Ressourcen** die Option **Modulkatalog** aus.
4. Suchen Sie nach MSOnline.
5. Wählen Sie das PowerShell-Modul `MSOnline` aus, und klicken Sie auf **Importieren** , um das Modul als Objekt zu importieren.
6. Wiederholen Sie die Schritte 4 und 5, um das `MSOnlineExt`-Modul zu suchen und zu importieren.

## <a name="create-a-credential-asset-optional"></a>Erstellen eines Anmeldeinformationsobjekts (optional)

Es ist optional, ein Anmeldeinformationsobjekt für den Administratorbenutzer von Office 365 zu erstellen, der über Berechtigungen zum Ausführen Ihres Skripts verfügt. Dies kann jedoch hilfreich sein, um die Offenlegung von Benutzernamen und Kennwörtern innerhalb von PowerShell-Skripts zu verhindern. Anleitungen finden Sie unter [Erstellen eines Anmeldeinformationsobjekts](automation-use-azure-ad.md#create-a-credential-asset).

## <a name="create-an-office-365-service-account"></a>Erstellen eines Office 365-Dienstkontos

Um Office 365-Abonnementdienste ausführen zu können, benötigen Sie ein Office 365-Dienstkonto mit den für die Aktionen notwendigen Berechtigungen, die Sie ausführen möchten. Sie können ein globales Administratorkonto, dabei ein Konto pro Dienst, verwenden oder über eine Funktion oder ein Skript zum Ausführen verfügen. In jedem Fall benötigt das Dienstkonto ein komplexes und sicheres Kennwort. Siehe [Einrichten von Office 365 Business](/microsoft-365/admin/setup/setup).

## <a name="connect-to-the-azure-ad-online-service"></a>Herstellen einer Verbindung mit dem Azure AD-Onlinedienst

>[!NOTE]
>Um die Cmdlets des MSOnline-Moduls verwenden zu können, müssen Sie sie über die Windows PowerShell ausführen. PowerShell Core unterstützt diese Cmdlets nicht.

Sie können das MSOnline-Modul verwenden, um eine Verbindung mit Azure AD aus dem Office 365-Abonnement herzustellen. Die Verbindung verwendet einen Benutzernamen und ein Kennwort von Office 365, oder sie verwendet mehrstufige Authentifizierung (Multi-Factor Authentication, MFA). Sie können eine Verbindung über das Azure-Portal oder eine Windows PowerShell-Eingabeaufforderung herstellen (benötigt keine erhöhten Rechte).

Unten sehen Sie ein PowerShell-Beispiel. Das [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)-Cmdlet fordert zur Eingabe von Anmeldeinformationen auf und speichert diese in der Variablen `Msolcred`. Anschließend verwendet das [Connect-MsolService](/powershell/module/msonline/connect-msolservice)-Cmdlet die Anmeldeinformationen, um eine Verbindung mit dem Azure Active Directory-Onlinedienst herzustellen. Wenn Sie eine Verbindung mit einer bestimmten Azure-Umgebung herstellen möchten, verwenden Sie den `AzureEnvironment`-Parameter.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Wenn Sie keine Fehler erhalten, haben Sie erfolgreich eine Verbindung hergestellt. Ein schneller Test besteht darin, ein Office 365-Cmdlet auszuführen, z. B. `Get-MsolUser`, und die Ergebnisse anzuzeigen. Wenn Sie Fehler erhalten, beachten Sie, dass ein häufiges Problem ein falsches Kennwort ist.

>[!NOTE]
>Sie können auch das AzureRM-Modul oder das Az-Modul verwenden, um eine Verbindung mit Azure AD aus dem Office 365-Abonnement herzustellen. Das Hauptverbindungs-Cmdlet ist [Connect-AzureAD](/powershell/module/azuread/connect-azuread). Dieses Cmdlet unterstützt den `AzureEnvironmentName`-Parameter für bestimmte Office 365-Umgebungen.

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>Erstellen eines PowerShell-Runbooks aus einem vorhandenen Skript

Auf Office 365-Funktionen greifen Sie über ein PowerShell-Skript zu. Im Folgenden finden Sie ein Beispiel für ein Skript für ein Anmeldeinformationsobjekt namens `Office-Credentials` mit dem Benutzernamen `admin@TenantOne.com`. Es verwendet `Get-AutomationPSCredential`, um das Office 365-Anmeldeinformationsobjekt zu importieren.

```powershell
$emailFromAddress = "admin@TenantOne.com"
$emailToAddress = "servicedesk@TenantOne.com"
$emailSMTPServer = "outlook.office365.com"
$emailSubject = "Office 365 License Report"

$credObject = Get-AutomationPSCredential -Name "Office-Credentials"
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body $O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="run-the-script-in-a-runbook"></a>Ausführen des Skripts in einem Runbook

Sie können Ihr Skript in einem Azure Automation-Runbook verwenden. Zu Demonstrationszwecken verwenden wir den PowerShell-Runbooktyp.

1. Erstellen Sie ein neues PowerShell-Runbook. Informationen finden Sie und [Erstellen eines Azure Automation-Runbooks](./automation-quickstart-create-runbook.md).
2. Wählen Sie in Ihrem Automation-Konto unter **Prozessautomatisierung** die Option **Runbooks** aus.
3. Wählen Sie das neue Runbook aus, und klicken Sie auf **Bearbeiten**.
4. Kopieren Sie Ihr Skript, und fügen Sie es in den Text-Editor für das Runbook ein.
5. Wählen Sie **OBJEKTE** aus, erweitern Sie dann **Anmeldeinformationen** , und überprüfen Sie, ob das Office 365-Anmeldeinformationsobjekt vorhanden ist.
6. Klicken Sie auf **Speichern**.
7. Wählen Sie **Testbereich** aus, und klicken Sie dann auf **Starten** , um das Runbook zu testen. Siehe [Verwalten von Runbooks in Azure Automation](./manage-runbooks.md).
8. Wenn die Tests fertig sind, beenden Sie den Testbereich.

## <a name="publish-and-schedule-the-runbook"></a>Veröffentlichen und Planen des Runbooks

Informationen zum Veröffentlichen und anschließenden Planen Ihres Runbooks finden Sie unter [Verwalten von Runbooks in Azure Automation](./manage-runbooks.md).

## <a name="next-steps"></a>Nächste Schritte

* Ausführliche Informationen zur Verwendung von Anmeldeinformationen finden Sie unter [Verwalten von Anmeldeinformationen in Azure Automation](shared-resources/credentials.md).
* Weitere Informationen zu Modulen finden Sie unter [Verwalten von Modulen in Azure Automation](shared-resources/modules.md).
* Wenn Sie Informationen zum Starten eines Runbooks benötigen, lesen Sie den Artikel [Starten eines Runbooks in Azure Automation](start-runbooks.md).
* Details zu PowerShell finden Sie in der [PowerShell-Dokumentation](/powershell/scripting/overview).
