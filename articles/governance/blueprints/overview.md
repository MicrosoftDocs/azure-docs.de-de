---
title: 'Azure Blueprint: Übersicht'
description: Hier wird erläutert, wie Sie den Azure Blueprints-Dienst zum Erstellen, Definieren und Bereitstellen von Artefakten in Ihrer Azure-Umgebung verwenden.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: f4ba77f5fcb376bf600d94997b0d6ba569f04f82
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919341"
---
# <a name="what-is-azure-blueprints"></a>Was ist Azure Blueprint?

Genau wie eine Blaupause, die einem Ingenieur oder Architekten die Skizzierung der Entwurfsparameter für ein Projekt ermöglicht, ermöglicht es Azure Blueprints Cloudarchitekten und zentralen IT-Gruppen, eine wiederholbare Gruppe von Azure-Ressourcen zu definieren, mit der die Standards, Muster und Anforderungen einer Organisation implementiert und erzwungen werden. Mit Azure Blueprints können Entwicklungsteams schnell neue Umgebungen bereitstellen und einrichten und dabei darauf vertrauen, dass sie die Konformitätsanforderungen der Organisation erfüllen und über eine Reihe integrierter Komponenten (z. B. Netzwerk) zur Beschleunigung der Entwicklung und Bereitstellung verfügen.

Blaupausen sind eine deklarative Möglichkeit zum Orchestrieren der Bereitstellung mehrerer Ressourcenvorlagen und anderer Artefakte wie etwa:

- Rollenzuweisungen
- Richtlinienzuweisungen
- Azure Resource Manager-Vorlagen (ARM-Vorlagen)
- Ressourcengruppen

Der Azure-Dienst für Blaupausen wird vom global verteilten [Azure Cosmos DB](../../cosmos-db/introduction.md)-Dienst unterstützt. Blaupausenobjekte werden in mehreren Azure-Regionen repliziert. Diese Replikation bietet niedrige Wartezeiten, Hochverfügbarkeit und konsistenten Zugriff auf Ihre Blaupausenobjekte – unabhängig davon, in welcher Region Ihre Ressourcen von Azure Blueprints bereitgestellt werden.

## <a name="how-its-different-from-arm-templates"></a>Unterschiede zu ARM-Vorlagen

Der Dienst soll die _Umgebungseinrichtung_ vereinfachen. Diese Einrichtung umfasst häufig eine Reihe von Ressourcengruppen, Richtlinien, Rollenzuweisungen und Bereitstellungen von ARM-Vorlagen. Eine Blaupause ist ein Paket, in dem die einzelnen _Artefakttypen_ zusammengeführt werden. Sie können das Paket zusammenstellen und versionieren, z. B. auch über eine CI/CD-Pipeline (Continuous Integration/Continuous Delivery). Letztlich wird jede in einem einzelnen Vorgang, der überwacht und nachverfolgt werden kann, einem Abonnement zugewiesen.

Nahezu alle Elemente, die Sie für die Bereitstellung in Azure Blueprints einfügen möchten, können über eine ARM-Vorlage eingefügt werden. Eine ARM-Vorlage ist aber ein Dokument, das in Azure nicht nativ vorhanden ist, sondern entweder lokal oder in der Quellcodeverwaltung gespeichert wird. Die Vorlage wird für die Bereitstellung einer oder mehrerer Azure-Ressourcen verwendet. Nach der Bereitstellung dieser Ressourcen besteht jedoch keine aktive Verbindung oder Beziehung mehr mit der Vorlage.

Mit Azure Blueprints bleibt die Beziehung zwischen der Blaupausendefinition (was _soll_ bereitgestellt werden) und der Blaupausenzuweisung (was _wurde_ bereitgestellt) erhalten. Diese Verbindung ermöglicht eine erweiterte Nachverfolgung und Überprüfung von Bereitstellungen. Mit Azure Blueprints lassen sich auch mehrere Abonnements, die der gleichen Blaupause unterliegen, gleichzeitig upgraden.

Es besteht nicht die Notwendigkeit, zwischen einer ARM-Vorlage und einer Blaupause zu wählen. Jede Blaupause kann null oder mehr _Artefakte_ für ARM-Vorlagen umfassen. Dies bedeutet, dass bereits durchgeführte Leistungen zur Entwicklung und Verwaltung einer Bibliothek mit ARM-Vorlagen in Azure Blueprints genutzt werden können.

