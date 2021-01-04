---
title: Erstellen und Verwalten von Aktionsgruppen im Azure-Portal
description: Erfahren Sie, wie Sie Aktionsgruppen im Azure-Portal erstellen und verwalten.
author: dkamstra
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: a2144827cd27c0af6610dabdf5ad205359f550cc
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435483"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Erstellen und Verwalten von Aktionsgruppen im Azure-Portal
Eine Aktionsgruppe ist eine Sammlung von Benachrichtigungseinstellungen, die vom Besitzer eines Azure-Abonnements definiert wurden. Azure Monitor- und Service Health-Warnungen verwenden Aktionsgruppen, um Benutzer zu benachrichtigen, dass eine Warnung ausgelöst wurde. Verschiedene Warnungen können je nach den Bedürfnissen des Benutzers die gleiche Aktionsgruppe oder verschiedene Aktionsgruppen verwenden. 

In diesem Artikel wird beschrieben, wie Sie Aktionsgruppen im Azure-Portal erstellen und verwalten.

Jede Aktion besteht aus den folgenden Eigenschaften:

* **Typ:** Die ausgeführte Benachrichtigung oder Aktion. Beispiele sind das Senden eines Sprachanrufs, SMS, E-Mail oder das Auslösen verschiedener Arten von automatisierten Aktionen. Siehe „Typen“ weiter unten in diesem Artikel.
* **Name**: Ein eindeutiger Bezeichner innerhalb der Aktionsgruppe.
* **Details**: Die entsprechenden Details. Diese können je nach *Typ* variieren.

