---
title: 'Verwalten von Wissensdatenbanken: QnA Maker'
description: Über QnA Maker können Sie zur Verwaltung Ihrer Wissensdatenbanken auf deren Einstellungen und Inhalte zugreifen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: c7bb79cbd67ba2fb151641ddeeb31b54e399b906
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938255"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Erstellen einer Wissensdatenbank und Verwalten der Einstellungen

Über QnA Maker können Sie zur Verwaltung Ihrer Wissensdatenbanken auf deren Einstellungen und Datenquellen zugreifen.

## <a name="prerequisites"></a>Voraussetzungen

> [!div class="checklist"]
> * Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.
> * Eine über das Azure-Portal erstellte [QnA Maker-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker). Merken Sie sich die Azure Active Directory-ID, das Abonnement und den QnA-Ressourcennamen, die Sie beim Erstellen der Ressource ausgewählt haben.

## <a name="create-a-knowledge-base"></a>Erstellen einer Wissensdatenbank

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen beim Portal [QnAMaker.ai](https://QnAMaker.ai) an.

1. Wählen Sie im QnA Maker-Portal die Option **Wissensdatenbank erstellen** aus.

1. Überspringen Sie **Schritt 1** auf der Seite **Erstellen**, wenn Sie bereits über eine QnA Maker-Ressource verfügen.

    Wenn Sie die Ressource noch nicht erstellt haben, wählen Sie **Create a QnA service** (QnA-Dienst erstellen) aus. Sie werden an das [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) weitergeleitet, um einen QnA Maker-Dienst in Ihrem Abonnement einzurichten. Merken Sie sich die Azure Active Directory-ID, das Abonnement und den QnA-Ressourcennamen, die Sie beim Erstellen der Ressource ausgewählt haben.

    Wenn Sie die Ressource im Azure-Portal erstellt haben, navigieren Sie zurück zum QnA Maker-Portal, aktualisieren Sie die Browserseite, und fahren Sie mit **Schritt 2** fort.

1. Wählen Sie in **Schritt 3** Ihre Active Directory-Instanz, das Abonnement, den Dienst (Ressource) und die Sprache für alle im Dienst erstellten Wissensdatenbanken aus.

   ![Screenshot: Auswählen einer Wissensdatenbank im QnA Maker-Dienst](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Geben Sie Ihrer Wissensdatenbank in **Schritt 3** den Namen `My Sample QnA KB`.

1. Konfigurieren Sie in **Schritt 4** die Einstellungen anhand der folgenden Tabelle:

    |Einstellung|Wert|
    |--|--|
    |**Enable multi-turn extraction from URLs, .pdf or .docx files** (Mehrfachdurchlauf-Extrahierung von URLs, PDF- oder DOCX-Dateien aktivieren)|Aktiviert|
    |**Default answer text** (Standardantworttext)| `Quickstart - default answer not found.`|
    |**+ URL hinzufügen**|`https://azure.microsoft.com/en-us/support/faq/`|
    |**Smalltalk**|Wählen Sie **Professional** aus.|

1. Wählen Sie in **Schritt 5** die Option **Wissensdatenbank erstellen** aus.

    Der Extraktionsvorgang nimmt einen Moment in Anspruch, um das Dokument zu lesen sowie Fragen und Antworten zu identifizieren.

    Nachdem die Wissensdatenbank von QnA Maker erfolgreich erstellt wurde, wird die Seite **Wissensdatenbank** geöffnet. Auf dieser Seite können Sie den Inhalt der Wissensdatenbank bearbeiten.

## <a name="edit-knowledge-base"></a>Bearbeiten der Knowledge Base

1.  Wählen Sie in der oberen Navigationsleiste **Meine Wissensdatenbanken** aus.

       Es werden alle Dienste, die Sie erstellt haben oder die für Sie freigegeben wurden, in absteigender Reihenfolge nach dem Datum für **Letzte Änderung** angezeigt.

       ![Meine Wissensdatenbanken](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Wählen Sie eine bestimmte Wissensdatenbank aus, um Änderungen daran vorzunehmen.

1.  Wählen Sie **Settings** aus. Die folgende Liste enthält Felder, die Sie ändern können:

       |Zielsetzung|Aktion|
       |--|--|
       |URL hinzufügen|Sie können neue URLs hinzufügen, um der Wissensdatenbank neue FAQ-Inhalte hinzuzufügen, indem Sie auf den Link **Wissensdatenbank verwalten > „+ URL hinzufügen“** klicken.|
       |URL löschen|Sie können vorhandene URLs löschen, indem Sie das Löschsymbol, d.h. den Papierkorb, auswählen.|
       |Inhalt aktualisieren|Damit Ihre Wissensdatenbank den neuesten Inhalt vorhandener URLs durchforstet, aktivieren Sie das Kontrollkästchen **Aktualisieren**. Dadurch wird die Wissensdatenbank einmal mit den neuesten URL-Inhalten aktualisiert. Es wird kein regelmäßiger Zeitplan für Updates festgelegt.|
       |Datei hinzufügen|Sie können einer Wissensdatenbank ein unterstütztes Dateidokument hinzufügen, indem Sie **Wissensdatenbank verwalten** und dann **+ Datei hinzufügen** auswählen.|
    |Importieren|Sie können auch vorhandene Wissensdatenbanken importieren, indem Sie die Schaltfläche **Wissensdatenbank importieren** auswählen. |
    |Aktualisieren|Das Aktualisieren der Wissensdatenbank hängt vom **Verwaltungstarif** ab, der beim Erstellen des QnA Maker-Diensts verwendet wird, der mit Ihrer Wissensdatenbank verknüpft ist. Sie können den Verwaltungstarif bei Bedarf auch über das Azure-Portal aktualisieren.

  1. Wenn Sie alle Änderungen an der Wissensdatenbank vorgenommen haben, wählen Sie rechts oben auf der Seite **Speichern und trainieren** aus, um die Änderungen dauerhaft zu speichern.

       ![Speichern und trainieren](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Wenn Sie die Seite verlassen, bevor Sie **Speichern und trainieren** auswählen, gehen alle Änderungen verloren.



## <a name="manage-large-knowledge-bases"></a>Verwalten großer Wissensdatenbanken

* **Datenquellengruppen:** Die Fragen und Antworten werden nach der Datenquelle gruppiert, aus der sie extrahiert wurden. Sie können die Datenquelle erweitern oder reduzieren.

    ![Verwenden der Datenquellenleiste von QnA Maker, um Datenquellenfragen und -antworten zu reduzieren bzw. zu erweitern](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Durchsuchen der Wissensdatenbank:** Sie können in der Wissensdatenbank suchen, indem Sie einen Suchbegriff in das Textfeld am oberen Rand der Wissensdatenbanktabelle eingeben. Drücken Sie die EINGABETASTE, um in den Fragen, Antworten oder Metadaten danach zu suchen. Klicken Sie auf das X-Symbol, um den Suchfilter zu entfernen.

    ![Verwenden des Suchfelds von QnA Maker (über den Fragen und Antworten), um nur Elemente anzuzeigen, die den Filterkriterien entsprechen](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Paginierung:** Navigieren Sie zur Verwaltung umfangreicher Wissensdatenbanken schnell durch Datenquellen.

    ![Verwenden der Paginierungsfeatures von QnA Maker (über den Fragen und Antworten), um seitenweise durch Fragen und Antworten zu navigieren](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Löschen von Wissensdatenbanken

Das Löschen einer Wissensdatenbank (Knowledge Base, KB) ist ein endgültiger Vorgang. Dieser Vorgang kann nicht rückgängig gemacht werden. Vor dem Löschen einer Wissensdatenbank, sollten Sie die Wissensdatenbank auf der Seite **Einstellungen** im QnA Maker-Portal exportieren.

Wenn Sie Ihre Wissensdatenbank für [Mitwirkende](collaborate-knowledge-base.md) freigeben und dann löschen, geht der Zugriff auf die Wissensdatenbank für alle verloren.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Verwalten der Sprache](../index.yml) aller Wissensdatenbanken in einer Ressource.

* Bearbeiten von Frage-Antwort-Paaren
* Verwalten der von QnA Maker verwendeten Azure-Ressourcen