## <a name="how-its-different-from-azure-policy"></a>Unterschied zu Azure Policy

Eine Blaupause ist ein Paket oder Container zum Zusammenstellen von spezifischen Gruppen von Standards, Mustern und Anforderungen in Bezug auf die Implementierung von Azure-Clouddiensten, Sicherheit und Entwurf. Sie kann wiederverwendet werden, um Konsistenz und Konformität zu gewährleisten.

Eine [Richtlinie](../policy/overview.md) ist ein System zur standardmäßigen Zulassung und expliziten Ablehnung, das sich auf Ressourceneigenschaften während der Bereitstellung und für bereits vorhandene Ressourcen konzentriert. Es unterstützt Cloud-Governance, indem sichergestellt wird, dass Ressourcen in einem Abonnement den Anforderungen und Standards entsprechen.

Das Einschließen einer Richtlinie in einer Blaupause ermöglicht die Erstellung des richtigen Musters oder Entwurfs bei der Zuweisung der Blaupause. Die eingeschlossene Richtlinie sorgt dafür, dass an der Umgebung nur genehmigte oder erwartete Änderungen vorgenommen werden können und die Konformität mit der Absicht der Blaupause kontinuierlich gewahrt bleibt.

Eine Richtlinie kann als eines von vielen _Artefakten_ in eine Blaupausendefinition eingefügt werden. Blaupausen unterstützen zudem die Verwendung von Parametern mit Richtlinien und Initiativen.

## <a name="blueprint-definition"></a>Blaupausendefinition

Eine Blaupause besteht aus _Artefakten_. Azure Blueprints unterstützt derzeit die folgenden Ressourcen als Artefakte:

|Resource  | Hierarchieoptionen| BESCHREIBUNG  |
|---------|---------|---------|
|Ressourcengruppen | Subscription | Erstellen einer neuen Ressourcengruppe zur Verwendung durch andere Artefakte innerhalb der Blaupause.  Diese Platzhalter-Ressourcengruppen ermöglichen es, Ressourcen genau auf die gewünschte Weise zu strukturieren. Sie umfassen eine Bereichsbeschränkung für enthaltene Richtlinien- und Rollenzuweisungsartefakte und ARM-Vorlagen. |
|ARM-Vorlage | Abonnement, Ressourcengruppe | Vorlagen, einschließlich geschachtelter und verknüpfter Vorlagen, werden zum Erstellen komplexer Umgebungen verwendet. Beispielumgebungen: SharePoint-Farm, Azure Automation State Configuration oder Log Analytics-Arbeitsbereich. |
|Richtlinienzuweisung | Abonnement, Ressourcengruppe | Ermöglicht die Zuweisung einer Richtlinie oder Initiative zum Abonnement, dem die Blaupause zugewiesen ist. Die Richtlinie oder Initiative muss innerhalb des Bereichs des Definitionsspeicherorts der Blaupause liegen. Wenn die Richtlinie oder Initiative über Parameter verfügt, werden diese bei der Erstellung der Blaupause oder bei der Blaupausenzuweisung zugewiesen. |
|Rollenzuweisung | Abonnement, Ressourcengruppe | Fügt einer integrierten Rolle einen vorhandenen Benutzer oder eine vorhandene Gruppe zu, um sicherzustellen, dass die richtigen Personen geeigneten Zugriff auf Ihre Ressourcen haben. Rollenzuweisungen können für das gesamte Abonnement definiert oder in einer bestimmten in der Blaupause enthaltenen Ressourcengruppe geschachtelt werden. |

### <a name="blueprint-definition-locations"></a>Definitionsspeicherorte von Blaupausen

Bei der Erstellung einer Blaupausendefinition legen Sie fest, wo die Blaupause gespeichert wird. Blaupausen können in einer [Verwaltungsgruppe](../management-groups/overview.md) oder in einem Abonnement gespeichert werden, für die bzw. das Sie über den Zugriff **Mitwirkender** verfügen. Wenn der Speicherort eine Verwaltungsgruppe ist, kann Blaupause jedem untergeordneten Abonnement dieser Verwaltungsgruppe zugewiesen werden.

