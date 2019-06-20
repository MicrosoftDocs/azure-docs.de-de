---
title: 'Bearbeiten einer Wissensdatenbank: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Mit QnA Maker können Sie die Inhalte Ihrer Wissensdatenbank auf einer benutzerfreundlichen Bearbeitungsoberfläche verwalten.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c9add80b7494ae2a8e671967a96dc5d3c7307f51
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65541670"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Bearbeiten einer Wissensdatenbank in QnA Maker

Mit QnA Maker können Sie die Inhalte Ihrer Wissensdatenbank auf einer benutzerfreundlichen Bearbeitungsoberfläche verwalten.

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>Bearbeiten der Inhalte Ihrer Wissensdatenbank

1.  Wählen Sie in der oberen Navigationsleiste **Meine Wissensdatenbanken** aus. 

    Es werden alle Dienste, die Sie erstellt haben oder die für Sie freigegeben wurden, in absteigender Reihenfolge nach dem Datum für **Letzte Änderung** angezeigt.

    ![Meine Wissensdatenbanken](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Wählen Sie eine bestimmte Wissensdatenbank aus, um Änderungen daran vorzunehmen.
 
1. Wählen Sie **Settings**aus. Hier können Sie das Pflichtfeld „Dienstname“ bearbeiten.
  
    |Zielsetzung|Aktion|
    |--|--|
    |URL hinzufügen|Sie können neue URLs hinzufügen, um der Wissensdatenbank neue FAQ-Inhalte hinzuzufügen, indem Sie auf den Link **Wissensdatenbank verwalten > „+ URL hinzufügen“** klicken.|
    |URL löschen|Sie können vorhandene URLs löschen, indem Sie das Löschsymbol, d.h. den Papierkorb, auswählen.|
    |URL-Inhalt aktualisieren|Damit Ihre Wissensdatenbank den neuesten Inhalt vorhandener URLs durchforstet, aktivieren Sie das Kontrollkästchen **Aktualisieren**. Dadurch wird die Wissensdatenbank mit den neuesten URL-Inhalten aktualisiert.|
    |Datei hinzufügen|Sie können einer Wissensdatenbank ein unterstütztes Dateidokument hinzufügen, indem Sie **Wissensdatenbank verwalten** und dann **+ Datei hinzufügen** auswählen.|
    |Importieren|Sie können auch vorhandene Wissensdatenbanken importieren, indem Sie die Schaltfläche **Wissensdatenbank importieren** auswählen. |
    |Aktualisieren|Das Aktualisieren der Wissensdatenbank hängt vom **Verwaltungstarif** ab, der beim Erstellen des QnA Maker-Diensts verwendet wird, der mit Ihrer Wissensdatenbank verknüpft ist. Sie können den Verwaltungstarif auch über das Azure-Portal aktualisieren, wenn erforderlich.

1. Wenn Sie alle Änderungen an der Wissensdatenbank vorgenommen haben, wählen Sie auf der Seite oben rechts **Speichern und trainieren** aus, um die Änderungen dauerhaft zu speichern.    

    ![Speichern und trainieren](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Wenn Sie die Seite verlassen, bevor Sie **Speichern und trainieren** auswählen, gehen alle Änderungen verloren.

## <a name="add-a-qna-pair"></a>Hinzufügen eines Fragen-und-Antworten-Paars

Wählen Sie auf der Seite **Einstellungen** die Option **Fragen-und-Antworten-Paar hinzufügen** aus, um der Wissensdatenbanktabelle eine neue Zeile hinzuzufügen.

![Hinzufügen eines Fragen-und-Antworten-Paars](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Löschen eines Fragen-und-Antworten-Paars

Zum Löschen eines Fragen-und-Antworten-Paars klicken Sie in der Fragen-und-Antworten-Zeile ganz rechts auf das Symbol **Löschen**. Das Paar wird endgültig gelöscht. Dieser Vorgang kann nicht rückgängig gemacht werden. Es ist ratsam, Ihre Wissensdatenbank auf der Seite **Veröffentlichen** zu exportieren, bevor Sie Paare löschen. 

![Löschen eines Fragen-und-Antworten-Paars](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Hinzufügen alternativer Fragen

Fügen Sie alternative Fragen zu einem vorhandenen Fragen-und-Antworten-Paar hinzu, um die Wahrscheinlichkeit einer Übereinstimmung mit einer Benutzerabfrage zu erhöhen.

![Hinzufügen alternativer Fragen](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Hinzufügen von Metadaten

Fügen Sie Metadatenpaare hinzu, indem Sie zuerst **Ansichtsoptionen** und dann **Metadaten anzeigen** auswählen. Dadurch wird die Metadatenspalte angezeigt. Wählen Sie anschließend das Zeichen **+** aus, um ein Metadatenpaar hinzuzufügen. Dieses Paar besteht aus einem Schlüssel und einen Wert.

![Hinzufügen von Metadaten](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Denken Sie daran, die Wissensdatenbank beim Durchführen von Änderungen in regelmäßigen Abständen zu speichern („Speichern und trainieren“), um einen Verlust von Änderungen zu vermeiden.

## <a name="manage-large-knowledge-bases"></a>Verwalten großer Wissensdatenbanken

* **Datenquellengruppen:** Die Fragen und Antworten werden nach der Datenquelle gruppiert, aus der sie extrahiert wurden. Sie können die Datenquelle erweitern oder reduzieren.

    ![Verwenden der Datenquellenleiste von QnA Maker, um Datenquellenfragen und -antworten zu reduzieren bzw. zu erweitern](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Durchsuchen der Wissensdatenbank:** Sie können in der Wissensdatenbank suchen, indem Sie einen Suchbegriff in das Textfeld am oberen Rand der Wissensdatenbanktabelle eingeben. Drücken Sie die EINGABETASTE, um in den Fragen, Antworten oder Metadaten danach zu suchen. Klicken Sie auf das X-Symbol, um den Suchfilter zu entfernen.

    ![Verwenden des Suchfelds von QnA Maker (über den Fragen und Antworten), um nur Elemente anzuzeigen, die den Filterkriterien entsprechen](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Paginierung:** Navigieren Sie zur Verwaltung umfangreicher Wissensdatenbanken schnell durch Datenquellen.

    ![Verwenden der Paginierungsfeatures von QnA Maker (über den Fragen und Antworten), um seitenweise durch Fragen und Antworten zu navigieren](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Löschen von Wissensdatenbanken

Das Löschen einer Wissensdatenbank (Knowledge Base, KB) ist ein endgültiger Vorgang. Dieser Vorgang kann nicht rückgängig gemacht werden. Vor dem Löschen einer Wissensdatenbank, sollten Sie die Wissensdatenbank auf der Seite **Einstellungen** im QnA Maker-Portal exportieren. 

Wenn Sie Ihre Wissensdatenbank für [Projektmitarbeiter](collaborate-knowledge-base.md) freigeben und sie dann löschen, verlieren alle Benutzer den Zugriff auf die Wissensdatenbank. 

## <a name="delete-azure-resources"></a>Löschen von Azure-Ressourcen 

Wenn Sie Azure-Ressourcen löschen, die für Ihre QnA Maker-Wissensdatenbanken verwendet werden, funktionieren diese Wissensdatenbanken nicht mehr. Stellen Sie vor dem Löschen von Ressourcen sicher, dass Sie Ihre Wissensdatenbanken auf der Seite **Einstellungen** exportieren. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Zusammenarbeiten an einer Wissensdatenbank](./collaborate-knowledge-base.md)
