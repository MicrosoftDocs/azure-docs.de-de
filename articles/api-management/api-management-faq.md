---
title: FAQ zu Azure API Management | Microsoft Docs
description: In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen (FAQ) sowie Informationen zu bewährten Methoden in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: edc3c2ddcddaa8a51ae634a7abcd3a61aab530e9
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357249"
---
# <a name="azure-api-management-faqs"></a>Häufig gestellte Fragen zu Azure API Management
Hier erhalten Sie Antworten auf häufig gestellte Fragen sowie Informationen zu Mustern und bewährten Methoden für Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>Kontakt
* [Wie kann ich dem Team von Microsoft Azure API Management eine Frage stellen?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
* [Was bedeutet es, wenn sich ein Feature in der Vorschauphase befindet?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Wie kann ich die Verbindung zwischen dem API Management-Gateway und meinen Back-End-Diensten sichern?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Wie kopiere ich meine API Management-Dienstinstanz in eine neue Instanz?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Kann ich meine API Management-Instanz programmgesteuert verwalten?](#can-i-manage-my-api-management-instance-programmatically)
* [Wie füge ich Benutzer zur Gruppe „Administratoren“ hinzu?](#how-do-i-add-a-user-to-the-administrators-group)
* [Weshalb ist die Richtlinie, die ich hinzufügen möchte, im Richtlinien-Editor nicht verfügbar?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Wie richte ich mehrere Umgebungen in einer einzelnen API ein?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Kann ich SOAP mit API Management verwenden?](#can-i-use-soap-with-api-management)
* [Ist die Gateway-IP-Adresse für API Management konstant? Kann ich sie in Firewallregeln verwenden?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* [Kann ich einen OAuth 2.0-Autorisierungsserver mit AD FS-Sicherheit konfigurieren?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Welche Routingmethode verwendet API Management in Bereitstellungen an mehreren geografischen Standorten?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Kann ich eine Azure Resource Manager-Vorlage zum Erstellen einer API Management-Dienstinstanz verwenden?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Kann ich ein selbstsigniertes SSL-Zertifikat für ein Back-End verwenden?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Warum erhalte ich einen Authentifizierungsfehler, wenn ich versuche, ein Git-Repository zu klonen?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Kann API Management mit Azure ExpressRoute verwendet werden?](#does-api-management-work-with-azure-expressroute)
* [Warum benötigen wir in Resource Manager-VNETs ein dediziertes Subnetz, wenn API Management in ihnen bereitgestellt wird?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Welche minimale Subnetzgröße ist erforderlich, wenn API Management in einem VNET bereitgestellt wird?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Kann ich einen API Management-Dienst in ein anderes Abonnement verschieben?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Sind Einschränkungen oder Probleme hinsichtlich des Imports meiner API bekannt?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Wie kann ich dem Team von Microsoft Azure API Management eine Frage stellen?
Sie können uns folgendermaßen kontaktieren:

* Stellen Sie Ihre Fragen im [MSDN-Forum zu API Management](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Senden Sie eine E-Mail an <mailto:apimgmt@microsoft.com>.
* Featureanfragen können im [Azure-Feedbackforum](https://feedback.azure.com/forums/248703-api-management) an uns gesendet werden.

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Was bedeutet es, wenn sich ein Feature in der Vorschauphase befindet?
Wenn sich ein Feature in der Vorschauphase befindet, bedeutet das, dass wir sehr an Benutzerfeedback zu diesem Feature interessiert sind. Ein Feature in der Vorschauphase ist funktionell abgeschlossen. Als Reaktion auf Kundenfeedback nehmen wir jedoch unter Umständen noch eine grundlegende Änderung am Feature vor. Es wird empfohlen, sich in einer Produktionsumgebung nicht auf ein Feature in der Vorschauphase zu verlassen. Wenn Sie Feedback zu Vorschaufeatures haben, können Sie uns Ihre Kommentare über eine der unter [Wie kann ich dem Microsoft Azure API Management-Team eine Frage stellen?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question) beschriebenen Kontaktoptionen senden.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Wie kann ich die Verbindung zwischen dem API Management-Gateway und meinen Back-End-Diensten sichern?
Sie haben verschiedene Optionen, die Verbindung zwischen dem API Management-Gateway und Ihren Back-End-Diensten zu sichern. Ihre Möglichkeiten:

* Verwenden Sie die HTTP-Standardauthentifizierung. Weitere Informationen finden Sie unter [Import and publish your first API](import-and-publish.md) (Importieren und Veröffentlichen Ihrer ersten API).
* Verwenden Sie die gegenseitige SSL-Authentifizierung wie unter [Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung in Azure API Management](api-management-howto-mutual-certificates.md) beschrieben.
* Verwenden Sie IP-Whitelists für Ihren Back-End-Dienst. Die IP-Adresse des Gateways bleibt in allen Tarifen von API Management (mit wenigen [Ausnahmen](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)) konstant. Sie können Ihre Positivliste so konfigurieren, dass diese IP-Adresse zugelassen wird. Sie können die IP-Adresse Ihrer API Management-Instanz im Azure-Portal auf dem Dashboard abrufen.
* Verbinden Sie Ihre API Management-Instanz mit einer Azure Virtual Network-Instanz.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Wie kopiere ich meine API Management-Dienstinstanz in eine neue Instanz?
Wenn Sie eine API Management-Instanz in eine neue Instanz kopieren möchten, haben Sie verschiedene Möglichkeiten. Ihre Möglichkeiten:

* Verwenden Sie die Sicherungs- und Wiederherstellungsfunktion in API Management. Weitere Informationen finden Sie unter [So implementieren Sie die Notfallwiederherstellung mit Sichern und Wiederherstellen von Diensten in Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* Erstellen Sie mithilfe der [API Management-REST-API](/rest/api/apimanagement/) ein eigenes Sicherungs- und Wiederherstellungsfeature. Verwenden Sie die REST-API zum Speichern und Wiederherstellen der Entitäten aus der gewünschten Dienstinstanz.
* Laden Sie die Dienstkonfiguration unter Verwendung von Git herunter, und laden Sie sie anschließend in eine neue Instanz hoch. Weitere Informationen finden Sie unter [Speichern und Konfigurieren der API Management-Dienstkonfiguration mit Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Kann ich meine API Management-Instanz programmgesteuert verwalten?
Ja, Sie können API Management unter Verwendung der folgenden Komponenten programmgesteuert verwalten:

* [API Management-REST-API](/rest/api/apimanagement/)
* [Microsoft Azure ApiManagement Service Management Library SDK](https://aka.ms/apimsdk)
* PowerShell-Cmdlets für [Dienstbereitstellung](https://docs.microsoft.com/powershell/module/wds) und [Dienstverwaltung](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Wie füge ich Benutzer zur Gruppe „Administratoren“ hinzu?
So fügen Sie Benutzer zur Gruppe „Administratoren“ hinzu:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Wechseln Sie zur Ressourcengruppe mit der API Management-Instanz, die Sie aktualisieren möchten.
3. Weisen Sie in API Management dem Benutzer die Rolle **Api Management Contributor** (Mitwirkender für API Management) hinzu.

Der neu hinzugefügte Mitwirkende kann nun Azure PowerShell-[Cmdlets](https://docs.microsoft.com/powershell/azure/overview) verwenden. So melden Sie sich als Administrator an:

1. Verwenden Sie das `Connect-AzAccount`-Cmdlet, um sich anzumelden.
2. Legen Sie den Kontext auf das Abonnement fest, das den Dienst enthält. Verwenden Sie hierfür `Set-AzContext -SubscriptionID <subscriptionGUID>`.
3. Rufen Sie mithilfe von `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>` eine URL für einmaliges Anmelden ab.
4. Greifen Sie über die URL auf das Verwaltungsportal zu.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Weshalb ist die Richtlinie, die ich hinzufügen möchte, im Richtlinien-Editor nicht verfügbar?
Ist die Richtlinie, die Sie hinzufügen möchten, im Richtlinien-Editor abgeblendet, stellen Sie sicher, dass Sie sich im richtigen Bereich für die Richtlinie befinden. Jede Richtlinienanweisung ist für die Verwendung in bestimmten Bereichen und Richtlinienabschnitten vorgesehen. Informationen zu den Richtlinienabschnitten und Bereichen für eine Richtlinie finden Sie unter [API Management policies](/azure/api-management/api-management-policies) (API Management-Richtlinien) im Abschnitt „Verwendung“ der jeweiligen Richtlinie.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Wie richte ich mehrere Umgebungen in einer einzelnen API ein?
Wenn Sie in einer API mehrere Umgebungen einrichten möchten, beispielsweise eine Testumgebung und eine Produktionsumgebung, haben Sie zwei Möglichkeiten. Ihre Möglichkeiten:

* Hosten Sie verschiedene APIs im gleichen Mandanten.
* Hosten Sie die gleichen APIs in verschiedenen Mandanten.

### <a name="can-i-use-soap-with-api-management"></a>Kann ich SOAP mit API Management verwenden?
[SOAP Pass-Through](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/)-Unterstützung ist jetzt verfügbar. Administratoren können die WSDL des SOAP-Diensts importieren. Azure API Management erstellt dann ein SOAP-Front-End. Für SOAP-Dienste stehen eine Dokumentation zum Entwicklerportal, eine Testkonsole, Richtlinien und Analysen zur Verfügung.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>Ist die Gateway-IP-Adresse für API Management konstant? Kann ich sie in Firewallregeln verwenden?
In allen Tarifen von API Management ist die öffentliche IP-Adresse (VIP) des API Management-Mandanten während der gesamten Lebensdauer des Mandanten bis auf einige Ausnahmen statisch. Die IP-Adresse ändert sich in folgenden Situationen:

* Der Dienst wird gelöscht und anschließend neu erstellt.
* Das Dienstabonnement wird [gesperrt](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states), oder es wird eine diesbezügliche [Warnung](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) ausgegeben (z.B. aufgrund von nicht geleisteten Zahlungen), und anschließend wird es reaktiviert.
* Sie fügen ein Azure Virtual Network hinzu oder entfernen diesen Dienst (Virtual Network kann nur im Entwickler- und Premium-Tarif verwendet werden).

Bei Bereitstellungen in mehreren Regionen ändert sich die regionale Adresse, wenn die Region verlassen und anschließend reaktiviert wird (Bereitstellungen in mehreren Regionen können nur im Premium-Tarif verwendet werden).

Mandanten mit Premium-Tarif, die für die Bereitstellung in mehreren Regionen konfiguriert sind, wird eine öffentliche IP-Adresse pro Region zugewiesen.

Sie können die IP-Adresse (bzw. die Adressen bei Bereitstellungen in mehreren Regionen) auf der Mandantenseite im Azure-Portal anzeigen.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Kann ich einen OAuth 2.0-Autorisierungsserver mit AD FS-Sicherheit konfigurieren?
Informationen zum Konfigurieren eines OAuth 2.0-Autorisierungsservers mit AD FS-Sicherheit (Active Directory Federation Services, Active Directory-Verbunddienste) finden Sie unter [Using AD FS in API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/) (Verwenden von AD FS in API Management).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Welche Routingmethode verwendet API Management in Bereitstellungen an mehreren geografischen Standorten?
API Management verwendet die [Routingmethode für Leistungsdatenverkehr](../traffic-manager/traffic-manager-routing-methods.md#performance) in Bereitstellungen an mehreren geografischen Standorten. Eingehender Datenverkehr wird zum nächstgelegenen API-Gateway weitergeleitet. Wenn eine Region offline geht, wird der eingehende Datenverkehr automatisch an das nächstgelegene Gateway weitergeleitet. Weitere Informationen zu Routingmethoden finden Sie unter [Traffic Manager-Methoden für das Datenverkehrsrouting](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Kann ich eine Azure Resource Manager-Vorlage zum Erstellen einer API Management-Dienstinstanz verwenden?
Ja. Weitere Informationen finden Sie in den Schnellstartvorlagen für den [Azure API Management-Dienst](https://aka.ms/apimtemplate).

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Kann ich ein selbstsigniertes SSL-Zertifikat für ein Back-End verwenden?
Ja. Sie können dafür PowerShell verwenden oder das Zertifikat direkt an die API übermitteln. Dadurch wird die Überprüfung der Zertifikatkette deaktiviert, und Sie können selbstsignierte oder privat signierte Zertifikate für die Kommunikation zwischen API Management und den Back-End-Diensten verwenden.

#### <a name="powershell-method"></a>PowerShell-Methode ####
Verwenden Sie das PowerShell-Cmdlet [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (für ein neues Back-End) oder [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (für ein vorhandenes Back-End), und legen Sie den Parameter `-SkipCertificateChainValidation` auf `True` fest. 

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Direkte API-Updatemethode ####
1. Erstellen Sie mit API Management eine [Back-End](/rest/api/apimanagement/)-Entität.     
2. Legen Sie die **skipCertificateChainValidation**-Eigenschaft auf **true** fest.     
3. Wenn Sie die Verwendung selbstsignierter Zertifikate nicht mehr zulassen möchten, löschen Sie die Back-End-Entität, oder legen Sie die **skipCertificateChainValidation**-Eigenschaft auf **false** fest.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Warum erhalte ich einen Authentifizierungsfehler, wenn ich versuche, ein Git-Repository zu klonen?
Wenn Sie die Git-Anmeldeinformationsverwaltung verwenden oder versuchen, ein Git-Repository mithilfe von Visual Studio zu klonen, tritt möglicherweise ein bekanntes Problem mit dem Windows-Dialogfeld für die Anmeldeinformationen auf. Das Dialogfeld beschränkt die Kennwortlänge auf 127 Zeichen und schneidet dadurch das von Microsoft generierte Kennwort ab. Wir arbeiten daran, das Kennwort zu verkürzen. Verwenden Sie vorerst Git Bash zum Klonen des Git-Repositorys.

### <a name="does-api-management-work-with-azure-expressroute"></a>Kann API Management mit Azure ExpressRoute verwendet werden?
Ja. API Management kann mit Azure ExpressRoute verwendet werden.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Warum benötigen wir in Resource Manager-VNETs ein dediziertes Subnetz, wenn API Management in ihnen bereitgestellt wird?
API Management erfordert ein dediziertes Subnetz, weil es auf dem klassischen Bereitstellungsmodell (PAAS V1-Ebene) basiert. Die Bereitstellung in einem Resource Manager-VNET (V2 Layer) ist möglich, hat jedoch bestimmte Konsequenzen. Das klassische Bereitstellungsmodell in Azure ist nicht eng an das Resource Manager-Modell gekoppelt. Daher ist für die V1-Ebene das Erstellen einer Ressource auf der V2-Ebene nicht erkennbar, und es können Probleme auftreten. Zum Beispiel kann von API Management versucht werden, eine IP-Adresse zu verwenden, die bereits einer Netzwerkschnittstellenkarte (die auf V2 basiert) zugewiesen ist.
Weitere Informationen über den Unterschied zwischen dem klassischen und dem Resource Manager-Modell in Azure finden Sie unter [Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Welche minimale Subnetzgröße ist erforderlich, wenn API Management in einem VNET bereitgestellt wird?
Die minimale Subnetzgröße für die Bereitstellung von API Management beträgt [/29](../virtual-network/virtual-networks-faq.md#configuration). Dies ist die minimale Subnetzgröße, die von Azure unterstützt wird.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Kann ich einen API Management-Dienst in ein anderes Abonnement verschieben?
Ja. Informationen hierzu finden Sie unter [Move resources to a new resource group or subscription](../azure-resource-manager/resource-group-move-resources.md) (Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Sind Einschränkungen oder Probleme hinsichtlich des Imports meiner API bekannt?
[Bekannte Probleme und Einschränkungen](api-management-api-import-restrictions.md) beim Import der Formate Open API (Swagger), WSDL und WADL.
