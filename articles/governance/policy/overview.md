---
title: Übersicht zu Azure-Richtlinien
description: Azure Policy ist ein Dienst in Azure, mit dem Sie Richtliniendefinitionen in Ihrer Azure-Umgebung erstellen, zuweisen und verwalten können.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 2dd31ab29479fade21d27b8e2c23952f905f530a
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979157"
---
# <a name="overview-of-the-azure-policy-service"></a>Übersicht über den Azure Policy-Dienst

Anhand von Governance wird überprüft, ob Ihre Organisation ihre Ziele basierend auf einer effektiven und effizienten IT-Nutzung erreichen kann. Hierzu werden die Geschäftsziele und die IT-Projekte eindeutig aufeinander abgestimmt.

Tritt in Ihrem Unternehmen eine erhebliche Anzahl von IT-Problemen auf, die anscheinend nie gelöst werden?
Eine gute IT-Governance zeichnet sich durch die Planung von Initiativen und das Setzen von Prioritäten auf strategischer Ebene aus, um Probleme managen und verhindern zu können. Bei dieser strategischen Anforderung kommt Azure Policy ins Spiel.

Azure Policy ist ein Dienst in Azure, mit dem Sie Richtlinien erstellen, zuweisen und verwalten können. Mit diesen Richtlinien werden unterschiedliche Regeln und Auswirkungen für Ihre Ressourcen erzwungen, damit diese stets mit Ihren Unternehmensstandards und Vereinbarungen zum Servicelevel konform bleiben. Azure Policy erfüllt diese Anforderung, indem Ihre Ressourcen auf die Nichteinhaltung der zugewiesenen Richtlinien überprüft werden. Beispielsweise können Sie eine Richtlinie erstellen, die besagt, dass in Ihrer Umgebung für virtuelle Computer nur eine bestimmte SKU-Größe zulässig ist. Nachdem diese Richtlinie implementiert wurde, wird die Konformität der neuen und vorhandenen Ressourcen ausgewertet. Mit dem richtigen Typ von Richtlinie kann für vorhandene Ressourcen Konformität erzielt werden. Weiter unten in dieser Dokumentation werden noch weitere Details zur Erstellung und Implementierung von Richtlinien mit Azure Policy beschrieben.

> [!IMPORTANT]
> Die Kompatibilitätsprüfung von Azure Policy wird nun unabhängig vom Tarif für alle Zuweisungen bereitgestellt. Sollten die Kompatibilitätsdaten für Ihre Zuweisungen nicht angezeigt werden, vergewissern Sie sich, dass das Abonnement beim Ressourcenanbieter „Microsoft.PolicyInsights“ registriert ist.

## <a name="how-is-it-different-from-rbac"></a>Worin unterscheidet sich dies von der rollenbasierten Zugriffssteuerung (RBAC)?

Zwischen Azure Policy und der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) gibt es einige entscheidende Unterschiede. Bei RBAC stehen Benutzeraktionen in verschiedenen Bereichen im Mittelpunkt. Sie können der Rolle „Mitwirkender“ für eine Ressourcengruppe hinzugefügt werden, damit Sie für die Ressourcengruppe Änderungen vornehmen können. Bei Azure Policy stehen Ressourceneigenschaften während der Bereitstellung und für bereits bestehende Ressourcen im Mittelpunkt. Azure Policy steuert Eigenschaften wie die Typen oder Speicherorte von Ressourcen. Im Gegensatz zu RBAC stellt Azure Policy ein System zum standardmäßigen Zulassen und expliziten Ablehnen dar.

### <a name="rbac-permissions-in-azure-policy"></a>RBAC-Berechtigungen in Azure Policy

Azure Policy verfügt über verschiedene Berechtigungen (als Vorgänge bezeichnet) in zwei Ressourcenanbietern:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Zahlreiche integrierte Rollen erteilen Berechtigungen für Azure Policy-Ressourcen. Die Rolle **Mitwirkender an Ressourcenrichtlinien (Vorschau)** umfasst die meisten Vorgänge in Azure Policy. Die Rolle **Besitzer** verfügt über die vollständigen Berechtigungen. Sowohl für **Mitwirkender** als auch für **Leser** können alle Azure Policy-Lesevorgänge genutzt werden, aber mit **Mitwirkender** kann zusätzlich eine Bereinigung ausgelöst werden.