### <a name="blueprint-parameters"></a>Blaupausenparameter

Bei Blaupausen können Parameter entweder an eine Richtlinie oder Initiative oder an eine ARM-Vorlage übergeben werden. Beim Hinzufügen eines _Artefakts_ zu einer Blaupause entscheidet der Ersteller, ob er einen definierten Wert für jede Blaupausenzuweisung angeben möchte oder ob bei jeder Blaupausenzuweisung ein Wert angegeben werden kann. Diese Flexibilität bietet die Möglichkeit, einen vorab festgelegten Wert für alle Verwendungen der Blaupause zu definieren. Es ist auch möglich, diese Festlegung zum Zeitpunkt der Zuweisung vorzunehmen.

> [!NOTE]
> Eine Blaupause kann über eigene Parameter verfügen, diese können derzeit jedoch nur erstellt werden, wenn die Blaupause mit der REST-API und nicht im Portal generiert wird.

Weitere Informationen finden Sie unter [Blaupausenparameter](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Blaupausenveröffentlichung

Wenn eine Blaupause erstellt wird, befindet sie sich im **Entwurfsmodus**. Wenn sie bereit ist für die Zuweisung, muss sie **veröffentlicht** werden. Für die Veröffentlichung muss eine **Versionszeichenfolge** (Buchstaben, Zahlen und Bindestriche mit einer maximalen Länge von 20 Zeichen) zusammen mit optionalen **Änderungshinweisen** definiert werden. Durch die **Version** unterscheidet sich eine Blaupause von zukünftigen Änderungen an der Blaupause, sodass jede Version zugewiesen werden kann. Diese Versionierung bedeutet auch, dass einem Abonnement verschiedene **Versionen** der gleichen Blaupause zugewiesen werden können. Wenn zusätzliche Änderungen an der Blaupause vorgenommen werden, ist die **veröffentlichte**
**Version** parallel zu den **unveröffentlichten Änderungen** vorhanden. Nachdem die Änderungen abgeschlossen wurden, wird die aktualisierte Blaupause mit einer neuen und eindeutigen **Version** **veröffentlicht** und kann dann auch zugewiesen werden.

## <a name="blueprint-assignment"></a>Blaupausenzuweisung

Jede **veröffentlichte** **Version** einer Blaupause kann (mit einem maximal 90 Zeichen langen Namen) einer vorhandenen Verwaltungsgruppe oder einem vorhandenen Abonnement zugewiesen werden. Im Portal wird standardmäßig die **Version** der Blaupause verwendet, die zuletzt **veröffentlicht** wurde. Wenn Artefaktparameter oder Blaupausenparameter vorhanden sind, werden die Parameter während des Zuweisungsvorgangs definiert.

> [!NOTE]
> Die Zuweisung einer Blaupausendefinition zu einer Verwaltungsgruppe bedeutet, dass das Zuweisungsobjekt bei der Verwaltungsgruppe vorhanden ist. Die Bereitstellung von Artefakten ist nach wie vor auf ein Abonnement ausgerichtet. Um eine Verwaltungsgruppenzuweisung durchzuführen, muss die REST-API [Erstellen oder Aktualisieren](/rest/api/blueprints/assignments/createorupdate) verwendet werden und der Anforderungstext muss einen Wert für `properties.scope` enthalten, um das Zielabonnement zu definieren.

## <a name="permissions-in-azure-blueprints"></a>Berechtigungen in Azure Blueprint

Zur Verwendung von Blaupausen müssen Ihnen über die [rollenbasierte Zugriffssteuerung von Azure (Role-Based Access Control, RBAC)](../../role-based-access-control/overview.md) Berechtigungen erteilt werden. Zum Lesen oder Anzeigen einer Blaupause im Azure-Portal muss Ihr Konto über Lesezugriff für den Bereich verfügen, in dem sich die Blaupausendefinition befindet.

Zur Erstellung von Blaupausen sind für Ihr Konto die folgenden Berechtigungen erforderlich:

- `Microsoft.Blueprint/blueprints/write`: Erstellen einer Blaupausendefinition
- `Microsoft.Blueprint/blueprints/artifacts/write`: Erstellen von Artefakten in einer Blaupausendefinition
- `Microsoft.Blueprint/blueprints/versions/write`: Veröffentlichen einer Blaupause

Zum Löschen von Blaupausen sind für Ihr Konto die folgenden Berechtigungen erforderlich:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Die Berechtigungen für die Blaupausendefinition müssen im Bereich der Verwaltungsgruppe oder des Abonnements, in der bzw. dem sie gespeichert ist, erteilt oder geerbt werden.

Zum Zuweisen oder zum Aufheben der Zuweisung einer Blaupause sind für Ihr Konto die folgenden Berechtigungen erforderlich:

- `Microsoft.Blueprint/blueprintAssignments/write`: Zuweisen einer Blaupause
- `Microsoft.Blueprint/blueprintAssignments/delete`: Aufheben der Zuweisung einer Blaupause

> [!NOTE]
> Da Blaupausenzuweisungen in einem Abonnement erstellt werden, müssen die Berechtigungen zum Zuweisen und Aufheben der Zuweisung von Blaupausen in einem Abonnementbereich erteilt oder vererbt werden.

Die folgenden integrierten Rollen sind verfügbar:

|Azure-Rolle | BESCHREIBUNG |
|-|-|
|[Besitzer](../../role-based-access-control/built-in-roles.md#owner) | Umfasst (neben anderen Berechtigungen) alle Berechtigungen im Zusammenhang mit der Azure-Blaupause. |
|[Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) | Umfasst (neben anderen Berechtigungen) Berechtigungen zum Erstellen und Löschen von Blaupausendefinitionen, aber keine Berechtigungen zum Zuweisen von Blaupausen. |
|[Blueprint-Mitwirkender](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Kann Blaupausendefinitionen verwalten, aber nicht zuweisen. |
|[Blueprint-Operator](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Kann vorhandene veröffentlichte Blaupausen zuweisen, aber keine neuen Blaupausendefinitionen erstellen. Die Blaupausenzuweisung funktioniert nur, wenn die Zuweisung mit einer vom Benutzer zugewiesenen verwalteten Identität erfolgt. |

Sollten diese integrierten Rollen nicht Ihren Sicherheitsanforderungen entsprechen, können Sie eine [benutzerdefinierte Rolle](../../role-based-access-control/custom-roles.md) erstellen.

> [!NOTE]
> Bei Verwenden einer vom System zugewiesenen Identität ist für den Dienstprinzipal für Azure Blueprints die Rolle **Besitzer** für das zugewiesene Abonnement erforderlich, um die Bereitstellung zu ermöglichen. Bei Verwendung des Portals wird diese Rolle für die Bereitstellung automatisch erteilt und widerrufen. Bei Verwendung der REST-API muss diese Rolle manuell erteilt werden, sie wird jedoch nach Abschluss der Bereitstellung auch automatisch widerrufen. Wenn eine vom Benutzer zugewiesene verwaltete Identität verwendet wird, benötigt nur der Benutzer, der die Blaupausenzuweisung erstellt, die `Microsoft.Blueprint/blueprintAssignments/write`-Berechtigung, die in den integrierten Rollen **Besitzer** und **Blueprint-Operator** enthalten ist.

## <a name="naming-limits"></a>Beschränkungen für Benennungen

Für bestimmte Felder gelten die folgenden Einschränkungen:

|Object|Feld|Zulässige Zeichen|Maximal Länge|
|-|-|-|-|
|Blaupause|Name|Buchstaben, Ziffern, Bindestriche und Punkte|48|
|Blaupause|Version|Buchstaben, Ziffern, Bindestriche und Punkte|20|
|Blaupausenzuweisung|Name|Buchstaben, Ziffern, Bindestriche und Punkte|90|
|Blaupausenartefakt|Name|Buchstaben, Ziffern, Bindestriche und Punkte|48|

## <a name="video-overview"></a>Videoübersicht

Die folgende Übersicht über Azure Blueprints stammt aus Azure Friday. Besuchen Sie zum Herunterladen des Videos [Azure Fridays – An overview of Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) (Azure Friday: Übersicht über Azure Blueprints) auf Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer Blaupause – Portal](./create-blueprint-portal.md)
- [Erstellen einer Blaupause – PowerShell](./create-blueprint-powershell.md)
- [Erstellen einer Blaupause – REST-API](./create-blueprint-rest-api.md)