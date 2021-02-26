---
title: Übersicht über die Azure Automation-Kontoauthentifizierung
description: Dieser Artikel enthält eine Übersicht über die Azure Automation-Kontoauthentifizierung.
keywords: Automation-Sicherheit, sicher Automation; Automation-Authentifizierung
services: automation
ms.subservice: process-automation
ms.date: 02/01/2021
ms.topic: conceptual
ms.openlocfilehash: e3ed14a0845d90a1b9b951dd5175acf6233f8718
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254998"
---
# <a name="automation-account-authentication-overview"></a>Übersicht über die Automation-Kontoauthentifizierung

Mit Azure Automation können Sie Aufgaben für Ressourcen in Azure, lokal und mit anderen Cloudanbietern, z.B. Amazon Web Services (AWS), automatisieren. Mit Runbooks können Sie Ihre Aufgaben automatisieren. Wenn Sie Geschäfts- oder Betriebsprozesse außerhalb von Azure verwalten müssen, verwenden Sie einen Hybrid Runbook Worker. In beiden Umgebungen werden Berechtigungen benötigt, um mit minimalen Rechten sicher auf die Ressourcen zugreifen zu können.

In diesem Artikel werden von Azure Automation unterstützte Authentifizierungsszenarien beschrieben. Außerdem werden die ersten Schritte für die Umgebungen erläutert, die Sie verwalten müssen.

## <a name="automation-account"></a>Automation-Konto

Wenn Sie das erste Mal mit Azure Automation arbeiten, müssen Sie zunächst mindestens ein Automation-Konto erstellen. Mithilfe von Automation-Konten können Sie Ihre Automation-Ressourcen, Runbooks, Objekte und Konfigurationen von den Ressourcen anderer Konten isolieren. Sie können Automation-Konten dazu verwenden, Ressourcen in separate logische Umgebungen oder delegierte Verantwortlichkeiten zu trennen. Beispielsweise können Sie ein Konto für die Entwicklung, ein Konto für die Produktion und ein Konto für Ihre lokale Umgebung nutzen. Sie können auch ein Automation-Konto zum Verwalten von Betriebssystemupdates auf allen Computern mit [Updateverwaltung](update-management/overview.md) festlegen. 