Weitere Informationen zum Verwenden von Azure Resource Manager-Vorlagen zur Konfigurierung von Aktionsgruppen finden Sie unter [Aktionsgruppen-Resource Manager-Vorlagen](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Erstellen einer Aktionsgruppe mit dem Azure-Portal

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach dem Eintrag **Überwachen**, und wählen Sie ihn aus. Im Bereich **Überwachen** sind alle Ihre Überwachungseinstellungen und -daten an einem zentralen Ort zusammengefasst.

1. Wählen Sie **Warnungen** und dann **Aktionen verwalten** aus.

    ![Schaltfläche „Aktionen verwalten“](./media/action-groups/manage-action-groups.png)
    
1. Wählen Sie **Aktionsgruppe hinzufügen** aus, und füllen Sie die relevanten Felder im Assistenten aus.

    ![Der Befehl „Aktionsgruppe hinzufügen“](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Konfigurieren grundlegender Aktionsgruppeneinstellungen

Gehen Sie unter **Projektdetails** wie folgt vor:

Wählen Sie das **Abonnement** und die **Ressourcengruppe** aus, in der die Aktionsgruppe gespeichert ist.

Gehen Sie unter **Instanzendetails** wie folgt vor:

1. Geben Sie einen **Aktionsgruppennamen** ein.

1. Geben Sie einen **Anzeigenamen** ein. Der Anzeigename wird anstelle eines vollständigen Aktionsgruppennamens verwendet, wenn Benachrichtigungen unter Verwendung dieser Gruppe gesendet werden.

      ![Das Dialogfeld „Aktionsgruppe hinzufügen“](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Konfigurieren von Benachrichtigungen

1. Klicken Sie auf die Schaltfläche **Weiter: Benachrichtigungen >** , um zur Registerkarte **Benachrichtigungen** zu wechseln, oder wählen Sie die Registerkarte **Benachrichtigungen** am oberen Rand des Bildschirms aus.

1. Definieren Sie eine Liste von Benachrichtigungen, die bei Auslösung einer Warnung gesendet werden. Geben Sie für jede Benachrichtigung Folgendes an:

    a. **Benachrichtigungstyp**: Wählen Sie den Typ der zu sendenden Benachrichtigung aus. Verfügbare Optionen:
      * E-Mail an Azure Resource Manager-Rolle: Sendet eine E-Mail an Benutzer, die bestimmten ARM-Rollen auf Abonnementebene zugewiesen sind.
      * E-Mail/SMS/Push/Sprachanruf: Sendet diese Benachrichtigungstypen an bestimmte Empfänger.
    
    b. **Name**: Geben Sie einen eindeutigen Namen für die Benachrichtigung ein.

    c. **Details**: Geben Sie je nach ausgewähltem Benachrichtigungstyp eine E-Mail-Adresse, eine Telefonnummer usw. ein.
    
    d. **Allgemeines Warnungsschema**: Sie können auch das [allgemeine Warnungsschema](./alerts-common-schema.md) verwenden, das den Vorteil einer einzelnen erweiterbaren und einheitlichen Warnungsnutzlast für alle Benachrichtigungsdienste in Azure Monitor bietet.

    ![Die Registerkarte „Benachrichtigungen“](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Konfigurieren von Aktionen

1. Klicken Sie auf die Schaltfläche **Weiter: Aktionen >** , um zur Registerkarte **Aktionen** zu wechseln, oder wählen Sie die Registerkarte **Aktionen** am oberen Rand des Bildschirms aus.

1. Definieren Sie eine Liste von Aktionen, die bei Auslösung einer Warnung gestartet werden. Geben Sie für jede Aktion Folgendes an:

    a. **Aktionstyp:** Wählen Sie „Automation-Runbook“, „Azure-Funktion“, „ITSM“, „Logik-App“, „Sicherer Webhook“ oder „Webhook“ aus.
    
    b. **Name**: Geben Sie einen eindeutigen Namen für die Aktion ein.

    c. **Details**: Geben Sie je nach Aktionstyp einen Webhook-URI, eine Azure-App, eine ITSM-Verbindung oder ein Automation-Runbook ein. Legen Sie für die ITSM-Aktion darüber hinaus **Arbeitselement** und andere Felder fest, die Ihr ITSM-Tool benötigt.
    
    d. **Allgemeines Warnungsschema**: Sie können auch das [allgemeine Warnungsschema](./alerts-common-schema.md) verwenden, das den Vorteil einer einzelnen erweiterbaren und einheitlichen Warnungsnutzlast für alle Benachrichtigungsdienste in Azure Monitor bietet.
    
    ![Die Registerkarte „Aktionen“](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>Erstellen der Aktionsgruppe

1. Bei Interesse können Sie sich die Einstellungen für **Tags** genauer ansehen. Auf diese Weise können Sie der Aktionsgruppe Schlüssel-Wert-Paare für die Kategorisierung zuordnen. Dieses Feature ist für jede Azure-Ressource verfügbar.

    ![Die Registerkarte „Tags“](./media/action-groups/action-group-4-tags.png)
    
1. Klicken Sie auf **Überprüfen + erstellen**, um die Einstellungen zu überprüfen. Dadurch wird eine schnelle Überprüfung Ihrer Eingaben vorgenommen, um sicherzustellen, dass alle erforderlichen Felder ausgewählt sind. Wenn Probleme vorliegen, werden diese hier angezeigt. Nachdem Sie die Einstellungen überprüft haben, klicken Sie auf **Erstellen**, um die Aktionsgruppe bereitzustellen.
    
    ![Die Registerkarte „Überprüfen + erstellen“](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Wenn Sie eine Aktion konfigurieren, um eine Person per E-Mail oder SMS zu benachrichtigen, erhält diese Person eine Bestätigung mit dem Hinweis, dass sie der Aktionsgruppe hinzugefügt wurde.

## <a name="manage-your-action-groups"></a>Verwalten von Aktionsgruppen

Nachdem Sie eine Aktionsgruppe erstellt haben, können Sie **Aktionsgruppen** anzeigen, indem Sie auf der Landing Page **Warnungen** im Bereich **Monitor** auf **Aktionen verwalten** klicken. Wählen Sie die Aktionsgruppe, die Sie verwalten möchten, um Folgendes zu tun:

* Fügen Sie Aktionen hinzu, bearbeiten oder entfernen Sie diese.
* Löschen der Aktionsgruppe.

## <a name="action-specific-information"></a>Aktionsspezifische Informationen

> [!NOTE]
> Informationen zu den numerischen Grenzwerten für die unten angegebenen Elemente finden Sie unter [Grenzwerte für Monitor](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits).  

### <a name="automation-runbook"></a>Automation Runbook
Informationen zu den Einschränkungen für Runbook-Nutzlasten finden Sie unter [Azure-Abonnementdienstgrenzen](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Runbook-Aktionen verfügen. 

### <a name="azure-app-push-notifications"></a>Pushbenachrichtigungen der Azure-App
Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Azure-App-Aktionen verfügen.

### <a name="email"></a>Email
E-Mails werden von den folgenden E-Mail-Adressen gesendet. Achten Sie darauf, dass Ihre E-Mail-Filterung ordnungsgemäß konfiguriert ist.
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von E-Mail-Aktionen verfügen. Weitere Informationen finden Sie im Artikel [Ratenlimits für Sprache, SMS-Nachrichten, E-Mail-Nachrichten, Azure App-Pushbenachrichtigungen und Webhookbeiträge](./alerts-rate-limiting.md).

### <a name="email-azure-resource-manager-role"></a>E-Mail an Azure Resource Manager-Rolle
Senden Sie eine E-Mail an die Mitglieder dieser Rolle im Abonnement. Die E-Mail wird nur an Mitglieder der Rolle gesendet, die **Azure AD-Benutzer** sind. E-Mail wird nicht an Azure AD-Gruppen oder Dienstprinzipale gesendet.

Eine Benachrichtigungs-E-Mail wird nur an die *primäre E-Mail-Adresse* gesendet.

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von E-Mail-Aktionen verfügen. Weitere Informationen finden Sie im Artikel [Ratenlimits für Sprache, SMS-Nachrichten, E-Mail-Nachrichten, Azure App-Pushbenachrichtigungen und Webhookbeiträge](./alerts-rate-limiting.md).

### <a name="function"></a>Funktion
Diese Aktion ruft einen vorhandenen HTTP-Triggerendpunkt in [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app) auf.

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Functions-Aktionen verfügen.

### <a name="itsm"></a>ITSM
Für eine ITSM-Aktion muss eine ITSM-Verbindung hergestellt werden. Informieren Sie sich, wie Sie [eine ITSM-Verbindung erstellen](./itsmc-overview.md).

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von ITSM-Aktionen verfügen. 

### <a name="logic-app"></a>Logik-App
Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Logik-App-Aktionen verfügen.

### <a name="secure-webhook"></a>Sicherer Webhook

> [!NOTE]
> Zur Verwendung der Webhookaktion ist es erforderlich, dass der Webhookendpunkt des Ziels entweder keine Details der Warnung zum erfolgreichen Funktionieren benötigt oder die Warnungskontextinformationen, die als Teil des POST-Vorgangs bereitgestellt werden, analysieren kann. Wenn der Webhookendpunkt die Warnungskontextinformationen nicht selbst verarbeiten kann, können Sie eine Lösung wie eine [Logik-App-Aktion](./action-groups-logic-app.md) zur benutzerdefinierten Bearbeitung der Warnungskontextinformationen verwenden, um sie an das erwartete Datenformat des Webhooks anzupassen.

Mithilfe der Aktion „Aktionsgruppenwebhook“ können Sie Azure Active Directory nutzen, um die Verbindung zwischen Ihrer Aktionsgruppe und Ihrer geschützten Web-API (Webhookendpunkt) zu sichern. Der gesamte Workflow für das Nutzen dieser Funktionalität wird unten beschrieben. Eine Übersicht über Azure AD-Anwendungen und -Dienstprinzipale finden Sie unter [Microsoft Identity Platform (v2.0): Übersicht](../../active-directory/develop/v2-overview.md).

1. Erstellen Sie eine Azure AD-Anwendung für ihre geschützte Web-API. Weitere Informationen finden Sie unter [Geschützte Web-API: App-Registrierung](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Konfigurieren Sie Ihre geschützte API so, dass Sie von einer [Daemon-App aufgerufen wird](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Aktivieren Sie Aktionsgruppen, um Ihre Azure AD-Anwendung zu verwenden.

    > [!NOTE]
    > Sie müssen Mitglied der [Rolle „Azure AD-Anwendungsadministrator“](../../active-directory/roles/permissions-reference.md#available-roles) sein, um dieses Skript ausführen zu können.
    
    - Ändern Sie den Connect-AzureAD-Befehl des PowerShell-Skripts so, dass Ihre Azure AD-Mandanten-ID verwendet wird.
    - Ändern Sie die Variable „$myAzureADApplicationObjectId“ des PowerShell-Skripts, um die Objekt-ID Ihrer Azure AD-Anwendung zu verwenden.
    - Führen Sie das geänderte Skript aus.
    
3. Konfigurieren Sie die Aktion für den sicheren Aktionsgruppen-Webhook.
    - Kopieren Sie den Wert von $MyApp.ObjectID aus dem Skript, und geben Sie ihn in das Feld Anwendungsobjekt-ID in der Webhookaktionsdefinition ein.
    
    ![Sichere Webhookaktion](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>PowerShell-Skript für sicheren Webhook

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>sms
Weitere wichtige Informationen finden Sie in den Artikeln zu [Ratenlimits](./alerts-rate-limiting.md) und zum [Verhalten von SMS-Benachrichtigungen](./alerts-sms-behavior.md). 

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von SMS-Aktionen verfügen.

> [!NOTE]
> Wenn Sie auf der Benutzeroberfläche für die Aktionsgruppe im Azure-Portal Ihren Länder-/Regionscode nicht auswählen können, wird SMS für Ihr Land bzw. Ihre Region nicht unterstützt.  Wenn Ihr Länder-/Regionscode nicht verfügbar ist, können Sie über das [Benutzerfeedback](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice) dafür stimmen, dass Ihr Land bzw. Ihre Region hinzugefügt wird. In der Zwischenzeit besteht eine Umgehungslösung darin, dass Ihre Aktionsgruppe einen Webhook bei einem anderen SMS-Anbieter mit Unterstützung in Ihrem Land bzw. Ihrer Region anruft.  

Die Preise für unterstützte Länder/Regionen sind auf der [Seite mit der Preisübersicht für Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) aufgeführt.
  

### <a name="voice"></a>Sprache
Weitere wichtige Informationen finden Sie im Artikeln zu [Ratenlimits](./alerts-rate-limiting.md).

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Sprachaktionen verfügen.

> [!NOTE]
> Wenn Sie auf der Benutzeroberfläche für die Aktionsgruppe im Azure-Portal Ihren Länder-/Regionscode nicht auswählen können, werden Sprachanrufe für Ihr Land bzw. Ihre Region nicht unterstützt. Wenn Ihr Länder-/Regionscode nicht verfügbar ist, können Sie über das [Benutzerfeedback](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice) dafür stimmen, dass Ihr Land bzw. Ihre Region hinzugefügt wird.  In der Zwischenzeit besteht eine Umgehungslösung darin, dass Ihre Aktionsgruppe einen Webhook bei einem anderen Anbieter von Sprachanrufen mit Unterstützung in Ihrem Land bzw. Ihrer Region anruft.  

Die Preise für unterstützte Länder/Regionen sind auf der [Seite mit der Preisübersicht für Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) aufgeführt.

### <a name="webhook"></a>Webhook

> [!NOTE]
> Zur Verwendung der Webhookaktion ist es erforderlich, dass der Webhookendpunkt des Ziels entweder keine Details der Warnung zum erfolgreichen Funktionieren benötigt oder die Warnungskontextinformationen, die als Teil des POST-Vorgangs bereitgestellt werden, analysieren kann. Wenn der Webhookendpunkt die Warnungskontextinformationen nicht selbst verarbeiten kann, können Sie eine Lösung wie eine [Logik-App-Aktion](./action-groups-logic-app.md) zur benutzerdefinierten Bearbeitung der Warnungskontextinformationen verwenden, um sie an das erwartete Datenformat des Webhooks anzupassen.

Webhooks werden mithilfe der folgenden Regeln verarbeitet:
- Ein Webhookaufruf wird maximal dreimal versucht.
- Der Aufruf wird wiederholt, wenn innerhalb des Timeoutzeitraums keine Antwort empfangen wird oder einer der folgenden HTTP-Statuscodes zurückgegeben wird: 408, 429, 503 oder 504
- Der erste Aufruf wartet 10 Sekunden auf eine Antwort.
- Der zweite und der dritte Versuch warten 30 Sekunden auf eine Antwort.
- Nachdem bei allen drei Versuchen, den Webhook aufzurufen, ein Fehler aufgetreten ist, ruft keine der Aktionsgruppen den Endpunkt während der nächsten 15 Minuten auf.

Quell-IP-Adressbereiche:

 - 13.66.60.119/32
 - 13.66.143.220/30
 - 13.66.202.14/32
 - 13.66.248.225/32
 - 13.66.249.211/32
 - 13.67.10.124/30
 - 13.69.109.132/30
 - 13.71.199.112/30
 - 13.77.53.216/30
 - 13.77.172.102/32
 - 13.77.183.209/32
 - 13.78.109.156/30
 - 13.84.49.247/32
 - 13.84.51.172/32
 - 13.84.52.58/32
 - 13.86.221.220/30
 - 13.106.38.142/32
 - 13.106.38.148/32
 - 13.106.54.3/32
 - 13.106.54.19/32
 - 13.106.57.181/32
 - 13.106.57.196/31
 - 20.38.149.132/30
 - 20.42.64.36/30
 - 20.43.121.124/30
 - 20.44.17.220/30
 - 20.45.123.236/30
 - 20.72.27.152/30
 - 20.150.172.228/30
 - 20.192.238.124/30
 - 20.193.202.4/30
 - 40.68.195.137/32
 - 40.68.201.58/32
 - 40.68.201.65/32
 - 40.68.201.206/32
 - 40.68.201.211/32
 - 40.68.204.18/32
 - 40.115.37.106/32
 - 40.121.219.215/32
 - 40.121.221.62/32
 - 40.121.222.201/32
 - 40.121.223.186/32
 - 51.104.9.100/30
 - 52.183.20.244/32
 - 52.183.31.0/32
 - 52.183.94.59/32
 - 52.184.145.166/32
 - 191.233.50.4/30
 - 191.233.207.64/26
 - 2603:1000:4:402::178/125
 - 2603:1000:104:402::178/125
 - 2603:1010:6:402::178/125
 - 2603:1010:101:402::178/125
 - 2603:1010:304:402::178/125
 - 2603:1010:404:402::178/125
 - 2603:1020:5:402::178/125
 - 2603:1020:206:402::178/125
 - 2603:1020:305:402::178/125
 - 2603:1020:405:402::178/125
 - 2603:1020:605:402::178/125
 - 2603:1020:705:402::178/125
 - 2603:1020:805:402::178/125
 - 2603:1020:905:402::178/125
 - 2603:1020:a04:402::178/125
 - 2603:1020:b04:402::178/125
 - 2603:1020:c04:402::178/125
 - 2603:1020:d04:402::178/125
 - 2603:1020:e04:402::178/125
 - 2603:1020:f04:402::178/125
 - 2603:1020:1004:800::f8/125
 - 2603:1020:1104:400::178/125
 - 2603:1030:f:400::978/125
 - 2603:1030:10:402::178/125
 - 2603:1030:104:402::178/125
 - 2603:1030:107:400::f0/125
 - 2603:1030:210:402::178/125
 - 2603:1030:40b:400::978/125
 - 2603:1030:40c:402::178/125
 - 2603:1030:504:802::f8/125
 - 2603:1030:608:402::178/125
 - 2603:1030:807:402::178/125
 - 2603:1030:a07:402::8f8/125
 - 2603:1030:b04:402::178/125
 - 2603:1030:c06:400::978/125
 - 2603:1030:f05:402::178/125
 - 2603:1030:1005:402::178/125
 - 2603:1040:5:402::178/125
 - 2603:1040:207:402::178/125
 - 2603:1040:407:402::178/125
 - 2603:1040:606:402::178/125
 - 2603:1040:806:402::178/125
 - 2603:1040:904:402::178/125
 - 2603:1040:a06:402::178/125
 - 2603:1040:b04:402::178/125
 - 2603:1040:c06:402::178/125
 - 2603:1040:d04:800::f8/125
 - 2603:1040:f05:402::178/125
 - 2603:1040:1104:400::178/125
 - 2603:1050:6:402::178/125
 - 2603:1050:403:400::1f8/125

Um Updates über Änderungen an diesen IP-Adressen zu erhalten, empfehlen wir Ihnen, eine Service Health-Warnung zu konfigurieren, die eine Überwachung auf Informationsbenachrichtigungen des Aktionsgruppendiensts ausführt.

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Webhookaktionen verfügen.

Häufige Aktualisierungen der Quell-IP-Adressen können im Webhook recht zeitaufwändig sein. Die Verwendung von **Diensttags** für *ActionGroup* hilft bei der Minimierung der Komplexität von häufigen manuellen Aktualisierungen von IP-Adressen. Die oben angegebenen Präfixe für Quell-IP-Adressbereiche,für die das **Diensttag** gilt, werden automatisch von Microsoft verwaltet.

#### <a name="service-tag"></a>Diensttag
Ein Diensttag steht für eine Gruppe von IP-Adresspräfixen eines bestimmten Azure-Diensts. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern. Auf diese Weise wird die Komplexität häufiger Updates an Netzwerksicherheitsregeln für eine Aktionsgruppe minimiert.

1. Suchen Sie im Azure-Portal unter „Azure-Dienste“ nach *Netzwerksicherheitsgruppe*.
2. Klicken Sie auf **Hinzufügen**, und erstellen Sie eine Netzwerksicherheitsgruppe.

   1. Fügen Sie den Ressourcengruppennamen hinzu, und geben Sie die *Instanzdetails* ein.
   1. Klicken Sie auf **Überprüfen + erstellen** und dann auf *Erstellen*.
   
   :::image type="content" source="media/action-groups/action-group-create-security-group.png" alt-text="Beispiel für das Erstellen einer Netzwerksicherheitsgruppe"border="true":::

3. Wechseln Sie zur Ressourcengruppe, und klicken Sie dann auf die erstellte *Netzwerksicherheitsgruppe*.

    1. Klicken Sie auf *Eingangssicherheitsregeln*.
    1. Klicken Sie auf **Hinzufügen**.
    
    :::image type="content" source="media/action-groups/action-group-add-service-tag.png" alt-text="Beispiel für das Hinzufügen eines Diensttags"border="true":::

4. Im rechten Bereich wird ein neues Fenster geöffnet.
    1.  Auswählen der Quelle: **Diensttag**
    1.  Quelldiensttag: **ActionGroup**
    1.  Klicken Sie auf **Hinzufügen**.
    
    :::image type="content" source="media/action-groups/action-group-service-tag.png" alt-text="Beispiel für das Hinzufügen eines Diensttags"border="true":::

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [SMS-Warnungsverhalten in Aktionsgruppen](./alerts-sms-behavior.md).  
* Erweitern Sie Ihr [Verständnis des Webhookschemas für Aktivitätsprotokollwarnungen](./activity-log-alerts-webhook.md).  
* Weitere Informationen zum ITSM-Connector finden Sie [hier](./itsmc-overview.md).
* Weitere Informationen zu [Ratenlimits](./alerts-rate-limiting.md) für Warnungen.
* Verschaffen Sie sich eine [Übersicht über Aktivitätsprotokollwarnungen](./alerts-overview.md), und erfahren Sie, wie Sie Warnungen empfangen können.  
* Erfahren Sie, wie Sie [Warnungen konfigurieren, wenn eine Dienstintegritätsbenachrichtigung gesendet wird](../../service-health/alerts-activity-log-service-notifications-portal.md).
