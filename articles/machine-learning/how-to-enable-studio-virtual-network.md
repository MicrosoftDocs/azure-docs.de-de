---
title: Aktivieren von Azure Machine Learning Studio in einem virtuellen Netzwerk
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Azure Machine Learning Studio konfigurieren, um auf Daten zuzugreifen, die in einem virtuellen Netzwerk gespeichert sind.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: a90b98e8be976da9ee2669ab3b5fed4a890f0fb2
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576615"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Verwenden von Azure Machine Learning Studio in einem virtuellen Netzwerk

In diesem Artikel erfahren Sie, wie Sie Azure Machine Learning Studio in einem virtuellen Netzwerk verwenden. Studio enthält Features wie automatisiertes maschinelles Lernen, den Designer und Datenbeschriftung. Um diese Features in einem virtuellen Netzwerk verwenden zu können, müssen Sie die Schritte in diesem Artikel ausführen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> - Erteilen der Studio-Berechtigung, auf Daten zuzugreifen, die in einem virtuellen Netzwerk gespeichert sind
> - Zugreifen auf Studio von einer Ressource innerhalb eines virtuellen Netzwerks aus
> - Erfahren Sie, wie sich das Studio auf die Speichersicherheit auswirkt.

Der Artikel ist Teil 5 einer fünfteiligen Artikelreihe, in der Sie schrittweise durch die Absicherung eines Azure Machine Learning-Workflows geführt werden. Es wird dringend empfohlen, die vorherigen Teile zum Einrichten einer VNET-Umgebung zu lesen.

Sehen Sie sich auch die anderen Artikel in dieser Reihe an:

[1. Übersicht zu VNETs](how-to-network-security-overview.md) > [2. Schützen des Arbeitsbereichs](how-to-secure-workspace-vnet.md) > [3. Schützen der Trainingsumgebung](how-to-secure-training-vnet.md) > [4. Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md) > **5. Verwenden von Studio in einem virtuellen Netzwerk**


> [!IMPORTANT]
> Wenn sich Ihr Arbeitsbereich in einer __Sovereign Cloud__ befindet, z. B. Azure Government oder Azure China 21Vianet, wird von integrierten Notebooks die Verwendung von Speicher, der sich in einem virtuellen Netzwerk befindet, _nicht_ unterstützt. Stattdessen können Sie Jupyter-Notebooks aus einer Compute-Instanz verwenden. Weitere Informationen finden Sie im Abschnitt [Zugreifen auf Daten auf einem Compute-Instanz-Notebook](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook).


## <a name="prerequisites"></a>Voraussetzungen

+ In der [Übersicht über die Netzwerksicherheit](how-to-network-security-overview.md) finden Sie Informationen zu gängigen Szenarien im Zusammenhang mit virtuellen Netzwerken sowie zur Architektur.

+ Ein bereits vorhandenes virtuelles Netzwerk und Subnetz, das verwendet werden kann

