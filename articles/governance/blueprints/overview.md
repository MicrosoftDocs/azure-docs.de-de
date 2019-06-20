---
title: 'Azure Blueprint: Übersicht'
description: Hier wird erläutert, wie Sie den Azure Blueprints-Dienst zum Erstellen, Definieren und Bereitstellen von Artefakten in Ihrer Azure-Umgebung verwenden.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/08/2019
ms.topic: overview
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 960b8145e5f53c6c37820604fd634ccf5fd77c6b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "60876238"
---
# <a name="overview-of-the-azure-blueprints-service"></a>Übersicht über den Azure Blueprints-Dienst

Genau wie eine Blaupause, die einem Ingenieur oder Architekten die Skizzierung der Entwurfsparameter für ein Projekt ermöglicht, ermöglicht es Azure Blueprints Cloudarchitekten und zentralen IT-Gruppen, eine wiederholbare Gruppe von Azure-Ressourcen zu definieren, mit der die Standards, Muster und Anforderungen einer Organisation implementiert und erzwungen werden. Mit Azure Blueprints können Entwicklungsteams schnell neue Umgebungen bereitstellen und einrichten und dabei darauf vertrauen, dass sie die Konformitätsanforderungen der Organisation erfüllen und über eine Reihe integrierter Komponenten (z.B. Netzwerk) zur Beschleunigung der Entwicklung und Bereitstellung verfügen.

Blaupausen sind eine deklarative Möglichkeit zum Orchestrieren der Bereitstellung mehrerer Ressourcenvorlagen und anderer Artefakte wie etwa:

- Rollenzuweisungen
- Richtlinienzuweisungen
- Azure-Ressourcen-Manager-Vorlagen
- Ressourcengruppen

Der Azure-Dienst für Blaupausen wird vom global verteilten [Azure Cosmos DB](../../cosmos-db/introduction.md)-Dienst unterstützt.
Blaupausenobjekte werden in mehreren Azure-Regionen repliziert. Diese Replikation bietet niedrige Wartezeiten, Hochverfügbarkeit und konsistenten Zugriff auf Ihre Blaupausenobjekte – unabhängig davon, in welcher Region Ihre Ressourcen bereitgestellt werden.

## <a name="how-its-different-from-resource-manager-templates"></a>Unterschied zu Resource Manager-Vorlagen

Der Dienst soll die _Umgebungseinrichtung_ vereinfachen. Diese Einrichtung umfasst häufig eine Reihe von Ressourcengruppen, Richtlinien, Rollenzuweisungen und Resource Manager-Vorlagenbereitstellungen. Eine Blaupause ist ein Paket, in dem die einzelnen _Artefakttypen_ zusammengeführt werden. Mit diesen können Sie das Paket zusammenstellen und versionieren – auch über eine CI/CD-Pipeline. Letztlich wird jede in einem einzelnen Vorgang, der überwacht und nachverfolgt werden kann, einem Abonnement zugewiesen.

Nahezu alle Elemente, die Sie für die Bereitstellung in Blaupausen einfügen möchten, können über eine Resource Manager-Vorlage eingefügt werden. Eine Resource Manager-Vorlage ist jedoch ein Dokument, das in Azure nicht nativ vorhanden ist, sondern entweder lokal oder in der Quellcodeverwaltung gespeichert wird. Die Vorlage wird für die Bereitstellung einer oder mehrerer Azure-Ressourcen verwendet. Nach der Bereitstellung dieser Ressourcen besteht jedoch keine aktive Verbindung oder Beziehung mehr mit der Vorlage.

Mit Blaupausen bleibt die Beziehung zwischen der Blaupausendefinition (was _soll_ bereitgestellt werden) und der Blaupausenzuweisung (was _wurde_  bereitgestellt) erhalten. Diese Verbindung ermöglicht eine erweiterte Nachverfolgung und Überprüfung von Bereitstellungen. Mit Blaupausen lassen sich auch mehrere Abonnements, die der gleichen Blaupause unterliegen, gleichzeitig upgraden.

Es besteht nicht die Notwendigkeit, zwischen einer Resource Manager-Vorlage und einer Blaupause zu wählen. Jede Blaupause kann aus keinem oder mehreren _Artefakten_ für Resource Manager-Vorlagen bestehen. Das bedeutet, dass frühere Bemühungen zur Entwicklung und Verwaltung einer Bibliothek von Resource Manager-Vorlagen in Blaupausen wiederverwendet werden können.

