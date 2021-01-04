---
title: Grundlegendes zu Unternehmensglossarfunktionen in Azure Purview (Vorschau)
description: In diesem Artikel wird das Unternehmensglossar in Azure Purview erläutert.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 8b391438d8d6605e7ef493a6552af634db840ad5
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550991"
---
# <a name="understand-business-glossary-features-in-azure-purview"></a>Grundlegendes zu Unternehmensglossarfunktionen in Azure Purview

Dieser Artikel bietet eine Übersicht über die Unternehmensglossarfunktion in Azure Purview. 

## <a name="business-glossary"></a>Unternehmensglossar

Ein Glossar enthält ein Vokabular für Geschäftsbenutzer.  Es besteht aus geschäftlichen Begriffen, die miteinander verknüpft werden können, und ermöglicht deren Kategorisierung, sodass sie in verschiedenen Kontexten verstanden werden können. Diese Begriffe können dann Ressourcen wie Datenbanken, Tabellen, Spalten usw. zugeordnet werden. Dies hilft bei der Abstraktion des technischen Jargons, der den Datenrepositorys zugeordnet ist, und ermöglicht Geschäftsbenutzern, Daten in dem Vokabular, das ihnen vertrauter ist, zu ermitteln und mit ihnen zu arbeiten.


Ein Unternehmensglossar ist eine Sammlung von Begriffen. Jeder Begriff repräsentiert ein Objekt in einer Organisation, und es ist sehr wahrscheinlich, dass mehrere Begriffe das gleiche Objekt darstellen. Ein Kunde kann auch als „Client“ oder „Käufer“ bezeichnet werden. Diese verschiedenen Begriffe haben eine Beziehung zueinander. Dabei könnte es sich um eine der folgenden Beziehungen handeln:

- Synonyme: Verschiedene Begriffe mit identischer Definition
- Verwandt: Anderer Name mit ähnlicher Definition

Derselbe Begriff kann auch mehrere Geschäftsobjekte implizieren. Es ist wichtig, dass jeder Begriff eindeutig definiert und innerhalb der Organisation klar verständlich ist.

## <a name="custom-attributes"></a>Benutzerdefinierte Attribute

Azure Purview unterstützt acht Standardattribute für jeden Begriff im Unternehmensglossar:
- Name
- Definition
- Data Stewards
- Datenexperten
- Akronym
- Synonyme
- Verwandte Begriffe
- Ressourcen

Diese Attribute können nicht bearbeitet oder gelöscht werden. Allerdings reichen diese Attribute nicht aus, um einen Begriff in einer Organisation vollständig zu definieren. Um dieses Problem zu lösen, bietet Purview eine Funktion, mit der Sie benutzerdefinierte Attribute für Ihr Glossar definieren können.

## <a name="term-templates"></a>Begriffsvorlagen

Begriffsvorlagen bieten benutzerdefinierte Glossarattribute, die im Katalog logisch gruppiert werden. Mit der Funktion können Sie alle relevanten benutzerdefinierten Attribute in einer Vorlage gruppieren und die Vorlage dann beim Erstellen des Glossarbegriffs anwenden. Beispielsweise können alle finanzbezogenen benutzerdefinierten Attribute wie Kostenstelle, Profitcenter, Buchhaltungscode in einer Begriffsvorlage „Finanzvorlage“ gruppiert werden, die dann zum Erstellen von Finanzglossarbegriffen verwendet werden kann.

Alle Standardattribute werden in einer Systemstandardvorlage gruppiert. Jede von Ihnen erstellte Begriffsvorlage enthält diese Attribute sowie alle zusätzlichen benutzerdefinierten Attribute, die im Rahmen der Vorlagenerstellung erstellt werden.

## <a name="glossary-vs-classification-vs-sensitivity-labels"></a>Glossar, Klassifizierung und Vertraulichkeitsbezeichnungen

Glossarbegriffe, Klassifizierungen und Bezeichnungen sind zwar alles Anmerkungen zu einer Datenressource, haben jedoch im Kontext des Katalogs jeweils eine andere Bedeutung. 

### <a name="glossary"></a>Glossar

Wie bereits erwähnt, definieren die Begriffe im Unternehmensglossar das Geschäftsvokabular für eine Organisation und schlagen eine Brücke zwischen verschiedenen Abteilungen Ihres Unternehmens.

### <a name="classifications"></a>Klassifizierungen

Klassifizierungen sind Anmerkungen, die Entitäten zugewiesen werden können. Die Flexibilität von Klassifizierungen ermöglicht Ihnen die Verwendung in mehreren Szenarien, wie z. B.:

- Grundlegendes zur Art der in den Datenressourcen gespeicherten Daten
- Definieren von Zugriffssteuerungsrichtlinien

Purview hat heute mehr als 100 Systemklassifizierer, und Sie können Ihre eigenen Klassifizierer im Katalog definieren. Im Rahmen des Scanvorgangs erkennen wir diese Klassifizierungen automatisch und wenden sie auf Datenressourcen und Schemas an. Sie können sie jedoch jederzeit überschreiben. Die menschlichen Überschreibungen werden nie durch automatisierte Scans ersetzt.

### <a name="sensitivity-labels"></a>Vertraulichkeitsbezeichnungen

Vertraulichkeitsbezeichnungen sind Anmerkungen, mit denen Sie die Daten Ihrer Organisation klassifizieren und schützen können, ohne Produktivität und Zusammenarbeit zu behindern. Vertraulichkeitsbezeichnungen werden verwendet, um die Klassifizierungstypkategorien in Ihren Organisationsdaten zu ermitteln und die Richtlinien zu gruppieren, die Sie auf die einzelnen Kategorien anwenden möchten. Purview verwendet die gleichen Typen von vertraulichen Informationen wie Microsoft 365. Hiermit können Sie Ihre vorhandenen Sicherheitsrichtlinien und den Schutz auf Ihre gesamten Inhalte und Ihre gesamte Datenumgebung ausdehnen. Die gleichen Bezeichnungen können über Microsoft Office-Produkte und Datenressourcen in Purview gemeinsam genutzt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Vorgehensweise: Verwalten von Begriffsvorlagen für Unternehmensglossare](how-to-manage-term-templates.md)
- [Durchsuchen des Azure Purview-Datenkatalogs](how-to-browse-catalog.md)
