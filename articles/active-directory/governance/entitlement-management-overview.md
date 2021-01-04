---
title: Was ist Berechtigungsverwaltung? – Azure AD
description: Verschaffen Sie sich einen Überblick über die Berechtigungsverwaltung von Azure Active Directory, und lesen Sie, wie Sie mit ihr den Zugriff auf Gruppen, Anwendungen und SharePoint Online-Websites für interne und externe Benutzer verwalten können.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 11/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.custom: contperf-fy21q1
ms.openlocfilehash: c9815355b26a9c14c02110e4bb5fff4f998d2105
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032031"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Was ist die Azure AD-Berechtigungsverwaltung?

Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) ist ein [Identitätsgovernance](identity-governance-overview.md)-Feature, mit dem Organisationen Identitäten und Zugriffszyklus skaliert verwalten können, indem sie Zugriffsanforderungsworkflows, Zugriffszuweisungen, Überprüfungen und Ablaufzeiten automatisieren.

Mitarbeiter in Organisationen benötigen für ihre Aufgaben Zugriff auf verschiedene Gruppen, Anwendungen und Websites. Die Verwaltung dieses Zugriffs ist eine Herausforderung, da sich die Anforderungen ändern, weil neue Anwendungen hinzugefügt werden oder Benutzer zusätzliche Zugriffsrechte benötigen.  Dieses Szenario wird noch komplizierter, wenn Sie mit externen Organisationen zusammenarbeiten, weil Sie u. U. nicht wissen, wer in der anderen Organisation Zugriff auf die Ressourcen Ihres Unternehmens benötigt, und diese Organisationen nicht wissen, welche Anwendungen, Gruppen oder Standorte in Ihrer Organisation verwendet werden.

Mit der Berechtigungsverwaltung von Azure AD können Sie den Zugriff auf Gruppen, Anwendungen und SharePoint Online-Websites für interne Benutzer und Benutzer außerhalb der Organisation, die auf diese Ressourcen zugreifen müssen, effizienter verwalten.

## <a name="why-use-entitlement-management"></a>Argumente für die Berechtigungsverwaltung

Die Verwaltung des Zugriffs von Mitarbeitern auf Ressourcen stellt Unternehmen häufig vor Herausforderungen, z. B.:

- Die Benutzer wissen möglicherweise nicht, welche Zugriffsrechte erforderlich sind. Aber auch dann, wenn Sie dies wissen, kann es schwierig sein, die richtigen Personen zu finden, die den Zugriff genehmigen.
- Wenn Benutzer den richtigen Zugriff auf eine Ressource erhalten haben, verfügen sie möglicherweise länger über diesen Zugriff, als für die Geschäftszwecke erforderlich ist.

Diese Probleme sind für Benutzer, die Zugriff aus einer anderen Organisation benötigen, z. B. externe Benutzer in Lieferkettenorganisationen oder bei Geschäftspartnern, noch größer. Beispiel:

- Kein Einzelner kann alle Personen in den Verzeichnissen anderer Organisationen kennen und einladen.
- Selbst wenn sie diese Benutzer einladen könnten, kann niemand in der betreffenden Organisation den Zugriff aller Benutzer konsistent verwalten.

