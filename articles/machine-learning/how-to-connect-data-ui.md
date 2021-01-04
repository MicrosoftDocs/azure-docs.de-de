---
title: Herstellen einer Verbindung mit Daten in Speicherdiensten in Azure
titleSuffix: Azure Machine Learning
description: Erstellen von Datenspeichern und Datasets, um sichere Verbindungen mit Daten in Speicherdiensten in Azure mit Azure Machine Learning Studio herzustellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to, data4ml
ms.openlocfilehash: 554c815e6384115d56611e497f49a2c97ed15e38
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461438"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Herstellen einer Verbindung mit Daten in Azure Machine Learning Studio

In diesem Artikel erfahren Sie, wie Sie mit [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) auf Ihre Daten zugreifen. Stellen Sie eine Verbindung mit Ihren Daten in Speicherdiensten in Azure mit [Azure Machine Learning-Datenspeichern](how-to-access-data.md) her, und verpacken Sie diese Daten dann für Aufgaben in ihren ML-Workflows mit [Azure Machine Learning-Datasets](how-to-create-register-datasets.md).

In der folgenden Tabelle werden die Vorteile von Datenspeichern und Datasets definiert und zusammengefasst. 

|Object|BESCHREIBUNG| Vorteile|   
|---|---|---|
|Datenspeicher| Stellen Sie eine sichere Verbindung mit Ihrem Speicherdienst in Azure her, indem Sie Ihre Verbindungsinformationen, wie Ihre Abonnement-ID und Tokenautorisierung, in Ihrem [Schlüsseltresor](https://azure.microsoft.com/services/key-vault/) speichern, der mit dem Arbeitsbereich verknüpft ist. | Da Ihre Daten sicher gespeichert sind, <br><br> <li> setzen Sie&nbsp;Authentifizierungsanmeldeinformationen&nbsp;oder&nbsp;ursprüngliche&nbsp;Datenquellen&nbsp;keinem&nbsp;Risiko aus. <li> Sie müssen Sie nicht mehr in Ihren Skripts hartcodieren.
|Datasets| Durch Erstellen eines Datasets erstellen Sie einen Verweis auf den Speicherort der Datenquelle sowie eine Kopie der zugehörigen Metadaten. Datasets ermöglichen Folgendes: <br><br><li> Zugreifen auf Daten während des Modelltrainings.<li> Freigeben von Daten und Zusammenarbeiten mit anderen Benutzern<li> Nutzen von Open-Source-Bibliotheken wie Pandas zum Untersuchen von Daten. | Da Datasets nur langsam ausgewertet werden und die Daten am vorhandenen Speicherort verbleiben, profitieren Sie von folgenden Vorteilen: <br><br><li>Aufbewahren einer einzelnen Datenkopie in Ihrem Speicher.<li> Keine zusätzlichen Speicherkosten. <li> Sie laufen nicht Gefahr, unabsichtlich Ihre ursprünglichen Datenquellen zu ändern.<li>Der ML-Workflow wird verbessert und beschleunigt. 

Informationen dazu, welche Rolle Datenspeicher und Datasets im Workflow für Datenzugriff in Azure Machine Learning spielen, finden Sie im Artikel [Sicherer Zugriff auf Daten](concept-data.md#data-workflow).

Informationen zu einer Code First-Funktion finden Sie in den folgenden Artikeln, um das [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py) für Folgendes zu verwenden:
* [Herstellen einer Verbindung mit Azure-Speicherdiensten mit Datenspeichern](how-to-access-data.md). 
* [Erstellen von Azure Machine Learning-Datasets](how-to-create-register-datasets.md). 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) aus.

- Zugriff auf [Azure Machine Learning Studio](https://ml.azure.com/).

- Ein Azure Machine Learning-Arbeitsbereich. [Erstellen Sie einen Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).

    -  Wenn Sie einen Arbeitsbereich erstellen, werden automatisch ein Azure-Blobcontainer und eine Azure-Dateifreigabe als Datenspeicher im Arbeitsbereich registriert. Sie erhalten die Namen `workspaceblobstore` und `workspacefilestore`. Wenn Blobspeicher für Ihre Anforderungen ausreicht, wird `workspaceblobstore` als Standarddatenspeicher festgelegt und bereits für die Verwendung konfiguriert. Andernfalls benötigen Sie ein Speicherkonto in Azure mit einem [unterstützten Speichertyp](how-to-access-data.md#matrix).
    

## <a name="create-datastores"></a>Erstellen von Datenspeichern

Aus diesen [Azure-Speicherlösungen](how-to-access-data.md#matrix) können Sie Datenspeicher erstellen. **Für nicht unterstützte Speicherlösungen** sowie zur Einsparung von Kosten für ausgehende Daten bei ML-Experimenten müssen Sie [Ihre Daten in eine unterstützte Azure-Speicherlösung verschieben](how-to-access-data.md#move). [Weitere Informationen zu Datenspeichern](how-to-access-data.md). 

Erstellen Sie einen neuen Datenspeicher in wenigen Schritten mit Azure Machine Learning Studio.

> [!IMPORTANT]
> Wenn sich Ihr Datenspeicherkonto in einem virtuellen Netzwerk befindet, sind zusätzliche Konfigurationsschritte erforderlich, um sicherzustellen, dass von Studio auf Ihre Daten zugegriffen werden kann. Stellen Sie wie unter [Netzwerkisolation und Datenschutz](how-to-enable-studio-virtual-network.md) beschrieben sicher, dass die erforderlichen Konfigurationsschritte ausgeführt wurden.

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com/) an.
1. Wählen Sie im linken Bereich unter **Verwalten** die Option **Datenspeicher** aus.
1. Wählen Sie **+ Neuer Datenspeicher** aus.
1. Füllen Sie das Formular aus, um einen neuen Datenspeicher zu erstellen und zu registrieren. Das Formular aktualisiert sich ausgehend von den ausgewählten Optionen für den Azure-Speichertyp und den Authentifizierungstyp intelligent selbst. Lesen Sie den Abschnitt [Speicherzugriff und Berechtigungen](#access-validation), um zu erfahren, wo Sie die erforderlichen Anmeldeinformationen zum Ausfüllen dieses Formulars finden.

Das folgende Beispiel zeigt, wie das Formular aussieht, wenn Sie einen **Azure-Blobdatenspeicher** erstellen:

![Formular für einen neuen Datenspeicher](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>Erstellen von Datasets

Nachdem Sie einen Datenspeicher erstellt haben, erstellen Sie ein Dataset, um mit Ihren Daten zu interagieren. Datasets packen Ihre Daten in ein selten ausgewertetes Objekt für Aufgaben des maschinellen Lernens wie das Training. [Erfahren Sie mehr über Datasets](how-to-create-register-datasets.md).

Es gibt zwei Arten von Datasets: FileDataset und TabularDataset. 
[FileDatasets](how-to-create-register-datasets.md#filedataset) erstellen Verweise auf mindestens eine Datei oder auf öffentliche URLs. [TabularDatasets](how-to-create-register-datasets.md#tabulardataset) stellen Ihre Daten in einem Tabellenformat dar. 

In den folgenden Schritten und in der Animation wird gezeigt, wie ein Dataset im [Azure Machine Learning Studio](https://ml.azure.com) erstellt wird.

> [!Note]
> Datasets, die über Azure Machine Learning Studio erstellt werden, werden automatisch beim Arbeitsbereich registriert.

![Erstellen eines Datasets mithilfe der Benutzeroberfläche](./media/how-to-connect-data-ui/create-dataset-ui.gif)

So erstellen Sie ein Dataset im Studio
1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com/) an.
1. Wählen Sie **Datasets** im Abschnitt **Assets** im linken Bereich aus.
1. Wählen Sie **Dataset erstellen** aus, um die Quelle Ihres Datasets auszuwählen. Bei dieser Quelle kann es sich um lokale Dateien, einen Datenspeicher, öffentliche URLs oder [Azure Open Datasets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md) handeln.
1. Wählen Sie **Tabellarisch** oder **Datei** als Datasettyp aus.
1. Wählen Sie **Weiter** aus, um das Formular **Datenspeicher- und Dateiauswahl** zu öffnen. In diesem Formular wählen Sie aus, wo das Dataset nach dem Erstellen aufbewahrt werden soll, sowie welche Datendateien für Ihr Dataset verwendet werden sollen.
    1. Aktivieren Sie das Überspringen der Überprüfung, wenn sich Ihre Daten in einem virtuellen Netzwerk befinden. Weitere Informationen finden Sie unter [Isolierung virtueller Netzwerke und Datenschutz](how-to-enable-studio-virtual-network.md).
    1. Für Tabellendatasets können Sie das Merkmal „timeseries angeben, um Datums- und Uhrzeitvorgänge für Ihr Dataset zu ermöglichen. Informieren Sie sich darüber, wie Sie [Ihrem Dataset das Merkmal „timeseries“ hinzufügen](how-to-monitor-datasets.md#studio-dataset).
1. Wählen Sie **Weiter** aus, um die Formulare **Einstellungen und Vorschau** und **Schema** auszufüllen. Sie werden basierend auf dem Dateityp auf intelligente Weise aufgefüllt, und Sie können das Dataset in diesen Formularen vor der Erstellung weiter konfigurieren. 
1. Wählen Sie **Weiter** aus, um das Formular **Details bestätigen** zu überprüfen. Überprüfen Sie Ihre Auswahl, und erstellen Sie ein optionales Datenprofil für das Dataset. Weitere Informationen zur [Datenprofilerstellung](#profile).
1. Wählen Sie **Erstellen** aus, um die Erstellung des Datasets abzuschließen.

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>Datenprofil und Vorschau

Nachdem Sie das Dataset erstellt haben, überprüfen Sie anhand der folgenden Schritte, ob Sie das Profil und die Vorschau in Studio anzeigen können. 

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com/) an.
1. Wählen Sie **Datasets** im Abschnitt **Assets** im linken Bereich aus.
1. Wählen Sie den Namen des Datasets aus, das Sie anzeigen möchten. 
1. Wählen Sie die Registerkarte **Explore** (Untersuchen). 
1. Wählen Sie die Registerkarte **Vorschau** oder **Profil** aus. 

![Anzeigen des Datasetprofils und der Vorschau](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

Sie können eine Vielzahl von Zusammenfassungsstatistiken zu Ihrem Dataset erhalten, um zu überprüfen, ob Ihr Dataset für ML bereit ist. Für nicht numerische Spalten sind nur grundlegende Statistiken wie Mindestwert, Maximalwert und Fehleranzahl enthalten. Für numerische Spalten können Sie auch ihre statistische Zeit und die geschätzten Quantile überprüfen. 

Das Datenprofil des Azure Machine Learning-Datasets enthält insbesondere Folgendes:

>[!NOTE]
> Leere Einträge werden für Features mit irrelevanten Typen angezeigt.

|Statistik|BESCHREIBUNG
|------|------
|Funktion| Der Name der Spalte, die zusammengefasst wird.
|Profil| Eine Inlinevisualisierung basierend auf dem abgeleiteten Typ. Beispielsweise weisen Zeichenfolgen, boolesche Werte und Datumsangaben Wertanzahlen auf, während Dezimalwerte (numerische Werte) Näherungswerte für Histogramme besitzen. Dadurch können Sie einen schnellen Überblick über die Verteilung der Daten erhalten.
|Typverteilung| Eine Inlinewertanzahl von Typen in einer Spalte. NULL-Werte sind eigene Typen, sodass diese Visualisierung für die Erkennung von ungewöhnlichen oder fehlenden Werten nützlich ist.
|type|Der abgeleitete Typ der Spalte. Mögliche Werte: Zeichenfolgen, boolesche Werte, Datumsangaben und Dezimalwerte.
|Min| Der Mindestwert der Spalte. Leere Einträge werden für Features angezeigt, deren Typ keine inhärente Reihenfolge aufweist (z. B. boolesche Werte).
|Max| Der Höchstwert der Spalte. 
|Anzahl| Die Gesamtanzahl der fehlenden und nicht fehlenden Einträge in der Spalte.
|Fehlt nicht (Anzahl)| Die Anzahl der nicht fehlenden Einträge in der Spalte. Leere Zeichenfolgen und Fehler werden als Werte behandelt, damit sie nicht zur „Anzahl nicht fehlender“ beitragen.
|Quantile| Die geschätzten Werte in jedem Quantil, um einen Eindruck von der Verteilung der Daten bereitzustellen.
|Mittelwert| Das arithmetische Mittel der Spalte.
|Standardabweichung| Das Maß der Verteilung oder Abweichung der Daten dieser Spalte.
|Variance| Das Maß, wie weit die Daten dieser Spalte gegenüber dem Durchschnittswert verteilt sind. 
|Schiefe| Das Maß für die Unterschiede zwischen den Daten dieser Spalte und der normalen Verteilung.
|Kurtosis| Das Maß für die schweren Ränder der Daten dieser Spalte im Vergleich mit der normalen Verteilung.

## <a name="storage-access-and-permissions"></a>Speicherzugriff und Berechtigungen

Um sicherzustellen, dass eine sichere Verbindung mit Ihrem Azure-Speicherdienst hergestellt wird, erfordert Azure Machine Learning, dass Sie über die Berechtigung zum Zugreifen auf den entsprechenden Datenspeicher verfügen. Dieser Zugriff ist von den Anmeldeinformationen für die Authentifizierung abhängig, die zum Registrieren des Datenspeichers verwendet werden.

### <a name="virtual-network"></a>Virtuelles Netzwerk

Wenn sich Ihr Datenspeicherkonto in einem **virtuellen Netzwerk** befindet, sind zusätzliche Konfigurationsschritte erforderlich, um sicherzustellen, dass Azure Machine Learning auf Ihre Daten zugreifen kann. Stellen Sie wie unter [Netzwerkisolation und Datenschutz](how-to-enable-studio-virtual-network.md) beschrieben sicher, dass die erforderlichen Konfigurationsschritte ausgeführt werden, wenn Sie Ihren Datenspeicher erstellen und registrieren.  

### <a name="access-validation"></a>Zugriffsüberprüfung

**Im Rahmen des ersten Erstellungs- und Registrierungsvorgangs des Datenspeichers** überprüft Azure Machine Learning automatisch, ob der zugrunde liegende Speicherdienst vorhanden ist und der vom Benutzer bereitgestellte Prinzipal (Benutzername, Dienstprinzipal oder SAS-Token) Zugriff auf den angegebenen Speicher besitzt.

**Nach dem Erstellen des Datenspeichers** wird diese Überprüfung nur noch für Methoden ausgeführt, die Zugriff auf den zugrunde liegenden Speichercontainer benötigen, und **nicht** bei jedem Abruf von Datenspeicherobjekten. Beispielsweise erfolgt eine Überprüfung, wenn Sie Dateien aus Ihrem Datenspeicher herunterladen möchten. Wenn Sie jedoch nur den Standarddatenspeicher ändern möchten, findet keine Überprüfung statt.

Um Ihren Zugriff auf den zugrunde liegenden Speicherdienst zu authentifizieren, können Sie je nach zu erstellendem Datenspeichertyp entweder Ihren Kontoschlüssel, SAS-Token (SAS = Shared Access Signatures) oder einen Dienstprinzipal bereitstellen. In der [Speichertypmatrix](how-to-access-data.md#matrix) werden die unterstützten Authentifizierungstypen aufgeführt, die den einzelnen Datenspeichertypen entsprechen.

Informationen zu Kontoschlüssel, SAS-Token und Dienstprinzipal finden Sie im [Azure-Portal](https://portal.azure.com).

* Wenn Sie beabsichtigen, einen Kontoschlüssel oder ein SAS-Token für die Authentifizierung zu verwenden, wählen Sie im linken Bereich **Speicherkonten** und dann das Speicherkonto aus, das Sie registrieren möchten.
  * Die Seite **Übersicht** enthält Informationen wie den Kontonamen und den Namen des Containers oder der Dateifreigabe.
      1. Wechseln Sie für Kontoschlüssel zu **Kontoschlüssel** im Bereich **Einstellungen**.
      1. Für SAS-Token wechseln Sie zu **Shared Access Signatures** im Bereich **Einstellungen**.

* Wenn Sie einen [Dienstprinzipal](../active-directory/develop/howto-create-service-principal-portal.md) für die Authentifizierung verwenden möchten, navigieren Sie zu Ihren **App-Registrierungen**, und wählen Sie die gewünschte App aus.
    * Auf der entsprechenden Übersichtsseite werden erforderliche Informationen wie Mandanten-ID und Client-ID angezeigt.

> [!IMPORTANT]
> * Wenn Sie Ihre Zugriffsschlüssel für ein Azure Storage-Konto (Kontoschlüssel oder SAS-Token) ändern müssen, stellen Sie sicher, dass die neuen Anmeldeinformationen mit Ihrem Arbeitsbereich und den damit verbundenen Datenspeichern synchronisiert werden. Eine Anleitung zum Synchronisieren Ihrer aktualisierten Anmeldeinformationen finden Sie [hier](how-to-change-storage-access-key.md). <br> <br>
> * Wenn Sie die Registrierung aufheben und einen Datenspeicher mit dem gleichen Namen erneut registrieren und dabei ein Fehler auftritt, ist bei dem Azure Key Vault für Ihren Arbeitsbereich vorläufiges Löschen möglicherweise nicht aktiviert. Standardmäßig ist vorläufiges Löschen für die Key Vault-Instanz aktiviert, die von Ihrem Arbeitsbereich erstellt wurde. Es ist jedoch möglicherweise nicht aktiviert, wenn Sie einen vorhandenen Schlüsseltresor verwendet haben oder einen Arbeitsbereich vor Oktober 2020 erstellt haben. Informationen zum Aktivieren des vorläufigen Löschens finden Sie unter [Aktivieren des vorläufigen Löschens für einen vorhandenen Schlüsseltresor]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault).

### <a name="permissions"></a>Berechtigungen

Stellen Sie für Azure-Blobcontainer und Azure Data Lake Gen2-Speicher sicher, dass Ihre Anmeldeinformationen für die Authentifizierung über den Zugriff **Storage-Blobdatenleser** verfügen. Erfahren Sie mehr über [Storage-Blobdatenleser](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Ein Konto-SAS-Token besitzt standardmäßig keine Berechtigungen. 
* Für den **Lesezugriff** auf Daten müssen Ihre Anmeldeinformationen für die Authentifizierung mindestens die Berechtigungen zum Auflisten und Lesen für Container und Objekte besitzen. 

* Für den **Schreibzugriff** auf Daten sind auch Berechtigungen zum Schreiben und Hinzufügen erforderlich.

## <a name="train-with-datasets"></a>Trainieren mit Datasets

Verwenden Sie Ihre Datasets in Ihren Machine Learning-Experimenten zum Trainieren von ML-Modellen. [Erfahren Sie mehr über das Trainieren mit Datasets](how-to-train-with-datasets.md)

## <a name="next-steps"></a>Nächste Schritte

* [Schrittweises Beispiel für das Training mit TabularDatasets und automatisiertem Machine Learning](tutorial-first-experiment-automated-ml.md).

* [Trainieren eines Modells](how-to-set-up-training-targets.md).

* Weitere Beispiele zum Trainieren von Datasets finden Sie in den [Beispielnotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).