Ein Azure Automation-Konto unterscheidet sich von einem Microsoft-Konto, das unter Ihrem Azure-Abonnement erstellt wird. Eine Einführung in die Erstellung eines Automation-Kontos finden Sie unter [Erstellen eines Azure Automation-Kontos](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Automation-Ressourcen

Die Automation-Ressourcen für jedes Automation-Konto sind zwar mit einer einzelnen Azure-Region verknüpft, mit dem Konto können jedoch alle Ressourcen in Ihrem Azure-Abonnement verwaltet werden. Automation-Konten werden in erster Linie dann in unterschiedlichen Regionen erstellt, wenn Sie über Richtlinien verfügen, die eine Isolierung von Daten und Ressourcen innerhalb einer spezifischen Region erfordern.

Alle Aufgaben, die Sie für Ressourcen mit Azure Resource Manager und den PowerShell-Cmdlets in Azure Automation erstellen, müssen gegenüber Azure mit Azure Active Directory (Azure AD) basierend auf den Anmeldeinformationen für die Organisationsidentität authentifiziert werden.

## <a name="run-as-accounts"></a>Ausführende Konten

Ausführende Konten in Azure Automation bieten Authentifizierung für die Verwaltung von Azure Resource Manager-Ressourcen oder von Ressourcen, die im klassischen Bereitstellungsmodell bereitgestellt wurden. In Azure Automation gibt es zwei Arten von ausführenden Konten:

* Ausführendes Azure-Konto: Ermöglicht das Verwalten von Azure-Ressourcen basierend auf dem Azure Resource Manager-Bereitstellungs- und -Verwaltungsdienst für Azure.
* Klassisches ausführendes Azure-Konto: Ermöglicht das Verwalten klassischer Azure-Ressourcen basierend auf dem klassischen Bereitstellungsmodell.

Weitere Informationen zum Resource Manager-Bereitstellungsmodell und zum klassischen Bereitstellungsmodell finden Sie unter [Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung](../azure-resource-manager/management/deployment-models.md).

>[!NOTE]
>Azure Cloud Solution Provider-Abonnements (Azure CSP) unterstützen nur das Azure Resource Manager-Modell. Dienste, die nicht auf Azure Resource Manager basieren, sind in diesem Programm nicht verfügbar. Wenn Sie ein CSP-Abonnement verwenden, wird kein klassisches ausführendes Azure-Konto erstellt, sondern das ausführende Azure-Konto. Weitere Informationen zu CSP-Abonnements finden Sie unter [Verfügbare Dienste in CSP-Abonnements](/azure/cloud-solution-provider/overview/azure-csp-available-services).

Wenn Sie ein Automation-Konto erstellen, wird standardmäßig gleichzeitig ein ausführendes Konto erstellt. Wenn Sie sich entscheiden, es nicht zusammen mit dem Automation-Konto zu erstellen, können Sie es zu einem späteren Zeitpunkt separat erstellen. Ein klassisches ausführendes Azure-Konto ist optional und wird getrennt erstellt, wenn Sie klassische Ressourcen verwalten müssen.

### <a name="run-as-account"></a>Ausführendes Konto

Ein ausführendes Azure-Konto wird für die folgenden Aufgaben verwendet:

* Erstellung einer Azure AD-Anwendung mit einem selbstsignierten Zertifikat, Erstellung eines Dienstprinzipalkonto für die Anwendung in Azure AD und Zuweisung der Rolle [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor) für das Konto in Ihrem aktuellen Abonnement. Sie können die Zertifikateinstellung in [Leser](../role-based-access-control/built-in-roles.md#reader) oder eine beliebige andere Rolle ändern. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure Automation](automation-role-based-access-control.md).

* Erstellt ein Automation-Zertifikatasset mit dem Namen `AzureRunAsCertificate` im angegebenen Automation-Konto. Das Zertifikatasset enthält den privaten Schlüssel des Zertifikats, der von der Azure AD-Anwendung verwendet wird.

* Erstellt ein Automation-Verbindungsasset mit dem Namen `AzureRunAsConnection` im angegebenen Automation-Konto. Die Verbindungsressource enthält die Anwendungs-ID, die Mandanten-ID, die Abonnement-ID und den Zertifikatfingerabdruck.

### <a name="azure-classic-run-as-account"></a>Klassisches ausführendes Azure-Konto

Ein klassisches ausführendes Azure-Konto wird für die folgenden Aufgaben verwendet:

> [!NOTE]
> Sie müssen Co-Administrator für das Abonnement sein, um diese Art Konto zu erstellen oder zu erneuern.

* Erstellen eines Verwaltungszertifikats im Abonnement.

* Erstellen eines Automation-Zertifikatassets mit dem Namen `AzureClassicRunAsCertificate` im angegebenen Automation-Konto. Das Zertifikatasset enthält den privaten Zertifikatschlüssel, der vom Verwaltungszertifikat verwendet wird.

* Erstellen ein Automation-Verbindungsassets mit dem Namen `AzureClassicRunAsConnection` im angegebenen Automation-Konto. Das Verbindungsasset enthält den Abonnementnamen, die Abonnement-ID und den Namen des Zertifikatassets.

## <a name="service-principal-for-run-as-account"></a>Dienstprinzipal für ausführendes Konto

Der Dienstprinzipal für ein ausführendes Konto verfügt standardmäßig über keine Leseberechtigungen für Azure AD. Wenn Sie Berechtigungen zum Lesen oder Verwalten von Azure AD hinzufügen möchten, müssen Sie dem Dienstprinzipal diese Berechtigung unter **API-Berechtigungen** erteilen. Weitere Informationen finden Sie unter [Hinzufügen von Berechtigungen für den Zugriff auf Ihre Web-API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Berechtigungen des ausführenden Kontos

In diesem Abschnitt werden Berechtigungen sowohl für normale ausführende Konten als auch für klassische ausführende Konten definiert.

* Ein Anwendungsadministrator in Azure Active Directory und ein Besitzer in einem Abonnement können sämtliche Aufgaben zum Erstellen oder Aktualisieren eines ausführenden Kontos erledigen.
* Zum Konfigurieren oder Erneuern klassischer ausführender Konten benötigen Sie die Rolle „Co-Administrator“ auf Abonnementebene. Weitere Informationen zu klassischen Abonnementberechtigungen finden Sie unter [Administratoren für klassische Azure-Abonnements](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

In der folgenden Tabelle werden die Aufgaben, das entsprechende Cmdlet und die erforderlichen Berechtigungen für Situationen mit Aufgabentrennung aufgeführt:

|Aufgabe|Cmdlet  |Mindestberechtigungen  |Hier legen Sie die Berechtigungen fest|
|---|---------|---------|---|
|Erstellen einer Azure AD-Anwendung|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Rolle „Anwendungsentwickler“<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > App-Registrierungen |
|Hinzufügen von Anmeldeinformationen zur Anwendung|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | „Anwendungsadministrator“ oder „Globaler Administrator“<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > App-Registrierungen|
|Erstellen und Abrufen eines Azure AD-Dienstprinzipals|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | „Anwendungsadministrator“ oder „Globaler Administrator“<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > App-Registrierungen|
|Zuweisen oder Abrufen der Azure-Rolle für den angegebenen Prinzipal|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | „Benutzerzugriffsadministrator“ oder „Besitzer“, oder muss die folgenden Berechtigungen besitzen:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Abonnement](../role-based-access-control/role-assignments-portal.md)</br>Start > Abonnements > \<subscription name\> -Access Control (IAM)|
|Erstellen oder Entfernen eines Automation-Zertifikats|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Mitwirkender der Ressourcengruppe         |Ressourcengruppe des Automation-Kontos|
|Erstellen oder Entfernen einer Automation-Verbindung|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Mitwirkender der Ressourcengruppe |Ressourcengruppe des Automation-Kontos|

<sup>1</sup> Benutzer Ihres Azure AD-Mandanten, die keine Administratoren sind, können [AD-Anwendungen registrieren](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app), wenn die Option **Benutzer können Anwendungen registrieren** des Azure AD-Mandanten auf der Seite **Benutzereinstellungen** auf **Ja** festgelegt ist. Wenn die Anwendungsregistrierungseinstellung **Nein** lautet, muss der Benutzer, der diese Aktion ausführt, eine der in dieser Tabelle definierten Rollen besitzen.

Wenn Sie kein Mitglied der Active Directory-Instanz des Abonnements sind, bevor Sie der Rolle „Globaler Administrator“ des Abonnements hinzugefügt werden, werden Sie als Gast hinzugefügt. In diesem Fall wird auf der Seite **Automation-Konto hinzufügen** eine Warnung vom Typ `You do not have permissions to create…` angezeigt.

So überprüfen Sie, ob die Situation, die die Fehlermeldung erzeugt, behoben wurde

1. Wählen Sie im Azure-Portal im Bereich „Azure Active Directory“ **Benutzer und Gruppen** aus.
2. Wählen Sie **Alle Benutzer**.
3. Wählen Sie Ihren Namen aus, und wählen Sie dann **Profil** aus.
4. Stellen Sie sicher, dass der Wert des Attributs **Benutzertyp** im Profil des Benutzers nicht auf **Gast** festgelegt ist.

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Die rollenbasierte Zugriffssteuerung ist für Azure Resource Manager verfügbar, um einem Azure AD-Benutzerkonto und ausführenden Konto zulässige Aktionen zu gewähren und den Dienstprinzipal zu authentifizieren. Weitere Informationen zur Entwicklung Ihres Modells zum Verwalten von Automation-Berechtigungen finden Sie im Artikel [Rollenbasierte Zugriffssteuerung in Azure Automation](automation-role-based-access-control.md) .

Wenn Sie über strikte Sicherheitskontrollen für die Zuweisung von Berechtigungen in Ressourcengruppen verfügen, müssen Sie der Rolle **Mitwirkender** in der Ressourcengruppe die Mitgliedschaft beim ausführenden Konto zuweisen.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbookauthentifizierung mit Hybrid Runbook Worker

Für Runbooks, die in Ihrem Rechenzentrum über einen Hybrid Runbook Worker oder für Computingdienste in anderen Cloudumgebungen wie AWS ausgeführt werden, kann nicht das gleiche Verfahren verwendet werden, das normalerweise für die Authentifizierung von Runbooks bei Azure-Ressourcen genutzt wird. Der Grund: Diese Ressourcen werden außerhalb von Azure ausgeführt, weshalb für die Authentifizierung bei Ressourcen, auf die lokal zugegriffen wird, ihre in Automation definierten Sicherheitsanmeldeinformationen verwendet werden müssen. Weitere Informationen zur Runbookauthentifizierung mit Runbook Workern finden Sie unter [Ausführen von Runbooks auf einem Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

Für Runbooks, die Hybrid Runbook Worker auf virtuellen Azure-Computern nutzen, können Sie anstelle von ausführenden Konten die [Authentifizierung von Runbooks mit verwalteten Identitäten](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) für die Authentifizierung bei Ihren Azure-Ressourcen verwenden.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Erstellen eines Automation-Kontos über das Azure-Portal finden Sie unter [Erstellen eines eigenständigen Azure Automation-Kontos](automation-create-standalone-account.md).
* Wenn Sie Ihr Konto lieber mithilfe einer Vorlage erstellen möchten, finden Sie weitere Informationen unter [Erstellen eines Automation-Kontos mithilfe einer Azure Resource Manager-Vorlage](quickstart-create-automation-account-template.md).
* Informationen zur Authentifizierung mithilfe von Amazon Web Services finden Sie unter [Authentifizieren von Azure Automation-Runbooks mit Amazon Web Services](automation-config-aws-account.md).