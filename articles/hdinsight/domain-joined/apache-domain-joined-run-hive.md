---
title: 'Apache Hive-Richtlinien in Apache Ranger: Azure HDInsight'
description: Erfahren Sie, wie Sie Apache Ranger-Richtlinien für Hive in einem Azure HDInsight-Dienst mit dem Enterprise-Sicherheitspaket konfigurieren.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: f2d9c96a616f05c22c8b999fdc6cab2505c27485
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544935"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Konfigurieren von Apache Hive-Richtlinien in HDInsight mit dem Enterprise-Sicherheitspaket

Hier erfahren Sie, wie Sie Apache Ranger-Richtlinien für Apache Hive konfigurieren. In diesem Artikel erstellen Sie zwei Ranger-Richtlinien, um den Zugriff auf die Hive-Beispieltabelle „hivesampletable“ einzuschränken. Die Hive-Beispieltabelle „hivesampletable“ ist in HDInsight-Clustern enthalten. Nachdem Sie die Richtlinien konfiguriert haben, stellen Sie unter Verwendung von Excel und des ODBC-Treibers eine Verbindung mit Hive-Tabellen in HDInsight her.

## <a name="prerequisites"></a>Voraussetzungen

* Ein HDInsight-Cluster mit dem Enterprise-Sicherheitspaket. Weitere Informationen finden Sie unter [Konfigurieren von HDInsight-Clustern mit Enterprise-Sicherheitspaket](./apache-domain-joined-configure-using-azure-adds.md).
* Eine Arbeitsstation mit Microsoft 365 Apps for Enterprise, Office 2016, Office 2013 Professional Plus, Excel 2013 Standalone oder Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Herstellen einer Verbindung mit der Apache Ranger-Administratoroberfläche
**So stellen Sie eine Verbindung mit der Ranger-Administratoroberfläche her**

1. Navigieren Sie in einem Browser zur Administratoroberfläche von Ranger unter `https://CLUSTERNAME.azurehdinsight.net/Ranger/`, wobei CLUSTERNAME dem Namen Ihres Clusters entspricht.

   > [!NOTE]  
   > Ranger verwendet andere Anmeldeinformationen als der Apache Hadoop-Cluster. Stellen Sie die Verbindung mit der Ranger-Administratoroberfläche in einem neuen InPrivate-Browserfenster her, um zu verhindern, dass der Browser zwischengespeicherte Hadoop-Anmeldeinformationen verwendet.