+ Ein vorhandener [Azure Machine Learning-Arbeitsbereich mit aktiviertem Private Link](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Ein vorhandenes [Azure Storage-Konto](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints), das Ihrem virtuellen Netzwerk hinzugefügt wurde.

## <a name="configure-data-access-in-the-studio"></a>Konfigurieren des Datenzugriffs in Studio

Einige der Funktionen von Studio sind in virtuellen Netzwerken standardmäßig deaktiviert. Wenn Sie diese Features aktivieren möchten, müssen Sie für Speicherkonten, die Sie in Studio verwenden möchten, die verwaltete Identität aktivieren. 

Die folgenden Vorgänge sind in einem virtuellen Netzwerk standardmäßig deaktiviert:

* Vorschau der Daten im Studio.
* Visualisieren von Daten im Designer.
* Bereitstellen eines Modells im Designer ([Standardspeicherkonto](#enable-managed-identity-authentication-for-default-storage-accounts))
* Übermitteln eines Experiments für das automatisierte maschinelle Lernen ([Standardspeicherkonto](#enable-managed-identity-authentication-for-default-storage-accounts))
* Starten eines Beschriftungsprojekts.

Das Studio unterstützt das Lesen von Daten aus den folgenden Datenspeichertypen in einem virtuellen Netzwerk:

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL-Datenbank

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Konfigurieren von Datenspeichern für die Verwendung der vom Arbeitsbereich verwalteten Identität

Nachdem Sie Ihrem virtuellen Netzwerk mit einem [Dienstendpunkt](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) oder [privaten Endpunkt](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints) ein Azure Storage-Konto hinzugefügt haben, müssen Sie Ihren Datenspeicher für die Verwendung der Authentifizierung anhand der [verwalteten Identität](../active-directory/managed-identities-azure-resources/overview.md) konfigurieren. Auf diese Weise kann Studio auf Daten in Ihrem Speicherkonto zugreifen.

Azure Machine Learning verwendet [Datenspeicher](concept-data.md#datastores), um eine Verbindung mit Speicherkonten herzustellen. Führen Sie die folgenden Schritte aus, um die Verwendung der verwalteten Identität in einem Datenspeicher zu konfigurieren:

1. Wählen Sie __Datastores__ (Datenspeicher) im Studio aus.

1. Wenn Sie einen vorhandenen Datenspeicher aktualisieren möchten, wählen Sie diesen aus, und wählen Sie __Anmeldeinformationen aktualisieren__.

    Um einen neuen Datenspeicher zu erstellen, wählen Sie __+ New datastore__ (+ Neuer Datenspeicher) aus.

1. Wählen Sie in den Datenspeichereinstellungen für __Use workspace managed identity for data preview and profiling in Azure Machine Learning studio__ (Vom Arbeitsbereich verwaltete Identität für Datenvorschau und Profilerstellung in Azure Machine Learning Studio verwenden) __Ja__ aus.

    ![Screenshot der Aktivierung einer verwalteten Identität für einen Arbeitsbereich](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

Mit diesen Schritten wird die vom Arbeitsbereich verwaltete Identität mithilfe von Azure RBAC dem Speicherdienst als __Leser__ hinzugefügt. Mit __Lesezugriff__ kann der Arbeitsbereich Firewalleinstellungen abrufen und sicherstellen, dass die Daten das virtuelle Netzwerk nicht verlassen. Es kann bis zu 10 Minuten dauern, bis Änderungen wirksam werden.

### <a name="enable-managed-identity-authentication-for-default-storage-accounts"></a>Aktivieren der Authentifizierung mit verwalteten Identitäten für Standardspeicherkonten

Jeder Azure Machine Learning-Arbeitsbereich verfügt über zwei Standardspeicherkonten, die beim Erstellen des Arbeitsbereichs definiert werden. Studio verwendet die Standardspeicherkonten zum Speichern von Experiment- und Modellartefakten, die für bestimmte Funktionen in Studio wichtig sind.

In der folgenden Tabelle wird beschrieben, warum Sie die Authentifizierung mit verwalteten Identitäten für die Standardspeicherkonten Ihres Arbeitsbereichs aktivieren müssen.

|Speicherkonto  | Hinweise  |
|---------|---------|
|Standardblobspeicher für den Arbeitsbereich| Speichert Modellressourcen vom Designer. Sie müssen die Authentifizierung mit verwalteten Identitäten für dieses Speicherkonto aktivieren, um Modelle im Designer bereitzustellen. <br> <br> Sie können eine Designer-Pipeline visualisieren und ausführen, wenn sie nicht den Standarddatenspeicher verwendet, sondern einen, der für die Verwendung der verwalteten Identität konfiguriert wurde. Wenn Sie jedoch versuchen, ein trainiertes Modell ohne aktivierte verwaltete Identität im Standarddatenspeicher bereitzustellen, tritt dabei ein Fehler auf, unabhängig davon, welche anderen Datenspeicher verwendet werden.|
|Standarddateispeicher für den Arbeitsbereich| Speichert Experimentressourcen für automatisiertes maschinelles Lernen. Sie müssen die Authentifizierung mit verwalteten Identitäten für dieses Speicherkonto aktivieren, um Experimente für automatisiertes maschinelles Lernen zu übermitteln. |


![Screenshot der Speicherorte von Standarddatenspeichern](./media/how-to-enable-studio-virtual-network/default-datastores.png)


### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Gewähren von __Lesezugriff__ auf die private Speicherverbindung für die vom Arbeitsbereich verwaltete Identität

Wenn Ihr Azure Storage-Konto einen privaten Endpunkt verwendet, müssen Sie der vom Arbeitsbereich verwalteten Identität **Lesezugriff** auf die private Verbindung erteilen. Weitere Informationen finden Sie unter der integrierten Rolle [Leser](../role-based-access-control/built-in-roles.md#reader). 

Wenn Ihr Speicherkonto einen Dienstendpunkt verwendet, können Sie diesen Schritt überspringen.

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Zugriff auf Studio von einer Ressource innerhalb des virtuellen Netzwerks aus

Wenn Sie über eine Ressource innerhalb eines virtuellen Netzwerks (z. B. eine Computeinstanz oder eine VM) auf das Studio zugreifen, müssen Sie aus dem virtuellen Netzwerk an das Studio ausgehenden Datenverkehr zulassen. 

Wenn Sie z. B. ausgehenden Datenverkehr mit Netzwerksicherheitsgruppen (NSG) einschränken, fügen Sie einem __Diensttag__-Ziel von __AzureFrontDoor.Frontend__ eine Regel hinzu.

## <a name="technical-notes-for-managed-identity"></a>Technische Hinweise zur verwalteten Identität

Die Verwendung der verwalteten Identität für den Zugriff auf Speicherdienste hat Auswirkungen auf Sicherheitsaspekte. In diesem Abschnitt werden die Änderungen für die einzelnen Speicherkontotypen beschrieben. 

Diese Überlegungen gelten nur für den __Typ des Speicherkontos__, auf das Sie zugreifen.

### <a name="azure-blob-storage"></a>Azure Blob Storage

Für __Azure Blob Storage__ wird die vom Arbeitsbereich verwaltete Identität auch als [Blob Data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) (Blobdatenleser) hinzugefügt, sodass Daten aus Blobspeicher gelesen werden können.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Zugriffssteuerung von Azure Data Lake Storage Gen2

Sie können den Datenzugriff innerhalb eines virtuellen Netzwerks mit Azure RBAC- und POSIX-Zugriffssteuerungslisten (Access Control Lists, ACLs) steuern.

Fügen Sie die vom Arbeitsbereich verwaltete Identität der Rolle [Blobdatenleser](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) hinzu, um Azure RBAC zu verwenden. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung (RBAC)](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Um ACLs zu verwenden, kann der verwalteten Identität des Arbeitsbereichs wie jedem anderen Sicherheitsprinzip Zugriff gewährt werden. Weitere Informationen finden Sie unter [Zugriffssteuerungslisten für Dateien und Verzeichnisse](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Zugriffssteuerung von Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 unterstützt nur Zugriffssteuerungslisten im POSIX-Format. Sie können der vom Arbeitsbereich verwalteten Identität wie jedem anderen Sicherheitsprinzip Zugriff auf Ressourcen zuweisen. Weitere Informationen finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>Eigenständiger Benutzer in Azure SQL-Datenbank

Für den Zugriff mithilfe der verwalteten Identität auf Daten, die in einer Azure SQL-Datenbank-Instanz gespeichert sind, müssen Sie einen eigenständigen SQL-Benutzer erstellen, der der verwalteten Identität zugeordnet ist. Weitere Informationen zum Erstellen eines Benutzers von einem externen Anbieter finden Sie unter [Erstellen eigenständiger Benutzer mit Zuordnung zu Azure AD-Identitäten](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Nachdem Sie einen eigenständigen SQL-Benutzer erstellt haben, erteilen Sie mithilfe des [GRANT T-SQL-Befehls](/sql/t-sql/statements/grant-object-permissions-transact-sql) Berechtigungen.

### <a name="azure-machine-learning-designer-intermediate-module-output"></a>Ausgabe des Zwischenmoduls des Azure Machine Learning-Designers

Sie können den Ausgabespeicherort für jedes beliebige Modul im Designer angeben. Damit können Sie zwischengeschaltete Datasets zu Sicherheits-, Protokollierungs- oder Überwachungszwecken an einem separaten Ort speichern. So geben Sie die Ausgabe an

1. Wählen Sie das Modul aus, für das Sie die Ausgabe angeben möchten.
1. Wählen Sie im Bereich der Moduleinstellungen auf der rechten Seite die Option **Ausgabeeinstellungen** aus.
1. Geben Sie den Datenspeicher an, den Sie für die einzelnen Modulausgaben verwenden möchten.
 
Stellen Sie sicher, dass Sie auf die zwischengeschalteten Speicherkonten in Ihrem virtuellen Netzwerk Zugriff haben. Andernfalls verursacht die Pipeline einen Fehler.

Sie sollten auch die [Authentifizierung mit verwalteten Identitäten](#configure-datastores-to-use-workspace-managed-identity) für die zwischengeschalteten Speicherkonten aktivieren, um Ausgabedaten visualisieren zu können.

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel ist ein optionaler Teil einer vierteiligen Serie zu virtuellen Netzwerken. Weitere Informationen zum Schützen eines virtuellen Netzwerks finden Sie in den verbleibenden Artikeln:

* [Teil 1: Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md)
* [Teil 2: Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md)
* [Teil 3: Schützen der Trainingsumgebung](how-to-secure-training-vnet.md)
* [Teil 4: Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)