## <a name="how-its-different-from-azure-policy"></a>Unterschied zu Azure Policy

Eine Blaupause ist ein Paket oder Container zum Zusammenstellen von spezifischen Gruppen von Standards, Mustern und Anforderungen in Bezug auf die Implementierung von Azure-Clouddiensten, Sicherheit und Entwurf. Sie kann wiederverwendet werden, um Konsistenz und Konformität zu gewährleisten.

Eine [Richtlinie](../policy/overview.md) ist ein System zur standardmäßigen Zulassung und expliziten Ablehnung, das sich auf Ressourceneigenschaften während der Bereitstellung und für bereits vorhandene Ressourcen konzentriert. Es unterstützt Cloud-Governance, indem sichergestellt wird, dass Ressourcen in einem Abonnement den Anforderungen und Standards entsprechen.

Das Einschließen einer Richtlinie in eine Blaupause ermöglicht die Erstellung des richtigen Musters oder Entwurfs während der Blaupausenzuweisung. Die eingeschlossene Richtlinie sorgt dafür, dass an der Umgebung nur genehmigte oder erwartete Änderungen vorgenommen werden können und die Konformität mit der Absicht der Blaupause kontinuierlich gewahrt bleibt.

Eine Richtlinie kann als eines von vielen _Artefakten_ in eine Blaupausendefinition eingefügt werden. Blaupausen unterstützen zudem die Verwendung von Parametern mit Richtlinien und Initiativen.

## <a name="blueprint-definition"></a>Blaupausendefinition

Eine Blaupause besteht aus _Artefakten_. Azure Blueprint unterstützt derzeit die folgenden Ressourcen als Artefakte:

|Resource  | Hierarchieoptionen| BESCHREIBUNG  |
|---------|---------|---------|
|Ressourcengruppen | Abonnement | Erstellen einer neuen Ressourcengruppe zur Verwendung durch andere Artefakte innerhalb der Blaupause.  Diese Platzhalter-Ressourcengruppen ermöglichen es, Ressourcen genau auf die gewünschte Weise zu strukturieren. Sie umfassen eine Bereichsbeschränkung für enthaltene Richtlinien- und Rollenzuweisungsartefakte und Azure Resource Manager-Vorlagen. |
|Azure Resource Manager-Vorlage | Abonnement, Ressourcengruppe | Vorlagen werden verwendet, um komplexe Umgebungen zusammenzustellen. Beispielumgebungen: SharePoint-Farm, Azure Automation State Configuration oder Log Analytics-Arbeitsbereich. |
|Richtlinienzuweisung | Abonnement, Ressourcengruppe | Ermöglicht die Zuweisung einer Richtlinie oder Initiative zum Abonnement, dem die Blaupause zugewiesen ist. Die Richtlinie oder Initiative muss innerhalb des Bereichs des Definitionsspeicherorts der Blaupause liegen. Wenn die Richtlinie oder Initiative über Parameter verfügt, werden diese bei der Erstellung der Blaupause oder bei der Blaupausenzuweisung zugewiesen. |
|Rollenzuweisung | Abonnement, Ressourcengruppe | Fügt einer integrierten Rolle einen vorhandenen Benutzer oder eine vorhandene Gruppe zu, um sicherzustellen, dass die richtigen Personen geeigneten Zugriff auf Ihre Ressourcen haben. Rollenzuweisungen können für das gesamte Abonnement definiert oder in einer bestimmten in der Blaupause enthaltenen Ressourcengruppe geschachtelt werden. |

### <a name="blueprint-definition-locations"></a>Definitionsspeicherorte von Blaupausen

Bei der Erstellung einer Blaupausendefinition legen Sie fest, wo die Blaupause gespeichert wird. Blaupausen können in einer [Verwaltungsgruppe](../management-groups/overview.md) oder in einem Abonnement gespeichert werden, für die bzw. das Sie über den Zugriff **Mitwirkender** verfügen. Wenn der Speicherort eine Verwaltungsgruppe ist, kann Blaupause jedem untergeordneten Abonnement dieser Verwaltungsgruppe zugewiesen werden.

### <a name="blueprint-parameters"></a>Blaupausenparameter