2. Melden Sie sich mit dem Domänenbenutzernamen und dem Kennwort des Clusteradministrators an:

    ![Startseite von HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Ranger kann derzeit nur mit Yarn und Hive verwendet werden.

## <a name="create-domain-users"></a>Erstellen von Domänenbenutzern

Unter [Erstellen eines HDInsight-Clusters mit ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp) finden Sie Informationen zum Erstellen von „hiveruser1“ und „hiveuser2“. Diese beiden Benutzerkonten werden in diesem Artikel verwendet.

## <a name="create-ranger-policies"></a>Erstellen von Ranger-Richtlinien

In diesem Abschnitt erstellen Sie zwei Ranger-Richtlinien für den Zugriff auf „hivesampletable“. Sie erteilen für bestimmte Spaltengruppen die Berechtigung „Auswählen“. Beide Benutzer wurden über [Erstellen eines HDInsight-Clusters mit ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp) erstellt. Im nächsten Abschnitt testen Sie die beiden Richtlinien in Excel.

**So erstellen Sie Ranger-Richtlinien**

1. Öffnen Sie die Ranger-Administratoroberfläche. Weitere Informationen finden Sie unter „Herstellen einer Verbindung mit der Apache Ranger-Administratoroberfläche“.
2. Wählen Sie unter **Hive** die Zeichenfolge **CLUSTERNAME_Hive** aus. Zwei vorkonfigurierte Richtlinien werden angezeigt.
3. Wählen Sie **Neue Richtlinie hinzufügen** aus, und geben Sie die folgenden Werte ein:

    |Eigenschaft |Wert |
    |---|---|
    |Richtlinienname|read-hivesampletable-all|
    |Hive-Datenbank|default|
    |table|hivesampletable|
    |Hive-Spalte|*|
    |Benutzer auswählen|hiveuser1|
    |Berechtigungen|select|

    ![HDInsight ESP Ranger – Konfigurieren von Hive-Richtlinien](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png)erforderlich.

    > [!NOTE]  
    > Wenn unter „Benutzer auswählen“ kein Domänenbenutzer eingetragen wird, warten Sie kurz, bis Ranger mit AAD synchronisiert wurde.

4. Wählen Sie **Hinzufügen** aus, um die Richtlinie zu speichern.

5. Wiederholen Sie die letzten beiden Schritte, um eine weitere Richtlinie mit folgenden Eigenschaften zu erstellen:

    |Eigenschaft |Wert |
    |---|---|
    |Richtlinienname|read-hivesampletable-devicemake|
    |Hive-Datenbank|default|
    |table|hivesampletable|
    |Hive-Spalte|clientid, devicemake|
    |Benutzer auswählen|hiveuser2|
    |Berechtigungen|select|

## <a name="create-hive-odbc-data-source"></a>Erstellen einer Hive ODBC-Datenquelle

Die Anleitung finden Sie unter [Erstellen einer Hive ODBC-Datenquelle](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Eigenschaft  |BESCHREIBUNG |
 | --- | --- |
 | Datenquellenname | Geben Sie einen Namen für die Datenquelle an. |
 | Host | Geben Sie „CLUSTERNAME.azurehdinsight.net“ ein. Beispiel: myHDICluster.azurehdinsight.net |
 | Port | Verwenden Sie **443** . (Dieser Port wurde von 563 in 443 geändert.) |
 | Datenbank | Verwenden Sie **Standard** . |
 | Hive-Servertyp | Wählen Sie **Hive Server 2** aus. |
 | Mechanismus | Wählen Sie **Azure HDInsight Service** aus. |
 | HTTP-Pfad | Lassen Sie dieses Feld leer. |
 | Benutzername | Geben Sie hiveuser1@contoso158.onmicrosoft.com ein. Aktualisieren Sie den Domänennamen, falls er abweicht. |
 | Kennwort | Geben Sie das Kennwort für „hiveuser1“ ein. |

Klicken Sie vor dem Speichern der Datenquelle auf **Testen** .

## <a name="import-data-into-excel-from-hdinsight"></a>Importieren von Daten aus HDInsight in Excel

Im letzten Abschnitt haben Sie zwei Richtlinien konfiguriert.  „hiveuser1“ verfügt über die Auswahlberechtigung für alle Spalten, „hiveuser2“ über die Auswahlberechtigung für zwei Spalten. In diesem Abschnitt nehmen Sie die Identität der beiden Benutzer an, um Daten in Excel zu importieren.

1. Öffnen Sie eine neue oder bereits vorhandene Arbeitsmappe in Excel.

1. Navigieren Sie auf der Registerkarte **Daten** zu **Daten abrufen** > **Aus anderen Quellen** > **Aus ODBC** , um das Fenster **Aus ODBC** zu öffnen.

    ![Öffnen Sie den Datenverbindungs-Assistenten.](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. Wählen Sie in der Dropdownliste den im letzten Abschnitt erstellten Datenquellennamen aus, und wählen Sie anschließend **OK** .

1. Bei der ersten Verwendung wird das Dialogfeld **ODBC-Treiber** geöffnet. Wählen Sie im Menü auf der linken Seite die Option **Windows** . Wählen Sie anschließend **Verbinden** , um das Fenster **Navigator** zu öffnen.

1. Warten Sie, bis sich der Dialog **Datenbank und Tabelle wählen** öffnet. Dies kann einige Zeit dauern.

1. Wählen Sie erst **hivesampletable** und dann **Weiter** aus.

1. Wählen Sie **Fertig stellen** aus.

1. Im Dialog **Daten importieren** können Sie die Abfrage ändern oder spezifizieren. Wählen Sie dazu **Eigenschaften** aus. Dies kann einige Zeit dauern.

1. Wählen Sie die Registerkarte **Definition** aus. Befehlstext:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"`
    ```

   Gemäß den definierten Ranger-Richtlinien verfügt „hiveuser1“ über die Auswahlberechtigung für alle Spalten.  Diese Abfrage kann daher mit den Anmeldeinformationen von „hiveuser1“ verwendet werden, nicht aber mit den Anmeldeinformationen von „hiveuser2“.

1. Wählen Sie **OK** aus, um das Dialogfeld „Verbindungseigenschaften“ zu schließen.

1. Wählen Sie **OK** aus, um das Dialogfeld **Daten importieren** zu schließen.  

1. Geben Sie erneut das Kennwort für „hiveuser1“ ein, und klicken Sie auf **OK** . Es dauert einige Zeit, bis die Daten in Excel importiert werden. Nach Abschluss des Vorgangs werden 11 Datenspalten angezeigt.

So testen Sie die zweite Richtlinie (read-hivesampletable-devicemake), die Sie im vorherigen Abschnitt erstellt haben

1. Fügen Sie in Excel ein neues Arbeitsblatt ein.
2. Führen Sie das letzte Verfahren durch, um die Daten zu importieren.  Geben Sie dabei allerdings anstelle der Anmeldeinformationen von „hiveuser1“ die Anmeldeinformationen von „hiveuser2“ an. Das funktioniert jedoch nicht, da „hiveuser2“ nur zum Anzeigen von zwei Spalten berechtigt ist. Sie erhalten den folgenden Fehler:

    ```output
    [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
    ```

3. Führen Sie das gleiche Datenimportverfahren durch. Verwenden Sie diesmal die Anmeldeinformationen von „hiveuser2“, und ändern Sie auch die select-Anweisung von:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"
    ```

    in:

    ```sql
    SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"
    ```

    Nach Abschluss des Vorgangs wurden zwei Datenspalten importiert.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Konfigurieren eines HDInsight-Clusters mit Enterprise-Sicherheitspaket finden Sie unter [Konfigurieren von HDInsight-Clustern mit Enterprise-Sicherheitspaket](./apache-domain-joined-configure-using-azure-adds.md).
* Informationen zur Verwaltung eines HDInsight-Clusters mit ESP finden Sie unter [Verwalten von HDInsight-Clustern mit ESP](apache-domain-joined-manage.md).
* Informationen zum Ausführen von Hive-Abfragen per SSH für HDInsight-Cluster mit ESP finden Sie unter [Verwenden von SSH mit HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
* Informationen zum Verbinden von Hive unter Verwendung von Hive JDBC finden Sie unter [Herstellen einer Verbindung mit Apache Hive unter Azure HDInsight per Hive-JDBC-Treiber](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
* Informationen zum Verbinden von Excel mit Hadoop unter Verwendung von Hive ODBC finden Sie unter [Verbinden von Excel über den Microsoft Hive ODBC-Treiber mit Apache Hadoop](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* Informationen zum Verbinden von Excel mit Hadoop unter Verwendung von Power Query finden Sie unter [Verbinden von Excel mit Apache Hadoop mithilfe von Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).