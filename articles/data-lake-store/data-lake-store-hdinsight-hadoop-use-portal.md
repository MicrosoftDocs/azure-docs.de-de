---
title: Erstellen von Azure HDInsight-Clustern mit Data Lake Storage Gen1 – Portal
description: Verwenden des Azure-Portals zum Erstellen und Verwenden von HDInsight-Clustern mit Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 666b39e2a600fe6ca004798ed4f8371cdd1dfe5f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340253"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Erstellen von HDInsight-Clustern mit Azure Data Lake Storage Gen1 mithilfe des Azure-Portals

> [!div class="op_single_selector"]
> * [Verwenden des Azure-Portals](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Verwenden von PowerShell (für Standardspeicher)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Verwenden von PowerShell (für zusätzlichen Speicher)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Verwenden des Ressourcen-Managers](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Hier erfahren Sie, wie Sie im Azure-Portal einen HDInsight-Cluster mit Azure Data Lake Storage Gen1 als Standardspeicher oder zusätzlichen Speicher erstellen. Obwohl zusätzlicher Speicher für einen HDInsight-Cluster optional ist, wird empfohlen, dass Sie Ihre Geschäftsdaten in den zusätzlichen Speicherkonten speichern.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, sorgen Sie dafür, dass Sie die folgenden Anforderungen erfüllt haben:

* **Ein Azure-Abonnement**. Navigieren Sie zu [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Ein Azure Data Lake Storage Gen1-Konto**. Führen Sie die Schritte in der Anleitung unter [Erste Schritte mit Azure Data Lake Storage Gen1 mithilfe des Azure-Portals](data-lake-store-get-started-portal.md) aus. Sie müssen auch einen Stammordner für das Konto erstellen.  In diesem Artikel wird der Stammordner __/clusters__ verwendet.
* **Azure Active Directory-Dienstprinzipal**. Diese Schrittanleitung enthält Anweisungen zum Erstellen eines Dienstprinzipals in Azure Active Directory (Azure AD). Sie müssen jedoch Azure AD-Administrator sein, um einen Dienstprinzipal erstellen zu können. Wenn Sie ein Administrator sind, können Sie diese Voraussetzung überspringen und den Vorgang fortsetzen.

>[!NOTE]
>Sie können einen Dienstprinzipal nur dann erstellen, wenn Sie ein Azure AD-Administrator sind. Ihr Azure AD-Administrator muss zunächst einen Dienstprinzipal erstellen, bevor Sie einen HDInsight-Cluster mit Data Lake Storage Gen1 erstellen können. Zudem muss der Dienstprinzipal mit einem Zertifikat erstellt werden, wie unter [Create a service principal with certificate (Erstellen eines Dienstprinzipals mit einem Zertifikat)](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) beschrieben.
>

## <a name="create-an-hdinsight-cluster"></a>Erstellen eines HDInsight-Clusters

In diesem Abschnitt erstellen Sie einen HDInsight-Cluster mit Data Lake Storage Gen1 als Standardspeicher oder zusätzlichen Speicher. Dieser Artikel befasst sich nur mit dem Konfigurieren von Data Lake Storage Gen1. Allgemeine Informationen zu Clustererstellung und Verfahren finden Sie unter [Erstellen von Hadoop-Clustern in HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Erstellen eines Clusters mit Data Lake Storage Gen1 als Standardspeicher

So erstellen Sie einen HDInsight-Cluster mit Data Lake Storage Gen1 als Standardspeicherkonto:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Befolgen Sie die allgemeinen Informationen zum Erstellen von HDInsight-Clustern unter [Erstellen von Clustern](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters).
3. Wählen Sie auf dem Blatt **Speicher** unter **Primärer Speichertyp** die Option **Azure Data Lake Storage Gen1** aus, und geben Sie dann die folgenden Informationen ein:

    ![HDInsight-Bereich „Speicherkontoeinstellungen“](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png)

    * **Data Lake Store-Konto wählen**: Wählen Sie ein vorhandenes Data Lake Storage Gen1-Konto aus. Ein vorhandenes Data Lake Storage Gen1-Konto ist erforderlich.  Siehe [Voraussetzungen](#prerequisites).
    * **Stammpfad**: Geben Sie einen Pfad ein, in dem die clusterspezifischen Dateien gespeichert werden sollen. Im Screenshot lautet der Pfad __/clusters/myhdiadlcluster/__ , in dem der Ordner __/clusters__ vorhanden sein muss. Der Ordner *myhdicluster* wird vom Portal erstellt.  *myhdicluster* ist der Clustername.
    * **Data Lake Store-Zugriff**: Konfigurieren Sie den Zugriff zwischen dem Data Lake Storage Gen1-Konto und dem HDInsight-Cluster. Eine Anleitung finden Sie unter [Konfigurieren des Data Lake Storage Gen1-Zugriffs](#configure-data-lake-storage-gen1-access).
    * **Zusätzliche Speicherkonten**: Fügen Sie Azure-Speicherkonten als zusätzliche Speicherkonten für den Cluster hinzu. Das Hinzufügen zusätzlicher Data Lake Storage Gen1-Konten erfolgt, indem dem Cluster die Berechtigungen für Daten in weiteren Data Lake Storage Gen1-Konten erteilt werden, während ein Data Lake Storage Gen1-Konto als primärer Speichertyp konfiguriert wird. Siehe [Konfigurieren des Data Lake Storage Gen1-Zugriffs](#configure-data-lake-storage-gen1-access).

4. Klicken Sie auf dem Blatt **Data Lake Store-Zugriff** auf **Auswählen**, und fahren Sie mit der Clustererstellung wie unter [Erstellen von Hadoop-Clustern in HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md) beschrieben fort.

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Erstellen eines Clusters mit Data Lake Storage Gen1 als zusätzlichem Speicher

Befolgen Sie die folgenden Anweisungen, um einen HDInsight-Cluster mit einem Azure Blob Storage-Konto als Standardspeicher und einem Speicherkonto mit Data Lake Storage Gen1 als zusätzlichem Speicher zu erstellen.

So erstellen Sie einen HDInsight-Cluster mit Data Lake Storage Gen1 als zusätzliches Speicherkonto:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Befolgen Sie die allgemeinen Informationen zum Erstellen von HDInsight-Clustern unter [Erstellen von Clustern](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters).
3. Wählen Sie auf dem Blatt **Speicher** unter **Primärer Speichertyp** die Option **Azure Storage** aus, und geben Sie dann die folgenden Informationen ein:

    ![HDInsight-Bereich „Speicherkontoeinstellungen“ für zusätzlichen Speicher](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png)

    * **Auswahlmethode** – Um ein Speicherkonto anzugeben, das Teil Ihres Azure-Abonnements ist, wählen Sie **Meine Abonnements** und dann das betreffende Speicherkonto aus. Um ein Speicherkonto anzugeben, das außerhalb Ihres Azure-Abonnements liegt, wählen Sie **Zugriffsschlüssel** aus, und geben Sie dann die Informationen des externen Speicherkontos an.

    * **Standardcontainer** – Verwenden Sie entweder den Standardwert, oder geben Sie einen eigenen Namen an.
    * **Zusätzliche Speicherkonten** – Fügen Sie weitere Azure-Speicherkonten als zusätzlichen Speicher hinzu.
    * **Data Lake Store-Zugriff** – Konfigurieren Sie den Zugriff zwischen dem Data Lake Storage Gen1-Konto und dem HDInsight-Cluster. Eine Anleitung finden Sie unter [Konfigurieren des Data Lake Storage Gen1-Zugriffs](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurieren des Data Lake Storage Gen1-Zugriffs

In diesem Abschnitt konfigurieren Sie den Data Lake Storage Gen1-Zugriff aus HDInsight-Clustern mithilfe eines Azure Active Directory-Dienstprinzipals.

### <a name="specify-a-service-principal"></a>Angeben eines Dienstprinzipals

Im Azure-Portal können Sie einen vorhandenen Dienstprinzipal verwenden oder einen neuen erstellen.

So erstellen Sie einen Dienstprinzipal im Azure-Portal:
1. Informationen zur Verwendung von Azure Active Directory finden Sie unter [Erstellen von Dienstprinzipalen und Zertifikaten](../active-directory/develop/howto-create-service-principal-portal.md).

So verwenden Sie einen im Azure-Portal vorhandenen Dienstprinzipal:

1. Der Dienstprinzipal sollte Besitzerberechtigungen für das Speicherkonto besitzen. Weitere Informationen finden Sie unter [Einrichten von Berechtigungen für den Dienstprinzipal als Besitzer des Speicherkontos](#configure-serviceprincipal-permissions).
1. Wählen Sie **Data Lake Store-Zugriff** aus.
1. Wählen Sie auf dem Blatt **Data Lake Storage Gen1-Zugriff** die Option **Vorhandenen verwenden** aus.
1. Wählen Sie **Dienstprinzipal** und dann den vorhandenen Dienstprinzipal aus.
1. Laden Sie das Zertifikat (PFX-Datei) hoch, das dem ausgewählten Dienstprinzipal zugeordnet ist, und geben Sie dann das Zertifikatkennwort ein.

    ![Dienstprinzipal für HDInsight-Cluster hinzufügen](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png)

1. Wählen Sie **Zugriff** aus, um den Zugriff auf den Ordner zu konfigurieren.  Siehe [Konfigurieren von Dateiberechtigungen](#configure-file-permissions).

### <a name="set-up-permissions-for-the-service-principal-to-be-owner-on-the-storage-account"></a><a name="configure-serviceprincipal-permissions"></a>Einrichten von Berechtigungen für den Dienstprinzipal als Besitzer des Speicherkontos
1. Klicken Sie auf dem Blatt „Zugriffssteuerung (IAM)“ des Speicherkontos auf „Rollenzuweisung hinzufügen“. 
2. Wählen Sie auf dem Blatt „Rollenzuweisung hinzufügen“ für die Rolle die Option „Besitzer“ und dann den SPN aus, und klicken Sie auf „Speichern“.

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Konfigurieren von Dateiberechtigungen

Die Konfiguration variiert je nachdem, ob das Konto als Standardspeicher oder zusätzliches Speicherkonto verwendet wird:

* Als Standardspeicher

  * Berechtigung auf Stammebene des Data Lake Storage Gen1-Kontos
  * Berechtigung auf Stammebene des HDInsight-Cluster-Speichers Beispiel: Der zuvor in diesem Tutorial verwendete Ordner __/clusters__.

* Als zusätzlicher Speicher

  * Berechtigung für die Ordner, in denen Sie Dateizugriff benötigen.

So weisen Sie die Berechtigung auf Stammebene des Speicherkontos mit Data Lake Storage Gen1 zu:

1. Wählen Sie auf dem Blatt **Data Lake Storage Gen1-Zugriff** die Option **Zugriff** aus. Das Blatt **Dateiberechtigungen auswählen** ist standardmäßig geöffnet. Hier werden alle Speicherkonten in Ihrem Abonnement aufgelistet.
1. Zeigen Sie mit der Maus (ohne zu klicken) auf den Namen des Kontos mit Data Lake Storage Gen1, um das Kontrollkästchen sichtbar zu machen. Aktivieren Sie dann das Kontrollkästchen.

    ![„Dateiberechtigungen auswählen“](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png)

   __LESEN__, __SCHREIBEN__ und __AUSFÜHREN__ sind standardmäßig ausgewählt.

1. Klicken Sie unten auf der Seite auf **Auswählen**.
1. Wählen Sie **Ausführen** aus, um die Berechtigung zuzuweisen.
1. Wählen Sie **Fertig** aus.

So weisen Sie die Berechtigung auf Stammebene des HDInsight-Clusters zu:

1. Wählen Sie auf dem Blatt **Data Lake Storage Gen1-Zugriff** die Option **Zugriff** aus. Das Blatt **Dateiberechtigungen auswählen** ist standardmäßig geöffnet. Hier werden alle Speicherkonten mit Data Lake Storage Gen1 in Ihrem Abonnement aufgelistet.
1. Wählen Sie auf dem Blatt **Dateiberechtigungen auswählen** den Namen des Speicherkontos mit Data Lake Storage Gen1 aus, um dessen Inhalt anzuzeigen.
1. Wählen Sie den Stammordner des HDInsight-Clusterspeichers aus, indem Sie das Kontrollkästchen auf der linken Seite des Ordners aktivieren. Entsprechend dem vorherigen Screenshot ist der Ordner __/clusters__, den Sie beim Auswählen von Data Lake Storage Gen1 als Standardspeicher angegeben haben, der Stammordner des Clusterspeichers.
1. Legen Sie die Berechtigungen für den Ordner fest.  LESEN, SCHREIBEN und AUSFÜHREN sind standardmäßig ausgewählt.
1. Klicken Sie unten auf der Seite auf **Auswählen**.
1. Klicken Sie auf **Run** (Ausführen).
1. Wählen Sie **Fertig** aus.

Wenn Sie Data Lake Storage Gen1 als zusätzlichen Speicher verwenden, müssen Sie die Berechtigung nur für die Ordner zuweisen, auf die Sie aus dem HDInsight-Cluster zugreifen möchten. Im folgenden Screenshot wird z. B. nur Zugriff auf den Ordner **mynewfolder** in einem Speicherkonto mit Data Lake Storage Gen1 gewährt.

![Zuweisen von Dienstprinzipalberechtigungen zum HDInsight-Cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png)

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Überprüfen der Clustereinrichtung

Nachdem die Einrichtung des Clusters abgeschlossen ist, überprüfen Sie auf dem Blatt „Cluster“ Ihre Ergebnisse, indem Sie einen oder beide der folgenden Schritte ausführen:

* Wenn Sie sicherstellen möchten, dass das von Ihnen angegebene Konto mit Data Lake Storage Gen1 der zugeordnete Speicher für den Cluster ist, klicken Sie im linken Bereich auf **Speicherkonten**.

    ![Überprüfen des zugeordneten Speichers](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png)

* Wenn Sie sicherstellen möchten, dass der Dienstprinzipal dem HDInsight-Cluster ordnungsgemäß zugeordnet wurde, wählen Sie im linken Bereich **Data Lake Storage Gen1-Zugriff** aus.

    ![Überprüfen des Dienstprinzipals](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png)

## <a name="examples"></a>Beispiele

Nachdem Sie den Cluster mit Data Lake Storage Gen1 als Ihren Speicher eingerichtet haben, sehen Sie sich diese Beispiele an, um zu erfahren, wie Sie mithilfe von HDInsight-Cluster die Daten analysieren können, die in Data Lake Storage Gen1 gespeichert sind.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-primary-storage"></a>Ausführen einer Hive-Abfrage für Daten in einer Data Lake Storage Gen1-Instanz (primärer Speicher)

Verwenden Sie zum Ausführen einer Hive-Abfrage die Hive-Ansichten-Schnittstelle im Ambari-Portal. Anweisungen zum Verwenden der Hive-Ansichten mit Ambari finden Sie unter [Verwenden der Hive-Ansicht mit Hadoop in HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Wenn Sie mit Daten in einer Data Lake Storage Gen1-Instanz arbeiten, müssen einige Zeichenfolgen geändert werden.

Wenn Sie beispielsweise den Cluster verwenden, den Sie mit Data Lake Storage Gen1 als primären Speicher erstellt haben, ist der Datenpfad folgender: *adl://<data_lake_storage_gen1__account_name>/azuredatalakestore.net/path/to/file*. Eine Hive-Abfrage zum Erstellen einer Tabelle aus Beispieldaten, die in der Data Lake Storage Gen1-Instanz gespeichert werden, sieht wie die folgende Anweisung aus:

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

Beschreibungen:

* `adl://hdiadlsg1storage.azuredatalakestore.net/` ist der Stamm des Kontos mit Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster` ist der Stamm der Clusterdaten, die Sie beim Erstellen des Clusters angegeben haben.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` ist der Speicherort der Beispieldatei, die Sie in der Abfrage verwenden.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-additional-storage"></a>Ausführen einer Hive-Abfrage für Daten in einer Data Lake Storage Gen1-Instanz (zusätzlicher Speicher)

Wenn der von Ihnen erstellte Cluster Azure Blob Storage als Standardspeicher verwendet, befinden sich die Beispieldaten nicht im Speicherkonto mit Data Lake Storage Gen1, das als zusätzlicher Speicher verwendet wird. Übertragen Sie in einem derartigen Fall die Daten zunächst aus Azure Blob Storage in das Speicherkonto mit Data Lake Storage Gen1, und führen Sie dann die Abfragen wie im Beispiel oben aus.

Informationen zum Kopieren von Daten aus Azure Blob Storage in ein Speicherkonto mit Data Lake Storage Gen1 finden Sie in den folgenden Artikeln:

* [Kopieren von Daten zwischen Azure Storage-Blobs und Azure Data Lake Storage Gen1 mit DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Kopieren von Daten aus Azure Storage-Blobs in Azure Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Verwenden von Data Lake Storage Gen1 mit einem Spark-Cluster

Sie können einen Spark-Cluster zum Ausführen von Spark-Aufträgen für Daten verwenden, die in einer Data Lake Storage Gen1-Instanz gespeichert sind. Weitere Informationen finden Sie unter [Verwenden des HDInsight Spark-Clusters, um Daten in Data Lake Storage Gen1 zu analysieren](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Verwenden von Data Lake Storage Gen1 in einer Storm-Topologie

Sie können das Speicherkonto mit Data Lake Storage Gen1 verwenden, um dort Daten aus einer Storm-Topologie zu schreiben. Informationen zum Umsetzen dieses Szenarios finden Sie unter [Verwenden von Azure Data Lake Storage Gen1 mit Apache Storm und HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Weitere Informationen

* [Verwenden von Data Lake Storage Gen1 mit Azure HDInsight-Clustern](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [PowerShell: Erstellen eines HDInsight-Clusters für die Verwendung von Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: /windows-hardware/drivers/devtest/pvk2pfx