Azure Blueprint kann Parameter entweder an eine Richtlinie oder Initiative oder an eine Azure Resource Manager-Vorlage übergeben.
Beim Hinzufügen eines _Artefakts_ zu einer Blaupause entscheidet der Ersteller, ob er einen definierten Wert für jede Blaupausenzuweisung angeben möchte oder ob bei jeder Blaupausenzuweisung ein Wert angegeben werden kann. Diese Flexibilität bietet die Möglichkeit, einen vorab festgelegten Wert für alle Verwendungen der Blaupause zu definieren. Es ist auch möglich, diese Festlegung zum Zeitpunkt der Zuweisung vorzunehmen.

> [!NOTE]
> Eine Blaupause kann über eigene Parameter verfügen, diese können derzeit jedoch nur erstellt werden, wenn die Blaupause mit der REST-API und nicht im Portal generiert wird.

Weitere Informationen finden Sie unter [Blaupausenparameter](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Blaupausenveröffentlichung

Wenn eine Blaupause erstellt wird, befindet sie sich im **Entwurfsmodus**. Wenn sie bereit ist für die Zuweisung, muss sie **veröffentlicht** werden. Für die Veröffentlichung muss eine **Versionszeichenfolge** (Buchstaben, Zahlen und Bindestriche mit einer maximalen Länge von 20 Zeichen) zusammen mit optionalen **Änderungshinweisen** definiert werden. Durch die **Version** unterscheidet sich eine Blaupause von zukünftigen Änderungen an der Blaupause, sodass jede Version zugewiesen werden kann. Diese Versionierung bedeutet auch, dass einem Abonnement verschiedene **Versionen** der gleichen Blaupause zugewiesen werden können. Wenn zusätzliche Änderungen an der Blaupause vorgenommen werden, existiert die **veröffentlichte** **Version** parallel zu den **unveröffentlichten Änderungen**. Nachdem die Änderungen abgeschlossen wurden, wird die aktualisierte Blaupause mit einer neuen und eindeutigen **Version** **veröffentlicht** und kann dann auch zugewiesen werden.

## <a name="blueprint-assignment"></a>Blaupausenzuweisung

Jede **veröffentlichte** **Version** einer Blaupause kann einem vorhandenen Abonnement zugewiesen werden. Im Portal wird standardmäßig die **Version** der Blaupause verwendet, die zuletzt **veröffentlicht** wurde. Wenn Artefaktparameter (oder Blaupausenparameter) vorhanden sind, werden die Parameter während des Zuweisungsvorgangs definiert.

## <a name="permissions-in-azure-blueprints"></a>Berechtigungen in Azure Blueprint

Zur Verwendung von Blaupausen müssen Ihnen über die [rollenbasierte Zugriffssteuerung](../../role-based-access-control/overview.md) (Role-Based Access Control, RBAC) Berechtigungen erteilt werden. Zur Erstellung von Blaupausen sind für Ihr Konto die folgenden Berechtigungen erforderlich:

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

Alle der oben genannten Berechtigungen sind in der Rolle **Besitzer** enthalten. Die Rolle **Mitwirkender** verfügt über Berechtigungen zum Erstellen und Löschen von Blaupausen, ist aber nicht zum Zuweisen von Blaupausen berechtigt. Sollten diese integrierten Rollen nicht Ihren Sicherheitsanforderungen entsprechen, können Sie eine [benutzerdefinierte Rolle](../../role-based-access-control/custom-roles.md) erstellen.

> [!NOTE]
> Für den Dienstprinzipal für Azure Blueprint ist die Rolle **Besitzer** für das zugewiesene Abonnement erforderlich, um die Bereitstellung zu ermöglichen. Bei Verwendung des Portals wird diese Rolle für die Bereitstellung automatisch erteilt und widerrufen. Bei Verwendung der REST-API muss diese Rolle manuell erteilt werden, sie wird jedoch nach Abschluss der Bereitstellung auch automatisch widerrufen.

## <a name="video-overview"></a>Videoübersicht

Die folgende Übersicht über Azure Blueprints stammt aus Azure Friday. Besuchen Sie zum Herunterladen des Videos [Azure Fridays – An overview of Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) (Azure Friday: Übersicht über Azure Blueprints) auf Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer Blaupause – Portal](create-blueprint-portal.md)
- [Erstellen einer Blaupause – REST-API](create-blueprint-rest-api.md)