Diese Herausforderungen können mit der Azure AD-Berechtigungsverwaltung leichter bewältigt werden.  Wenn Sie mehr darüber erfahren möchten, wie Kunden die Berechtigungsverwaltung von Azure AD verwendet haben, können Sie die [Avanade-Fallstudie](https://customers.microsoft.com/story/avanade-professional-services-azure-canada) und die [Centrica-Fallstudie](https://customers.microsoft.com/story/757467-centrica-energy-azure) lesen.  Dieses Video bietet einen Überblick über die Berechtigungsverwaltung und ihren Nutzen:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Welche Möglichkeiten bietet mir die Berechtigungsverwaltung?

Einige Funktionen der Berechtigungsverwaltung:

- Delegieren der Möglichkeit, Zugriffspakete zu erstellen, an Nicht-Administratoren. Diese Zugriffspakete enthalten Ressourcen, die von Benutzern angefordert werden können, und die delegierten Zugriffspaket-Manager können Richtlinien mit Regeln definieren, dein festlegen, welche Benutzer Zugriff anfordern können, wer den Zugriff genehmigen muss und wann der Zugriff abläuft.
- Auswählen verbundener Organisationen, deren Benutzer Zugriff anfordern können.  Wenn ein Benutzer, der sich noch nicht im Verzeichnis befindet, Zugriff anfordert und dieser genehmigt wird, wird er automatisch in Ihr Verzeichnis eingeladen und Zugriff zugewiesen.  Wenn der Zugriff abläuft und keine anderen Zugriffspaketzuweisungen vorhanden sind, kann das betreffende B2B-Konto in Ihrem Verzeichnis automatisch entfernt werden.

>[!NOTE]
>Wenn Sie die Berechtigungsverwaltung ausprobieren möchten, können Sie mit dem [Tutorial zum Erstellen des ersten Zugriffspakets](entitlement-management-access-package-first.md) beginnen.

Sie können auch die [gängigen Szenarios](entitlement-management-scenarios.md) durchlesen oder Videos ansehen, z. B.:

- [Bereitstellen der Azure AD-Berechtigungsverwaltung in Ihrer Organisation](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Überwachen und Skalieren der Verwendung der Azure AD-Berechtigungsverwaltung](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Delegieren in der Berechtigungsverwaltung](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Was sind Zugriffspakete und welche Ressourcen können damit verwaltet werden?

Die Berechtigungsverwaltung führt in Azure AD das Konzept von *Zugriffspaketen* ein. Ein Zugriffspaket ist ein Bündel aller Ressourcen mit den Zugriffsrechten, die ein Benutzer benötigt, um an einem Projekt zu arbeiten oder seine Aufgaben zu erledigen. Zugriffspakete dienen zum Steuern des Zugriffs für die internen Mitarbeiter und auch für Benutzer außerhalb Ihrer Organisation.

 Mit der Berechtigungsverwaltung können Sie den Benutzerzugriff auf folgende Ressourcen verwalten:

- Mitgliedschaft in Azure AD-Sicherheitsgruppen
- Mitgliedschaft in Microsoft 365-Gruppen und -Teams
- Zuweisung zu Azure AD-Unternehmensanwendungen wie SaaS-Anwendungen und kundenspezifisch integrierten Anwendungen, die Verbund-/Einzelanmeldung und/oder Bereitstellung unterstützen
- Mitgliedschaft in SharePoint Online-Websites

Sie können auch den Zugriff auf andere Ressourcen steuern, die auf Azure AD-Sicherheitsgruppen oder Microsoft 365-Gruppen basieren.  Beispiel:

- Sie können Benutzerlizenzen für Microsoft 365 mit einer Azure AD-Sicherheitsgruppe in einem Zugriffspaket vergeben und die [gruppenbasierte Lizenzierung](../enterprise-users/licensing-groups-assign.md) für diese Gruppe konfigurieren.
- Sie können Benutzern mithilfe einer Azure AD-Sicherheitsgruppe in einem Zugriffspaket und durch Erstellen einer [Azure-Rollenzuweisung](../../role-based-access-control/role-assignments-portal.md) für diese Gruppe Zugriff zum Verwalten von Azure-Ressourcen erteilen.
- Sie können mithilfe von Gruppen, die Azure AD-Rollen in einem Zugriffspaket zugewiesen werden können, und durch das [Zuweisen einer Azure AD-Rolle zu dieser Gruppe](../roles/groups-assign-role.md) Benutzern Zugriff zum Verwalten von Azure AD-Rollen gewähren.

## <a name="how-do-i-control-who-gets-access"></a>Wie kann gesteuert werden, wer Zugriff erhält?

Bei einem Zugriffspaket listet ein Administrator oder delegierter Zugriffspaket-Manager die Ressourcen (Gruppen, Apps und Websites) sowie die Rollen auf, die die Benutzer für diese Ressourcen benötigen.

Zugriffspakete enthalten außerdem eine oder mehrere *Richtlinien*. Eine Richtlinie definiert die Regeln oder Leitlinien für die Zuweisung zu einem Zugriffspaket. Die einzelnen Richtlinien können verwendet werden, um sicherzustellen, dass nur die entsprechenden Benutzer Zugriff anfordern können, dass es genehmigende Personen für die Anforderung gibt und dass der Zugriff auf die betreffenden Ressourcen zeitlich begrenzt ist und abläuft, sofern er nicht erneuert wird.

![Zugriffspakete und Zugriffspaketrichtlinien](./media/entitlement-management-overview/elm-overview-access-package.png)

In jeder Richtlinie definiert ein Administrator oder Zugriffspaket-Manager:

- Die bereits vorhandenen Benutzer (in der Regel Mitarbeiter oder bereits eingeladene Gäste) oder die Partnerorganisationen von externen Benutzern, die Zugriff anfordern dürfen
- Den Genehmigungsprozess und die Benutzer, die den Zugriff genehmigen oder verweigern können
- Die Dauer der Zuweisung des Benutzerzugriffs nach der Genehmigung, bevor die Zuweisung abläuft

Im folgenden Diagramm wird ein Beispiel für die verschiedenen Elemente der Berechtigungsverwaltung gezeigt. Es zeigt einen Katalog mit zwei exemplarischen Zugriffspaketen.

- **Zugriffspaket 1** enthält als Ressource eine einzelne Gruppe. Der Zugriff wird mit einer Richtlinie definiert, die einer Gruppe von Benutzern im Verzeichnis das Anfordern des Zugriffs ermöglicht.
- **Zugriffspaket 2** enthält als Ressourcen eine Gruppe, eine Anwendung und eine SharePoint Online-Website. Der Zugriff wird mit zwei verschiedenen Richtlinien definiert. Die erste Richtlinie ermöglicht einer Gruppe von Benutzern im Verzeichnis das Anfordern des Zugriffs. Die zweite Richtlinie ermöglicht Benutzern in einem externen Verzeichnis das Anfordern des Zugriffs.

![Übersicht über die Berechtigungsverwaltung](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Wann sollten Zugriffspakete verwendet werden?

Zugriffspakete ersetzen keine anderen Mechanismen für die Zugriffszuweisung.  Sie eignen sich am besten für folgende Situationen:

- Mitarbeiter benötigen zeitlich begrenzten Zugriff für eine bestimmte Aufgabe.  Sie können beispielsweise die gruppenbasierte Lizenzierung und eine dynamische Gruppe verwenden, um sicherzustellen, dass alle Mitarbeiter über ein Exchange Online-Postfach verfügen, und dann Zugriffspakete in Situationen verwenden, in denen Mitarbeiter zusätzliche Zugriffsrechte benötigen, z. B. zum Lesen von abteilungsspezifischen Ressourcen einer anderen Abteilung.
- Zugriff, der vom Vorgesetzten eines Mitarbeiters oder von anderen festgelegten Personen genehmigt werden muss
- Abteilungen möchten eigene Zugriffsrichtlinien für ihre Ressourcen ohne Beteiligung der IT verwalten.  
- Zwei oder mehr Organisationen arbeiten in einem Projekt zusammen, sodass mehrere Benutzer aus einer Organisation über Azure AD B2B integriert werden müssen, um auf die Ressourcen einer anderen Organisation zugreifen zu können.

## <a name="how-do-i-delegate-access"></a>Wie kann der Zugriff delegiert werden?

 Zugriffspakete sind in Containern definiert, die als *Kataloge* bezeichnet werden.  Sie können einen einzelnen Katalog für alle Zugriffspakete verwenden oder einzelne Personen festlegen, die eigene Kataloge erstellen und dafür verantwortlich sind. Ein Administrator kann Ressourcen zu jedem Katalog hinzufügen. Ein Nicht-Administrator kann jedoch nur Ressourcen, deren Besitzer er ist, zu einem Katalog hinzufügen. Ein Katalogbesitzer kann andere Benutzer als Mitbesitzer des Katalogs oder als Zugriffspaket-Manager hinzufügen.  Diese Szenarien werden ausführlicher im Artikel [Delegierung und Rollen in der Azure AD-Berechtigungsverwaltung](entitlement-management-delegate.md) beschrieben.

## <a name="summary-of-terminology"></a>Übersicht über die verwendete Terminologie

Zum besseren Verständnis der Berechtigungsverwaltung und der dazugehörigen Dokumentation sollten Sie mit den folgenden Begriffen vertraut sein.

| Begriff | BESCHREIBUNG |
| --- | --- |
| Zugriffspaket | Ein Ressourcenpaket, das von einem Team oder Projekt benötigt wird und Richtlinien unterliegt. Zugriffspakete sind immer in einem Katalog enthalten. Sie erstellen ein neues Zugriffspaket für ein Szenario, in dem Benutzer Zugriff anfordern müssen.  |
| Zugriffsanforderung | Die Anforderung des Zugriffs auf die Ressourcen in einem Zugriffspaket. Eine Anforderung durchläuft in der Regel einen Genehmigungsworkflow.  Bei einer Genehmigung erhält der anfordernde Benutzer eine Zugriffspaketzuweisung. |
| Zuweisung | Die Zuweisung eines Zugriffspakets für einen Benutzer stellt sicher, dass der Benutzer über alle Ressourcenrollen des betreffenden Zugriffspakets verfügt.  Zugriffspaketzuweisungen sind normalerweise zeitlich begrenzt, bevor sie ablaufen. |
| catalog | Ein Container verwandter Ressourcen und Zugriffspakete.  Kataloge werden für die Delegierung verwendet, damit Nicht-Administratoren eigene Zugriffspakete erstellen können. Katalogbesitzer können Ressourcen, deren Besitzer sie sind, zu einem Katalog hinzufügen. |
| Katalogersteller | Eine Auflistung der Benutzer, die berechtigt sind, neue Kataloge zu erstellen.  Wenn ein Nicht-Administratorbenutzer, der als Katalogersteller autorisiert wurde, einen neuen Katalog erstellt, wird er automatisch Besitzer des betreffenden Katalogs. |
| Verbundene Organisation | Ein externes Azure AD-Verzeichnis bzw. eine externe Domäne, zu der eine Beziehung besteht. Die Benutzer einer verbundenen Organisation können in einer Richtlinie als Benutzer angegeben werden, die Zugriff anfordern dürfen. |
| policy | Mehrere Regeln, die den Zugriffslebenszyklus definieren. Sie legen beispielsweise fest, wie Benutzer Zugriff erhalten, wer den Zugriff genehmigen darf und wie lange Benutzer durch eine Zuweisung Zugriff haben. Eine Richtlinie ist mit einem Zugriffspaket verknüpft. Ein Zugriffspaket kann beispielsweise zwei Richtlinien enthalten: eine für Mitarbeiter, um Zugriff anzufordern, und eine zweite für externe Benutzer, um Zugriff anzufordern. |
| resource | Ein Asset, z. B. eine Office-Gruppe, eine Sicherheitsgruppe, eine Anwendung oder eine SharePoint Online-Website, mit einer Rolle, für die einem Benutzer Berechtigungen erteilt werden können. |
| Ressourcenverzeichnis | Ein Verzeichnis, das mindestens eine Ressource enthält, die freigegeben (geteilt) werden soll. |
| Ressourcenrolle | Mehrere Berechtigungen, die einer Ressource zugeordnet sind und von einer Ressource definiert werden. Eine Gruppe umfasst zwei Rollen: Mitglied und Besitzer. SharePoint-Websites verfügen normalerweise über 3 Rollen, können aber über weitere benutzerdefinierte Rollen verfügen. Anwendungen können über benutzerdefinierte Rollen verfügen. |


## <a name="license-requirements"></a>Lizenzanforderungen

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Spezielle Clouds, z. B. Azure Deutschland und Azure China 21Vianet, können derzeit nicht verwendet werden.

### <a name="how-many-licenses-must-you-have"></a>Wie viele Lizenzen benötigen Sie?

Die Anzahl der Azure AD Premium P2-Lizenzen, über die Ihr Verzeichnis verfügen muss, errechnet sich anhand der folgenden Zahlen:

- Mitgliedsbenutzer, die ein Zugriffspaket anfordern **können**
- Mitgliedsbenutzer, die ein Zugriffspaket <u>anfordern</u>
- Mitgliedsbenutzer, die <u>Zugriffspaketanforderungen genehmigen</u>
- Mitgliedsbenutzer, die <u>Zugriffspaketzuweisungen überprüfen</u> 
- Mitgliedsbenutzer mit <u>direkter Zuweisung</u> zu einem Zugriffspaket

Bei Gastbenutzern hängen die Lizenzierungsanforderungen vom verwendeten [Lizenzierungsmodell](../external-identities/external-identities-pricing.md) ab. Die folgenden Gastbenutzeraktivitäten werden jedoch als Azure AD Premium P2-Nutzung betrachtet:
- Gastbenutzer, die ein Zugriffspaket <u>anfordern</u> 
- Gastbenutzer, die <u>Zugriffspaketanforderungen genehmigen</u>
- Gastbenutzer, die <u>Zugriffspaketzuweisungen überprüfen</u>
- Gastbenutzer mit <u>direkter Zuweisung</u> zu einem Zugriffspaket 

Für die folgenden Aufgaben sind **keine** Azure AD Premium P2-Lizenzen erforderlich:

- Für Benutzer mit der Rolle „globaler Administrator“, die Anfangskataloge einrichten, auf Pakete und Richtlinien zugreifen und administrative Aufgaben an andere Benutzer delegieren, sind keine Lizenzen erforderlich.
- Für Benutzer, an die administrative Aufgaben (z. B. Katalogersteller, Katalogbesitzer und Zugriffspaketmanager) delegiert wurden, sind keine Lizenzen erforderlich.
- Für Gäste, die Zugriffspakete anfordern **können**, aber **kein** Zugriffspaket anfordern, sind keine Lizenzen erforderlich.

Weitere Informationen zu Lizenzen finden Sie unter [Zuweisen oder Entfernen von Lizenzen im Azure Active Directory-Portal](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Beispielszenarien für Lizenzen

Anhand der folgenden Beispielszenarien für Lizenzen können Sie die Anzahl der benötigten Lizenzen bestimmen.

| Szenario | Berechnung | Anzahl der Lizenzen |
| --- | --- | --- |
| Ein globaler Administrator bei der Woodgrove Bank erstellt Anfangskataloge und delegiert administrative Aufgaben an sechs andere Benutzer. Eine der Richtlinien gibt an, dass **alle Mitarbeiter** (2.000 Mitarbeiter) einen bestimmten Satz von Zugriffspaketen anfordern können. 150 Mitarbeiter fordern die Zugriffspakete an. | 2\.000 Mitarbeiter, die Zugriffspakete anfordern **können** | 2\.000 |
| Ein globaler Administrator bei der Woodgrove Bank erstellt Anfangskataloge und delegiert administrative Aufgaben an sechs andere Benutzer. Eine der Richtlinien gibt an, dass **alle Mitarbeiter** (2.000 Mitarbeiter) einen bestimmten Satz von Zugriffspaketen anfordern können. Eine andere Richtlinie gibt an, dass einige **Benutzer des Partners Contoso** (Gäste) vorbehaltlich der Genehmigung dieselben Zugriffspakete anfordern können. Contoso hat 30.000 Benutzer. 150 Mitarbeiter fordern die Zugriffspakete an, und 10.500 Benutzer von Contoso fordern den Zugriff an. | 2\.000 Mitarbeiter + 500 Gastbenutzer von Contoso, die das 1:5-Verhältnis überschreiten (10.500-(2.000*5)) | 2\.500 |

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Erstellen Ihres ersten Zugriffspakets](entitlement-management-access-package-first.md)
- [Gängige Szenarios](entitlement-management-scenarios.md)
