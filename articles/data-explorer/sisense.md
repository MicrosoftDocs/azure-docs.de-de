---
title: Visualisieren von Daten über Azure Data Explorer mit Sisense
description: In diesem Artikel wird beschrieben, wie Sie Azure Data Explorer als Datenquelle für Sisense einrichten und die Daten visualisieren.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358006"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Visualisieren von Daten über Azure Data Explorer in Sisense

Sisense ist eine Business Intelligence-Plattform für Analysen, mit der Sie Analyse-Apps entwickeln können, die über Benutzeroberflächen mit hohem Interaktivitätsgrad verfügen. Mit der Business Intelligence- und Dashboardanzeigesoftware können Sie mit wenigen Klicks auf Daten zugreifen und diese kombinieren. Sie können eine Verbindung mit strukturierten und unstrukturierten Datenquellen herstellen, mit minimalem Skript- und Codieraufwand Tabellen aus mehreren Quellen verknüpfen und interaktive Webdashboards und Berichte erstellen. In diesem Artikel erfahren Sie, wie Sie Azure Data Explorer als Datenquelle für Sisense einrichten und Daten aus einem Beispielcluster visualisieren.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um diesen Artikel durchzuarbeiten:

* [Laden Sie die Sisense-App herunter, und installieren Sie sie](https://documentation.sisense.com/latest/getting-started/download-install.htm). 

* Erstellen Sie einen Cluster und die Datenbank, die die StormEvents-Beispieldaten enthält. Weitere Informationen finden Sie unter [Quickstart: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](create-cluster-database-portal.md) und [Erfassen von Beispieldaten in Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Herstellen einer Verbindung mit Sisense-Dashboards per Azure Data Explorer-JDBC-Connector

1. Laden Sie die aktuellen Versionen der folgenden JAR-Dateien herunter, und kopieren Sie sie in das Verzeichnis *..\Sisense\DataConnectors\jdbcdrivers\adx*. 

    * activation-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Öffnen Sie die **Sisense**-App.
1. Wählen Sie die Registerkarte **Data** (Daten) und dann die Option **+ElastiCube**, um ein neues ElastiCube-Modell zu erstellen.
    
    ![Wählen von „ElastiCube“](media/sisense/data-select-elasticube.png)

1. Geben Sie unter **Add new ElastiCube Model** (Neues ElastiCube-Modell hinzufügen) dem ElastiCube-Modell einen Namen, und wählen Sie **Save** (Speichern).
   
    ![Hinzufügen eines neuen ElastiCube-Modells](media/sisense/add-new-elasticube-model.png)

1. Wählen Sie **+ Data** (+ Daten).

    ![Wählen der Schaltfläche „Daten“](media/sisense/select-data.png)

1. Wählen Sie auf der Registerkarte **Select Connector** (Connector auswählen) den Connector **Generic JDBC** (JDBC generisch) aus.

    ![Auswählen des JDBC-Connectors](media/sisense/select-connector.png)

1. Füllen Sie auf der Registerkarte **Connect** (Verbinden) die folgenden Felder für den Connector **Generic JDBC** (JDBC generisch) aus, und wählen Sie **Weiter**.

    ![Einstellungen für JDBC-Connector](media/sisense/jdbc-connector.png)

    |Feld |BESCHREIBUNG |
    |---------|---------|
    |Verbindungszeichenfolge     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC: Ordner „JARs“  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Klassenname des Treibers    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Benutzername   |    AAD-Benutzername     |
    |Kennwort     |   AAD-Benutzerkennwort      |

1. Suchen Sie auf der Registerkarte **Select Data** (Daten auswählen) nach der Option **Select Database** (Datenbank auswählen), um die relevante Datenbank auszuwählen, für die Sie über Berechtigungen verfügen. Wählen Sie in diesem Beispiel die Option *test1*.

    ![Auswählen der Datenbank](media/sisense/select-database.png)

1. Im Bereich *test* (Datenbankname):
    1. Wählen Sie den Tabellennamen aus, um eine Vorschau der Tabelle und die Spaltennamen der Tabelle anzuzeigen. Sie können nicht benötigte Spalten entfernen.
    1. Aktivieren Sie das Kontrollkästchen der relevanten Tabelle, um sie auszuwählen. 
    1. Wählen Sie **Fertig**aus.

    ![Auswählen der Tabelle](media/sisense/select-table-see-columns.png)    

1. Wählen Sie **Build** (Erstellen), um Ihr Dataset zu erstellen. 

    * Wählen Sie im Fenster **Build** (Erstellen) die Option **Build** (Erstellen).

      ![Fenster „Build“ (Erstellen)](media/sisense/build-window.png)

    * Warten Sie, bis der Erstellungsvorgang abgeschlossen ist, und wählen Sie dann die Option **Build Succeeded** (Erstellung erfolgreich).

      ![Erstellung erfolgreich](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Erstellen von Sisense-Dashboards

1. Wählen Sie auf der Registerkarte **Analytics** (Analyse) die Option **+**  > **New Dashboard** (Neues Dashboard), um Dashboards für dieses Dataset zu erstellen.

    ![Neues Dashboard](media/sisense/new-dashboard.png)

1. Wählen Sie ein Dashboard aus, und wählen Sie die Option **Create** (Erstellen). 

    ![Erstellen von Dashboards](media/sisense/create-dashboard.png)

1. Wählen Sie unter **New Widget** (Neues Widget) die Option **+ Select Data** (+ Daten auswählen), um ein neues Widget zu erstellen. 

    ![Hinzufügen von Feldern zum StormEvents-Dashboard](media/sisense/storm-dashboard-add-field.png)  

1. Wählen Sie **+ Add More Data** (+ Weitere Daten hinzufügen), um Ihrem Graphen weitere Spalten hinzuzufügen. 

    ![Hinzufügen von weiteren Daten zum Graphen](media/sisense/add-more-data.png)

1. Wählen Sie **+ Widget**, um ein weiteres Widget zu erstellen. Ordnen Sie die Widgets per Drag & Drop in Ihrem Dashboard neu an.

    ![Storm-Dashboard](media/sisense/final-dashboard.png)

Sie können Ihre Daten jetzt mit Funktionen für die visuelle Analyse untersuchen, zusätzliche Dashboards entwickeln und Daten in verwertbare Erkenntnisse verwandeln, um Ihrem Unternehmen nützliche Informationen zu verschaffen.

## <a name="next-steps"></a>Nächste Schritte

* [Schreiben von Abfragen für den Azure-Daten-Explorer](write-queries.md)

