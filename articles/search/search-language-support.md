---
title: Mehrsprachige Indizierung für nicht-englische Suchabfragen – Azure Search
description: Azure Search unterstützt 56 Sprachen und nutzt Sprachanalysen mit Lucene- und Natural Language Processing-Technologie von Microsoft.
author: yahnoosh
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: cc9f271c1c79f34ba62fa22d6ce4fd6bf16738f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65025272"
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Erstellen eines Index für Dokumente in mehreren Sprachen in Azure Search
> [!div class="op_single_selector"]
>
> * [Portal](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

Sie können die Vorteile von Sprachanalysen einfach nutzen, indem Sie eine Eigenschaft für ein durchsuchbares Feld in der Indexdefinition festlegen. Diesen Schritt können Sie jetzt im Portal ausführen.

Nachfolgend finden Sie Screenshots der Azure-Portalblätter für Azure Search, über die Benutzer ein Indexschema definieren können. Auf diesem Blatt können Benutzer alle Felder erstellen und die Analyseeigenschaften für die einzelnen Felder festlegen.

> [!IMPORTANT]
> Eine Sprachanalyse kann nur während der Felddefinition festgelegt werden, also nur beim Erstellen eines neuen Index und beim Hinzufügen eines neuen Felds zu einem vorhandenen Index. Stellen Sie sicher, dass Sie beim Erstellen des Felds alle Attribute, einschließlich der Analyse, vollständig angeben. Sie können die Attribute nicht mehr bearbeiten und den Analysetyp nicht mehr ändern, nachdem Ihre Änderungen gespeichert wurden.
>
>

## <a name="define-a-new-field-definition"></a>Definieren einer neuen Felddefinition
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie das Dienstblatt für Ihren Suchdienst.
2. Klicken Sie oben im Dienstdashboard auf der Befehlsleiste auf **Index hinzufügen** , um einen neuen Index zu beginnen, oder öffnen Sie einen vorhandenen Index, um eine Analyse für neue Felder festzulegen, die Sie einem vorhandenen Index hinzufügen.
3. Das Blatt „Felder“ mit Optionen zum Festlegen des Indexschemas wird angezeigt. Hier sehen Sie auch die Registerkarte für die Analyse, über die Sie eine Sprachanalyse auswählen.
4. Beginnen Sie unter „Felder“ mit einer Felddefinition, indem Sie einen Namen angeben, den Datentyp auswählen und Attribute festlegen. Mit diesen Attributen geben Sie u. a. an, dass das Feld für die Volltextsuche verwendet, in Suchergebnissen abgerufen, in Facettennavigationsstrukturen verwendet und sortiert werden kann.
5. Bevor Sie mit dem nächsten Feld fortfahren, öffnen Sie die Registerkarte **Analyse** .

![][1]
*Klicken Sie zum Auswählen einer Analyse auf dem Blatt „Felder“ auf die Registerkarte „Analyse“* .

## <a name="choose-an-analyzer"></a>Auswählen einer Analyse
1. Führen Sie einen Bildlauf zu dem Feld durch, das Sie definieren.
2. Wenn Sie das Feld noch nicht als durchsuchbar gekennzeichnet haben, aktivieren Sie jetzt das Kontrollkästchen, um es als **durchsuchbar**zu kennzeichnen.
3. Klicken Sie auf den Bereich „Analyse“, um die Liste der verfügbaren Analysen anzuzeigen.
4. Wählen Sie die zu verwendende Analyse aus.

![][2]
*Wählen Sie für jedes Feld eine der unterstützten Analysen aus*.

Standardmäßig verwenden alle durchsuchbaren Felder die sprachunabhängige [Lucene-Standardanalyse](https://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) . Die vollständige Liste der unterstützten Analysen finden Sie unter [Sprachunterstützung in Azure Search](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Sobald die Sprachanalyse für ein Feld ausgewählt wurde, wird sie für jede Indizierungs- und Suchanfrage für dieses Feld verwendet. Wenn eine Abfrage mithilfe verschiedener Analysen für mehrere Felder ausgegeben wird, wird sie von der richtigen Analyse unabhängig für das jeweilige Feld verarbeitet.

Viele webbasierte und mobile Anwendungen werden von Benutzern auf der ganzen Welt in unterschiedlichen Sprachen genutzt. Es ist möglich, einen Index für ein derartiges Szenario festzulegen. Hierzu muss ein Feld für jede unterstützte Sprache erstellt werden.

![][3]
*Indexdefinition mit einem Beschreibungsfeld für jede unterstützte Sprache*

Wenn die Sprache des Agents, der eine Abfrage ausgibt, bekannt ist, kann eine Suchabfrage mit dem **SearchFields** -Abfrageparameter auf ein bestimmtes Feld beschränkt werden. Die folgende Abfrage wird nur für die Beschreibung in polnischer Sprache ausgegeben:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2019-05-06`

Sie können im Portal im **Suchexplorer** Ihren Index abfragen, um eine Abfrage einzufügen, die der oben gezeigten ähnlich ist. Der Suchexplorer ist auf dem Blatt des Diensts auf der Befehlsleiste verfügbar. Ausführliche Informationen finden Sie unter [Abfragen des Azure Search-Indexes im Portal](search-explorer.md) .

Manchmal ist die Sprache des Agents, der eine Abfrage ausgibt, nicht bekannt. In diesem Fall kann die Abfrage für alle Felder gleichzeitig ausgegeben werden. Bei Bedarf kann die Ausgabe der Ergebnisse in einer bestimmten Sprache mithilfe von [Bewertungsprofilen](https://msdn.microsoft.com/library/azure/dn798928.aspx) festgelegt werden. Im folgenden Beispiel erhalten in der Beschreibung in englischer Sprache gefundene Übereinstimmungen eine höhere Bewertung als Übereinstimmungen in polnischer und französischer Sprache:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

Wenn Sie .NET-Entwickler sind, können Sie Sprachanalysen mit dem [Azure Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search)konfigurieren. Die neueste Version unterstützt auch die Microsoft-Sprachanalysen.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