Wenn keine der integrierten Rollen über die erforderlichen Berechtigungen verfügt, erstellen Sie eine [benutzerdefinierte Rolle](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Richtliniendefinition

Die Erstellung und Implementierung einer Richtlinie in Azure Policy beginnt mit dem Erstellen einer Richtliniendefinition. Jede Richtliniendefinition verfügt über Bedingungen, unter denen sie erzwungen wird. Des Weiteren verfügt sie über einen definierten Effekt, der wirksam wird, wenn die Bedingungen erfüllt sind.

In Azure Policy bieten wir mehrere integrierte Richtlinien an, die standardmäßig zur Verfügung stehen. Beispiel: 

- **SQL Server-Version 12.0 erforderlich**: Überprüft, ob für alle Server mit SQL Server die Version 12.0 verwendet wird. Alle Server, die diese Kriterien nicht erfüllen, werden abgelehnt.
- **Zulässige Speicherkonten-SKUs**: Bestimmt, ob ein bereitzustellendes Speicherkonto innerhalb einer Gruppe von SKU-Größen liegt. Alle Speicherkonten, die nicht der definierten Gruppe von SKU-Größen entsprechen, werden abgelehnt.
- **Zulässiger Ressourcentyp**: Definiert die Ressourcentypen, die Sie bereitstellen können. Alle Ressourcen, die nicht in dieser Liste enthalten sind, werden abgelehnt.
- **Zulässige Standorte**: Schränkt die verfügbaren Standorte für neue Ressourcen ein. Der dazugehörige Effekt dient zur Erzwingung Ihrer Geokonformitätsanforderungen.
- **Zulässige SKUs für virtuelle Computer**: Gibt eine Gruppe von SKUs für virtuelle Computer an, die Sie bereitstellen können.
- **Tag und Standardwert anwenden**: Wendet ein erforderliches Tag und dessen Standardwert an, falls dies nicht mit der Bereitstellungsanforderung angegeben wird.
- **Tag und dessen Wert erzwingen**: Erzwingt ein erforderliches Tag und den zugehörigen Wert für eine Ressource.
- **Nicht zulässige Ressourcentypen**: Verhindert, dass die in der Liste enthaltenen Ressourcentypen bereitgestellt werden.

Um diese Richtliniendefinitionen implementieren zu können (integrierte und benutzerdefinierte Definitionen), müssen Sie sie zuweisen. Sie können diese Richtlinien über das Azure-Portal, PowerShell oder die Azure CLI zuweisen.

Bei der Richtlinienauswertung werden mehrere unterschiedliche Aktionen durchgeführt, z.B. Richtlinienzuweisung oder Richtlinienaktualisierungen. Eine vollständige Liste finden Sie unter [Evaluation Triggers](./how-to/get-compliance-data.md#evaluation-triggers) (Auswertungsauslöser).

Weitere Informationen zu den Strukturen von Richtliniendefinitionen finden Sie unter [Struktur von Richtliniendefinitionen](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Richtlinienzuweisung

Eine Richtlinienzuweisung ist eine zugewiesene Richtliniendefinition, die innerhalb eines bestimmten Bereichs angewendet werden soll. Ein solcher Bereich kann sich von einer [Verwaltungsgruppe](../management-groups/overview.md) bis hin zu einer Ressourcengruppe erstrecken. Der Begriff *Bereich* bezieht sich auf alle Ressourcengruppen, Abonnements oder Verwaltungsgruppen, denen die Richtliniendefinition zugewiesen ist. Richtlinienzuordnungen werden von allen untergeordneten Ressourcen geerbt. Dies bedeutet, dass eine auf eine Ressourcengruppe angewendete Richtlinie auch auf die Ressourcen in dieser Ressourcengruppe angewendet wird. Sie können jedoch einen Unterbereich von der Richtlinienzuordnung ausschließen.

Im Abonnementbereich können Sie beispielsweise eine Richtlinie zuweisen, die die Erstellung von Netzwerkressourcen verhindert. Sie können eine Ressourcengruppe in diesem Abonnement ausschließen, die für die Netzwerkinfrastruktur vorgesehen ist. Sie gewähren Benutzern, denen Sie in Bezug auf das Erstellen von Netzwerkressourcen vertrauen, dann Zugriff auf diese Netzwerkressourcengruppe.

Als weiteres Beispiel möchten Sie möglicherweise eine Richtlinie für eine Ressourcentyp-Zulassungsliste auf der Verwaltungsgruppenebene zuweisen. Außerdem möchten Sie einer untergeordneten Verwaltungsgruppe (oder sogar Abonnements direkt) eine weniger restriktive Richtlinie zuweisen, die mehr Ressourcentypen zulässt. Dieses Beispiel funktioniert jedoch nicht, da es sich bei einer Richtlinie um ein explizites Verbotssystem handelt. Stattdessen müssen Sie die untergeordnete Verwaltungsgruppe oder das Abonnement aus der Richtlinienzuweisung auf der Verwaltungsgruppenebene ausschließen. Anschließend müssen Sie die weniger restriktive Richtlinie auf der Ebene der untergeordneten Verwaltungsgruppe oder des Abonnements zuweisen. Falls eine Ressource aufgrund einer Richtlinie abgelehnt wird, muss zum Zulassen der Ressource die ablehnende Richtlinie geändert werden.

Weitere Informationen zum Einrichten von Richtliniendefinitionen und Zuweisungen über das Portal finden Sie unter [Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen in Ihrer Azure-Umgebung](assign-policy-portal.md). Schritte für [PowerShell](assign-policy-powershell.md) und die [Azure-Befehlszeilenschnittstelle](assign-policy-azurecli.md) sind ebenfalls verfügbar.

## <a name="policy-parameters"></a>Richtlinienparameter

Richtlinienparameter vereinfachen die Richtlinienverwaltung für Sie, da sie die Anzahl der Richtliniendefinitionen, die Sie erstellen müssen, reduzieren. Beim Erstellen einer Richtliniendefinition können Sie Parameter definieren, damit sie allgemeingültiger ist. Anschließend können Sie diese Richtliniendefinition für verschiedene Szenarien wiederverwenden. Dazu übergeben Sie unterschiedliche Werte beim Zuweisen der Richtliniendefinition. Geben Sie z.B. eine Gruppe von Speicherorten für ein Abonnement an.

Parameter werden beim Erstellen einer Richtliniendefinition definiert. Bei der Definition eines Parameters wird ein Name und optional ein Wert vergeben. Sie können z.B. einen Parameter für eine Richtlinie mit dem Titel *location* definieren. Dann können Sie ihm beim Zuweisen einer Richtlinie unterschiedliche Werte wie z.B. *EastUS* oder *WestUS* geben.

Weitere Informationen zu Richtlinienparametern finden Sie unter [Struktur von Azure Policy-Definitionen – Parameter](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Initiativdefinition

Eine Initiativdefinition ist eine Auflistung von Richtliniendefinitionen, die auf das Erreichen eines einzigen übergeordneten Ziels ausgerichtet sind. Initiativdefinitionen vereinfachen das Verwalten und Zuweisen von Richtliniendefinitionen. Die Vereinfachung besteht im Gruppieren einer Reihe von Richtlinien zu einem einzelnen Element. Beispielsweise können Sie eine Initiative mit dem Titel **Überwachung im Azure Security Center aktivieren** mit dem Ziel erstellen, alle vorhandenen Sicherheitsempfehlungen in Ihrem Azure Security Center zu überwachen.

Im Rahmen dieser Initiative würden Sie Richtliniendefinitionen wie etwa Folgende haben:

- **Unverschlüsselte SQL-Datenbank im Security Center überwachen** – Zum Überwachen von unverschlüsselten SQL-Datenbanken und Servern.
- **Betriebssystem-Sicherheitsrisiken in Security Center überwachen** – Zum Überwachen von Servern, die die konfigurierte Baseline nicht erfüllen.
- **Fehlendes Endpoint Protection im Security Center überwachen** – Zum Überwachen von Servern ohne installierten Endpoint Protection-Agent.

## <a name="initiative-assignment"></a>Initiativzuweisung

Bei einer Initiativzuweisung handelt es sich ähnlich wie bei einer Richtlinienzuweisung um eine Initiativdefinition, die einem bestimmten Bereich zugewiesen ist. Initiativzuweisungen reduzieren den Bedarf dafür, für jeden Bereich mehrere Initiativdefinitionen zu definieren. Ein solcher Bereich kann sich auch von einer Verwaltungsgruppe bis hin zu einer Ressourcengruppe erstrecken.

Jede Initiative kann unterschiedlichen Bereichen zugewiesen werden. Eine Initiative kann sowohl Abonnement A (**subscriptionA**) als auch Abonnement B (**subscriptionB**) zugewiesen werden.

## <a name="initiative-parameters"></a>Initiativparameter

Genau wie Richtlinienparameter vereinfachen auch Initiativparameter die Initiativverwaltung, indem sie die Redundanz verringern. Bei Initiativparametern handelt es sich um Parameter, die von den Richtliniendefinitionen innerhalb der Initiative verwendet werden.

Stellen Sie sich beispielsweise ein Szenario vor, in dem Sie die Initiativdefinition **initiativeC** mit den Richtliniendefinitionen **policyA** und **policyB** verwenden, die jeweils einen anderen Parametertyp erwarten:

| Richtlinie | Name des Parameters |Typ des Parameters  |Hinweis |
|---|---|---|---|
| policyA | allowedLocations | Array  |Dieser Parameter erwartet eine Liste von Zeichenfolgen als Wert, da der Parametertyp als Array definiert wurde. |
| policyB | allowedSingleLocation |Zeichenfolge |Dieser Parameter erwartet ein Wort als Wert, da der Parametertyp als Zeichenfolge definiert wurde. |

In diesem Szenario haben Sie bei der Definition der Initiativparameter für **initiativeC** drei Optionen:

- Verwenden Sie die Parameter der Richtliniendefinitionen innerhalb dieser Initiative: In diesem Beispiel werden *allowedLocations* und *allowedSingleLocation* zu Initiativparametern für **initiativeC**.
- Geben Sie Werte für die Parameter der Richtliniendefinitionen innerhalb dieser Initiativdefinition ein. In diesem Beispiel können Sie eine Liste von Standorten für den **Parameter von policyA – allowedLocations** und den **Parameter von policyB – allowedSingleLocation** zur Verfügung stellen. Bei der Zuweisung dieser Initiative können Sie auch Werte bereitstellen.
- Geben Sie eine Liste von *Wertoptionen* an, die bei der Zuweisung dieser Initiative verwendet werden können. Damit dürfen die von den Richtliniendefinitionen geerbten Parameter innerhalb der Initiative bei der Zuweisung dieser Initiative nur Werte aus der angegebenen Liste enthalten.

Beim Erstellen von Wertoptionen in einer Initiativdefinition können Sie während der Initiativenzuweisung keinen anderen Wert eingeben, da er nicht Teil der Liste ist.

## <a name="maximum-count-of-azure-policy-objects"></a>Maximale Anzahl von Azure Policy-Objekten

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Empfehlungen für die Verwaltung von Richtlinien

Hier sind einige Hinweise und Tipps aufgeführt, die Sie beachten sollten:

- Beginnen Sie mit einem Überwachungseffekt anstelle eines Ablehnungseffekts, um die Auswirkung Ihrer Richtliniendefinition auf die Ressourcen in Ihrer Umgebung nachzuverfolgen. Wenn Sie bereits Skripts für die automatische Skalierung Ihrer Anwendungen eingerichtet haben, kann ein Ablehnungseffekt die bereits eingerichteten Automatisierungsaufgaben verhindern.

- Berücksichtigen Sie beim Erstellen von Definitionen und Anweisungen die Hierarchien der Organisation. Wir empfehlen Ihnen, Definitionen auf allgemeiner Ebene zu erstellen, z.B. Verwaltungsgruppen- oder Abonnementebene. Erstellen Sie anschließend die Zuweisung auf der nächsten untergeordneten Ebene. Wenn Sie eine Definition für eine Verwaltungsgruppe erstellen, kann die Zuweisung für ein Abonnement oder eine Ressourcengruppe innerhalb dieser Verwaltungsgruppe festgelegt werden.

- Wir empfehlen Ihnen, auch für nur eine einzelne Richtliniendefinition Initiativdefinitionen zu erstellen und zuzuweisen.
Es kann beispielsweise sein, dass Sie über die Richtliniendefinition *policyDefA* verfügen und sie unter der Initiativdefinition *initiativeDefC* erstellen. Wenn Sie später eine andere Richtliniendefinition für *policyDefB* erstellen, deren Ziele denen von *policyDefA* ähneln, können Sie sie unter *initiativeDefC* hinzufügen und beide zusammen nachverfolgen.

- Nachdem Sie eine Initiativzuweisung erstellt haben, werden der Initiative hinzugefügte Richtliniendefinitionen ebenfalls Teil dieser Initiativzuweisungen.

- Wenn eine Initiativzuweisung ausgewertet wird, werden auch alle Richtlinien innerhalb der Initiative ausgewertet. Falls Sie eine Richtlinie einzeln auswerten möchten, empfiehlt es sich, sie nicht in eine Initiative aufzunehmen.

## <a name="video-overview"></a>Videoübersicht

Die folgende Übersicht über Azure Policy stammt von der Build 2018. Folien und den Videodownload finden Sie unter [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) (Steuern Ihrer Azure-Umgebung per Azure Policy) auf Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun einen Überblick über Azure Policy erhalten und einige Schlüsselbegriffe kennengelernt haben, geht es mit den nächsten empfohlenen Schritten weiter:

- [Zuweisen einer Richtliniendefinition mit dem Portal](assign-policy-portal.md)
- [Zuweisen einer Richtliniendefinition mit der Azure-Befehlszeilenschnittstelle](assign-policy-azurecli.md)
- [Zuweisen einer Richtliniendefinition mit PowerShell](assign-policy-powershell.md)
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](..//management-groups/overview.md).
- Sehen Sie sich [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) (Steuern Ihrer Azure-Umgebung über Azure Policy) auf Channel 9 an.