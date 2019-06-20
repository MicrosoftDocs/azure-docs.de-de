---
title: Erfassen von Daten von Event Hubs in Azure Data Lake Storage Gen1 | Microsoft-Dokumentation
description: Verwenden von Azure Data Lake Storage Gen1 zum Erfassen von Daten von Event Hubs
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bb67c1769510710b368bef4dc0b501f939b3427e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60879521"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Verwenden von Azure Data Lake Storage Gen1 zum Erfassen von Daten von Event Hubs

Erfahren Sie, wie Sie Azure Data Lake Storage Gen1 zum Erfassen von Daten verwenden, die von Azure Event Hubs empfangen werden.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Ein Azure Data Lake Storage Gen1-Konto**. Eine Anleitung zur Erstellung finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Ein Event Hubs-Namespace**. Anweisungen hierzu finden Sie unter [Erstellen eines Event Hubs-Namespaces](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Stellen Sie sicher, dass das Data Lake Storage Gen1-Konto und der Event Hubs-Namespace im selben Azure-Abonnement enthalten sind.


## <a name="assign-permissions-to-event-hubs"></a>Zuweisen von Berechtigungen für Event Hubs

In diesem Abschnitt erstellen Sie einen Ordner in dem Konto, in dem Sie die Daten von Event Hubs erfassen möchten. Zudem weisen Sie Event Hubs Berechtigungen zu, damit der Dienst Daten in ein Data Lake Storage Gen1-Konto schreiben kann. 

1. Öffnen Sie das Data Lake Storage Gen1-Konto, in dem Sie Daten von Event Hubs erfassen möchten, und klicken Sie dann auf **Daten-Explorer**.

    ![Data Lake Storage Gen1-Daten-Explorer](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1-Daten-Explorer")

1.  Klicken Sie auf **Neuer Ordner**, und geben Sie dann einen Namen für den Ordner ein, in dem Sie die Daten erfassen möchten.

    ![Erstellen eines neuen Ordners in Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Erstellen eines neuen Ordners in Data Lake Storage Gen1")

1. Weisen Sie auf der Data Lake Storage Gen1-Stammebene Berechtigungen zu. 

    a. Klicken Sie auf **Daten-Explorer**, wählen Sie den Stamm des Data Lake Storage Gen1-Kontos aus, und klicken Sie dann auf **Zugriff**.

    ![Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Stamm](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Stamm")

    b. Klicken Sie unter **Zugriff** auf **Hinzufügen** und dann auf **Benutzer oder Gruppe auswählen**, und suchen Sie anschließend nach `Microsoft.EventHubs`. 

    ![Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Stamm](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Stamm")
    
    Klicken Sie auf **Auswählen**.

    c. Klicken Sie unter **Berechtigungen zuweisen** auf **Berechtigungen auswählen**. Legen Sie **Berechtigungen** auf **Ausführen** fest. Legen Sie **Add to** (Hinzufügen zu) auf **Diesen Ordner und alle untergeordneten Ordner** fest. Legen Sie **Add as** (Hinzufügen als) auf **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** fest.

    > [!IMPORTANT]
    > Wenn Sie eine neue Ordnerhierarchie für die Erfassung der von Azure Event Hubs empfangenen Daten erstellen, ist dies eine einfache Möglichkeit, den Zugriff auf den Zielordner sicherzustellen.  Das Hinzufügen von Berechtigungen zu allen untergeordneten Ordnern eines Ordners auf oberster Ebene mit vielen untergeordneten Dateien und Ordnern kann jedoch lange dauern.  Wenn Ihr Stammordner eine große Anzahl von Dateien und Ordnern enthält, kann es schneller sein, die Berechtigung **Ausführen** für `Microsoft.EventHubs` jedem Ordner im Pfad zu Ihrem endgültigen Zielordner einzeln hinzuzufügen. 

    ![Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Stamm](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Stamm")

    Klicken Sie auf **OK**.

1. Weisen Sie Berechtigungen für den Ordner im Data Lake Storage Gen1-Konto zu, in dem Sie Daten erfassen möchten.

    a. Klicken Sie auf **Daten-Explorer**, wählen Sie den Ordner im Data Lake Storage Gen1-Konto aus, und klicken Sie dann auf **Zugriff**.

    ![Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Ordner](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Ordner")

    b. Klicken Sie unter **Zugriff** auf **Hinzufügen** und dann auf **Benutzer oder Gruppe auswählen**, und suchen Sie anschließend nach `Microsoft.EventHubs`. 

    ![Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Ordner](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Ordner")
    
    Klicken Sie auf **Auswählen**.

    c. Klicken Sie unter **Berechtigungen zuweisen** auf **Berechtigungen auswählen**. Legen Sie **Berechtigungen** auf **Read, Write** (Lesen, Schreiben) und **Ausführen** fest. Legen Sie **Add to** (Hinzufügen zu) auf **Diesen Ordner und alle untergeordneten Ordner** fest. Legen Sie abschließend **Add as** (Hinzufügen als) auf **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** fest.

    ![Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Ordner](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Ordner")
    
    Klicken Sie auf **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Konfigurieren von Event Hubs zum Erfassen von Daten in Data Lake Storage Gen1

In diesem Abschnitt erstellen Sie einen Event Hub innerhalb eines Event Hubs-Namespaces. Außerdem konfigurieren Sie den Event Hub zum Erfassen von Daten in einem Azure Data Lake Storage Gen1-Konto. In diesem Abschnitt wird davon ausgegangen, dass Sie bereits einen Event Hubs-Namespace erstellt haben.

1. Klicken Sie im Bereich **Übersicht** des Event Hubs-Namespaces auf **+ Event Hub**.

    ![Erstellen eines Event Hubs](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Erstellen eines Event Hubs")

1. Geben Sie die folgenden Werte an, um Event Hubs zum Erfassen von Daten in Data Lake Storage Gen1 zu konfigurieren.

    ![Erstellen eines Event Hubs](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Erstellen eines Event Hubs")

    a. Geben Sie einen Namen für den Event Hub an.
    
    b. In diesem Tutorial legen Sie **Partitionsanzahl** und **Nachrichtenaufbewahrung** auf die Standardwerte fest.
    
    c. Legen Sie **Erfassen** auf **On** (Ein) fest. Legen Sie das **Zeitfenster** (Häufigkeit der Erfassung) und **Größenfenster** (zu erfassende Datengröße) fest. 
    
    d. Wählen Sie für **Capture Provider** (Erfassungsanbieter) die Option **Azure Data Lake Store** aus, und wählen Sie dann das zuvor erstellte Data Lake Storage Gen1-Konto aus. Geben Sie für **Data Lake Path** (Data Lake-Pfad) den Namen des Ordners ein, den Sie im Data Lake Storage Gen1-Konto erstellt haben. Sie müssen nur den relativen Pfad zum Ordner angeben.

    e. Übernehmen Sie für **Beispielnamensformate für Erfassungsdateien** den Standardwert. Diese Option steuert die Ordnerstruktur, die unter dem Erfassungsordner erstellt wird.

    f. Klicken Sie auf **Create**.

## <a name="test-the-setup"></a>Testen der Einstellungen

Sie können die Lösung jetzt testen, indem Sie Daten an den Azure Event Hub senden. Folgen Sie den Anweisungen unter [Senden von Ereignissen an Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Sobald Sie mit dem Senden der Daten beginnen, werden die Daten in der von Ihnen angegebenen Ordnerstruktur in Data Lake Storage Gen1 angezeigt. Beispielsweise sehen Sie wie im folgenden Screenshot gezeigt eine Ordnerstruktur in Ihrem Data Lake Storage Gen1-Konto.

![EventHub-Beispieldaten in Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "EventHub-Beispieldaten in Data Lake Storage Gen1")

> [!NOTE]
> Auch wenn keine Nachrichten in Event Hubs eingehen, schreibt Event Hubs leere Dateien, die nur die Header enthalten, in das Data Lake Storage Gen1-Konto. Die Dateien werden in dem Zeitintervall geschrieben, das Sie beim Erstellen des Event Hubs angegeben haben.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analysieren von Daten in Data Lake Storage Gen1

Sobald die Daten sich in Data Lake Storage Gen1 befinden, können Sie Analyseaufträge zum Verarbeiten und Berechnen der Daten ausführen. Informationen zum Ausführen solcher Aufträge mit Azure Data Lake Analytics finden Sie unter [U-SQL Avro Example](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) (U-SQL-Avro-Beispiel).
  

## <a name="see-also"></a>Weitere Informationen
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Kopieren von Daten aus Azure Storage-Blobs in Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
