---
title: Übersicht über das Entwicklerportal in Azure API Management
titleSuffix: Azure API Management
description: Informieren Sie sich über das Entwicklerportal in API Management, eine anpassbare Website, auf der API-Consumer Ihre APIs untersuchen können.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 30487218fc95be75d22b5a9ea5a6dbc224ffd025
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074796"
---
# <a name="overview-of-the-developer-portal"></a>Übersicht über das Entwicklerportal

Das Entwicklerportal ist eine automatisch generierte, vollständig anpassbare Website mit der Dokumentation Ihrer APIs. Dort können API-Consumer ihre APIs ermitteln, erfahren, wie sie verwendet werden, Zugriff anfordern und die APIs ausprobieren.

In diesem Artikel werden die Unterschiede zwischen selbstgehosteten und verwalteten Versionen des Entwicklerportals in API Management beschrieben. Außerdem erhalten Sie hier Antworten auf häufig gestellte Fragen.

![Entwicklerportal für API Management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migration vom Legacyportal

> [!IMPORTANT]
> Das Legacyentwicklerportal ist mittlerweile veraltet, und es werden nur noch Sicherheitsupdates angewendet. Sie können die Anwendung bis Oktober 2023 wie gewohnt weiterhin verwenden, daraufhin wird sie aus allen API Management-Diensten entfernt.

Die Migration zum neuen Entwicklerportal ist in einem [eigenen Artikel in der Dokumentation](developer-portal-deprecated-migration.md) beschrieben.

## <a name="customization-and-styling"></a>Anpassung und Formatierung

Das Entwicklerportal kann mit dem integrierten, visuellen Drag & Drop-Editor angepasst und formatiert werden. Weitere Einzelheiten finden Sie in [diesem Tutorial](api-management-howto-developer-portal-customize.md).

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Erweiterbarkeit

Der API Management-Dienst umfasst ein integriertes **verwaltetes** Entwicklerportal, das laufend aktualisiert wird. Sie können über die Benutzeroberfläche des Azure-Portals darauf zugreifen.

Wenn Sie es mit benutzerdefinierter Logik erweitern müssen, was standardmäßig nicht unterstützt wird, können Sie die entsprechende Codebasis ändern. Die Codebasis des Portals ist [in einem GitHub-Repository verfügbar][1]. Beispielsweise können Sie ein neues Widget implementieren, das in das Supportsystem eines Drittanbieters integriert ist. Wenn Sie neue Funktionen implementieren, können Sie aus folgenden Optionen wählen:

- **Selbsthosting** des resultierenden Portals außerhalb Ihres API Management-Diensts. Wenn Sie das Portal selbst hosten, sind Sie der Maintainer und selbst für dessen Upgrades verantwortlich. Die Unterstützung durch den Azure-Support ist nur auf die grundlegende Einrichtung selbst gehosteter Portale beschränkt, wie im [Bereich „Wiki“ des Repositorys][2] dokumentiert.
- Öffnen Sie einen Pull Request, damit das API Management-Team neue Funktionen mit der Codebasis des **verwalteten** Portals zusammenführen kann.

Details und Anweisungen zur Erweiterbarkeit finden Sie im [GitHub-Repository][1] und im [Tutorial zum Implementieren eines Widgets][3]. Das [Tutorial zum Anpassen des verwalteten Portals](api-management-howto-developer-portal-customize.md) führt Sie schrittweise durch den Verwaltungsbereich des Portals, der sowohl in der **verwalteten** als auch in der **selbstgehosteten** Version vorhanden ist.

## <a name="frequently-asked-questions"></a><a name="faq"></a> Häufig gestellte Fragen

In diesem Abschnitt beantworten wir häufig gestellte Fragen zum Entwicklerportal, die allgemeiner Natur sind. Fragen, die sich speziell auf die selbstgehostete Version beziehen, finden Sie im [GitHub-Repository im Abschnitt „Wiki“](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> Wie kann ich von der Vorschauversion des Portals migrieren?

Beim ersten Starten der Vorschauversion des Entwicklerportals haben Sie die Vorschauversion des Standardinhalts in Ihrem API Management-Dienst bereitgestellt. In der allgemein verfügbaren Version wurde der Standardinhalt erheblich geändert. So enthält die Vorschauversion des Standardinhalts beispielsweise auf der Anmeldeseite keine OAuth-Schaltflächen, sie verwendet zur Anzeige von APIs andere Widgets, und die Strukturierung der Seiten im Entwicklerportal wird mit eingeschränkten Funktionen ausgeführt. Trotz der inhaltlichen Unterschiede wird die Engine des Portals (samt der zugrunde liegenden Widgets) bei jeder Veröffentlichung des Entwicklerportals automatisch aktualisiert.

Wenn Sie Ihr Portal basierend auf der Vorschauversion des Inhalts umfangreich angepasst haben, können Sie es unverändert weiter verwenden und neue Widgets manuell auf den Seiten des Portals platzieren. Andernfalls wird empfohlen, den Inhalt Ihres Portals durch den neuen Standardinhalt zu ersetzen.

Wenn Sie den Inhalt in einem verwalteten Portal zurücksetzen möchten, wählen Sie im Menüabschnitt **Vorgänge** die Option **Inhalt zurücksetzen** aus. Dadurch wird der gesamte Inhalt des Portals entfernt, und die neuen Standardinhalte werden bereitgestellt. Alle Anpassungen und Änderungen des Entwicklerportals gehen dabei verloren. **Dieser Vorgang kann nicht rückgängig gemacht werden**.

![Zurücksetzen des Portalinhalts](media/api-management-howto-developer-portal/reset-content.png)

Wenn Sie die selbstgehostete Version verwenden, führen Sie die Skripts `scripts.v2/cleanup.bat` und `scripts.v2/generate.bat` aus dem GitHub-Repository aus, um vorhandene Inhalte zu entfernen und neue Inhalte bereitzustellen. Stellen Sie sicher, dass Sie im Vorfeld den Code Ihres Portals im GitHub-Repository auf die neueste Version aktualisieren.

Wenn Sie zum ersten Mal nach der Ankündigung der allgemeinen Verfügbarkeit im November 2019 auf das Portal zugegriffen haben, sollte der neue Standardinhalt bereits vorhanden sein. In diesem Fall ist keine weitere Aktion erforderlich.

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Eine von mir benötigte Funktion wird im Portal nicht unterstützt

Sie können im [GitHub-Repository][1] eine Funktionsanforderung öffnen oder die [fehlende Funktionalität selbst implementieren][3]. Weitere Informationen finden Sie im Abschnitt **Erweiterbarkeit**.

### <a name="how-can-i-automate-portal-deployments"></a><a id="automate"></a> Wie kann ich Portalbereitstellungen automatisieren?

Sie können über die REST-API programmgesteuert auf die Inhalte des Entwicklerportals zugreifen und diese verwalten, und zwar unabhängig davon, ob Sie eine verwaltete oder eine selbstgehostete Version verwenden.

Die APIs sind im [Wiki-Abschnitt des GitHub-Repositorys][2] dokumentiert. Diese Dokumentation kann für die Automatisierung von Migrationen von Portalinhalten zwischen Umgebungen verwendet werden (z. B. aus einer Testumgebung in die Produktionsumgebung). Weitere Informationen zu diesem Vorgang finden Sie [in diesem Dokumentationsartikel](https://aka.ms/apimdocs/migrateportal) auf GitHub.

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>Wie kann ich von der verwalteten Version zur selbstgehosteten Version wechseln?

Informationen finden Sie im ausführlichen Artikel im [Bereich „Wiki“ des Entwicklerportal-Repositorys auf GitHub][2].

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Kann ich über mehrere Entwicklerportale in einem API Management-Dienst verfügen?

Sie können über ein verwaltetes Portal und mehrere selbstgehostete Portale verfügen. Der Inhalt aller Portale wird in demselben API Management-Dienst gespeichert, sodass die Portale identisch sind. Wenn das Aussehen und die Funktionalität der einzelnen Portale unterschiedlich sein soll, können Sie diese eigenständig mit Ihren benutzerdefinierten Widgets hosten, die Seiten zur Runtime dynamisch anpassen, beispielsweise basierend auf der URL.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Unterstützt das Portal Azure Resource Manager-Vorlagen und/oder ist es mit dem API Management DevOps Resource Kit kompatibel?

Nein.

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Wird der Inhalt des Portals mit der Sicherungs-/Wiederherstellungsfunktion in API Management gespeichert?

Nein.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Muss ich zusätzliche VNET-Konnektivität für die Abhängigkeiten des verwalteten Portals aktivieren?

In den meisten Fällen nicht.

Wenn sich Ihr API Management-Dienst in einem internen VNET befindet, kann nur innerhalb des Netzwerks auf Ihr Entwicklerportal zugegriffen werden. Der Hostname des Verwaltungsendpunkts muss über den Computer, mit dem Sie auf die Verwaltungsschnittstelle des Portals zugreifen, zur internen VIP des Diensts aufgelöst werden. Vergewissern Sie sich, dass der Verwaltungsendpunkt im DNS registriert ist. Ist die Konfiguration fehlerhaft, wird ein Fehler angezeigt: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`.

Wenn sich Ihr API Management-Dienst in einem internen VNET befindet und Sie über Application Gateway über das Internet darauf zugreifen, stellen Sie sicher, dass Sie die Konnektivität mit dem Entwicklerportal und den Verwaltungsendpunkten von API Management aktivieren. Möglicherweise müssen Sie die Firewallregeln für Webanwendungen deaktivieren. Weitere Informationen finden Sie in [diesem Dokumentationsartikel](api-management-howto-integrate-internal-vnet-appgateway.md).

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Ich habe eine benutzerdefinierte API Management-Domäne zugewiesen, und das veröffentlichte Portal funktioniert nicht.

Nachdem Sie die Domäne aktualisiert haben, müssen Sie [das Portal erneut veröffentlichen](api-management-howto-developer-portal-customize.md#publish), damit die Änderungen wirksam werden.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Ich habe einen Identitätsanbieter hinzugefügt, er wird im Portal aber nicht angezeigt.

Nachdem Sie einen Identitätsanbieter (beispielsweise Azure AD oder Azure AD B2C) konfiguriert haben, müssen Sie das [Portal erneut veröffentlichen](api-management-howto-developer-portal-customize.md#publish), damit die Änderungen wirksam werden. Stellen Sie sicher, dass die Seiten des Entwicklerportals das OAuth-Schaltflächen-Widget enthalten.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Ich habe die Delegierung eingerichtet, sie wird vom Portal aber nicht verwendet.

Nachdem Sie die Delegierung eingerichtet haben, müssen Sie [das Portal erneut veröffentlichen](api-management-howto-developer-portal-customize.md#publish), damit die Änderungen wirksam werden.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Meine anderen API Management-Konfigurationsänderungen wurden nicht an das Entwicklerportal weitergegeben.

Bei den meisten Konfigurationsänderungen (beispielsweise für das VNET, für die Anmeldung und für Produktbedingungen) ist eine [erneute Veröffentlichung des Portals](api-management-howto-developer-portal-customize.md#publish) erforderlich.

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Beim Verwenden der interaktiven Konsole erhalte ich einen CORS-Fehler.

Die interaktive Konsole sendet eine clientseitige API-Anforderung über den Browser. Beheben Sie das CORS-Problem, indem Sie [eine CORS-Richtlinie](api-management-cross-domain-policies.md#CORS) für Ihre API(s) hinzufügen.

Sie können den Status der CORS-Richtlinie im Abschnitt **Portalübersicht** Ihres API Management-Diensts im Azure-Portal überprüfen. Ein Warnfeld zeigt eine fehlende oder falsch konfigurierte Richtlinie an.

![Screenshot, der zeigt, wo Sie den Status Ihrer CORS-Richtlinie überprüfen können.](media/api-management-howto-developer-portal/cors-azure-portal.png)

Wenden Sie die COLRS-Richtlinie automatisch an, indem Sie auf die Schaltfläche **CORS aktivieren** klicken.

Sie können CORS auch manuell aktivieren.

1. Wählen Sie den Link **Manuelle Anwendung auf globaler Ebene** aus, um den generierten Richtliniencode anzuzeigen.
2. Navigieren Sie im Azure-Portal im Abschnitt **APIs** Ihres API Management-Diensts zu **Alle APIs**.
3. Wählen Sie im Abschnitt **Eingehende Verarbeitung** das Symbol **</>** aus.
4. Fügen Sie die Richtlinie in den Abschnitt **<inbound>** der XML-Datei ein. Stellen Sie sicher, dass der **<origin>** -Wert mit der Domäne Ihres Entwicklerportals übereinstimmt.

> [!NOTE]
> 
> Wenn Sie die CORS-Richtlinie nicht im API-Bereich, sondern im Produktbereich anwenden und Ihre API die Abonnementschlüsselauthentifizierung über einen Header verwendet, funktioniert Ihre Konsole nicht.
>
> Der Browser gibt automatisch eine HTTP-Anforderung vom Typ „OPTIONS“ aus, die keinen Header mit dem Abonnementschlüssel enthält. Aufgrund des fehlenden Abonnementschlüssels kann API Management den OPTIONS-Aufruf keinem Produkt zuordnen und somit die CORS-Richtlinie nicht anwenden.
>
> Zur Umgehung dieses Problems können Sie den Abonnementschlüssel in einem Abfrageparameter übergeben.

> [!NOTE]
> 
> Es wird nur eine CORS-Richtlinie ausgeführt. Wenn Sie mehrere CORS-Richtlinien angegeben haben (z. B. auf API-Ebene und auf der Ebene alle APIs), funktioniert Ihre interaktive Konsole möglicherweise nicht wie erwartet.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Welche Berechtigungen sind zum Bearbeiten des Entwicklerportals erforderlich?

Sollte beim Öffnen des Portals im Verwaltungsmodus der Fehler `Oops. Something went wrong. Please try again later.` angezeigt werden, verfügen Sie möglicherweise nicht über die erforderlichen Berechtigungen (Azure RBAC).

Bei den Legacyportalen war die Berechtigung `Microsoft.ApiManagement/service/getssotoken/action` im Dienstbereich (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) erforderlich, um dem Benutzer Administratorzugriff auf die Portale zu gewähren. Für das neue Portal ist die Berechtigung `Microsoft.ApiManagement/service/users/token/action` im Bereich `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` erforderlich.

Sie können das folgende PowerShell-Skript verwenden, um eine Rolle mit der erforderlichen Berechtigung zu erstellen. Vergessen Sie nicht, den Parameter `<subscription-id>` zu ändern. 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Nachdem die Rolle erstellt wurde, kann sie im Azure-Portal im Abschnitt **Zugriffssteuerung (IAM)** einem beliebigen Benutzer zugewiesen werden. Wenn Sie diese Rolle einem Benutzer zuweisen, wird die Berechtigung im Dienstbereich zugewiesen. Der Benutzer kann SAS-Token im Namen *jedes beliebigen* Benutzers im Dienst generieren. Diese Rolle muss mindestens dem Administrator des Diensts zugewiesen werden. Der folgende PowerShell-Befehl zeigt, wie Sie die Rolle dem Benutzer `user1` im kleinstmöglichen Bereich zuweisen, um dem Benutzer keine unnötigen Berechtigungen zu erteilen: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Nach Erteilung der Berechtigungen muss sich der betreffende Benutzer abmelden und erneut beim Azure-Portal anmelden, damit die neuen Berechtigungen wirksam werden.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Der Fehler `Unable to start the portal. See if settings are specified correctly (...)` wird angezeigt.

Dieser Fehler wird angezeigt, wenn ein Aufruf vom Typ `GET` für `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` nicht erfolgreich war. Der Aufruf wird vom Browser über die Verwaltungsschnittstelle des Portals ausgegeben.

Wenn sich Ihr API Management-Dienst in einem VNET befindet, lesen Sie weiter oben die Frage zur VNET-Konnektivität.

Der Aufruffehler kann auch auf ein TLS/SSL-Zertifikat zurückzuführen sein, das einer benutzerdefinierten Domäne zugewiesen ist und dem der Browser nicht vertraut. Sie können die benutzerdefinierte Domäne des Verwaltungsendpunkts entfernen. API Management greift daraufhin auf den Standardendpunkt mit einem vertrauenswürdigen Zertifikat zurück.

### <a name="whats-the-browser-support-for-the-portal"></a>Was ist die Browserunterstützung für das Portal?

| Browser                     | Unterstützt       |
|-----------------------------|-----------------|
| Apple Safari                | Ja<sup>1</sup> |
| Google Chrome               | Ja<sup>1</sup> |
| Microsoft Edge              | Ja<sup>1</sup> |
| Microsoft Internet Explorer | Nein              |
| Mozilla Firefox             | Ja<sup>1</sup> |

 <small><sup>1</sup> In den beiden neuesten Produktionsversionen unterstützt.</small>

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum neuen Entwicklerportal:

- [Zugreifen auf das neue Entwicklerportal und Verwalten des Portals](api-management-howto-developer-portal-customize.md)
- [Einrichten einer selbstgehosteten Version des Portals][2]
- [Implementieren Ihres eigenen Widgets][3]

Weitere Ressourcen:

- [GitHub-Repository mit dem Quellcode][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
