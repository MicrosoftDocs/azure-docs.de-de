---
title: 'Apache HBase und Enterprise-Sicherheitspaket: Azure HDInsight'
description: 'Tutorial: Erfahren Sie, wie Sie Apache Ranger-Richtlinien für HBase in Azure HDInsight mit dem Enterprise-Sicherheitspaket konfigurieren.'
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 5747de399e7ae0cfe99ba013f8da376be0ba1b2a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993634"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>Tutorial: Konfigurieren von Apache HBase-Richtlinien in HDInsight mit dem Enterprise-Sicherheitspaket

Hier erfahren Sie, wie Sie Apache Ranger-Richtlinien für Apache HBase-Cluster mit dem Enterprise-Sicherheitspaket (ESP) konfigurieren. Es werden Verbindungen von ESP-Clustern mit einer Domäne hergestellt, sodass Benutzer sich mit Anmeldeinformationen für die Domäne authentifizieren können. In diesem Tutorial erstellen Sie zwei Ranger-Richtlinien, um den Zugriff auf verschiedene Spaltenfamilien in einer HBase-Tabelle einzuschränken.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von Domänenbenutzern
> * Erstellen von Ranger-Richtlinien
> * Erstellen von Tabellen in einem HBase-Cluster
> * Testen von Ranger-Richtlinien

## <a name="before-you-begin"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.

* Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

* Erstellen Sie einen [HDInsight-HBase-Cluster mit dem Enterprise-Sicherheitspaket](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Herstellen einer Verbindung mit der Apache Ranger-Administratoroberfläche

1. Stellen Sie in einem Browser mit der URL `https://<ClusterName>.azurehdinsight.net/Ranger/` eine Verbindung mit der Ranger-Administrator-Benutzeroberfläche her. Vergessen Sie nicht, `<ClusterName>` in den Namen Ihres HBase-Clusters zu ändern.

    > [!NOTE]  
    > Ranger-Anmeldeinformationen sind nicht identisch mit Hadoop-Clusteranmeldeinformationen. Stellen Sie die Verbindung mit der Ranger-Administratoroberfläche in einem neuen InPrivate-Browserfenster her, um zu verhindern, dass der Browser zwischengespeicherte Hadoop-Anmeldeinformationen verwendet.

2. Melden Sie sich mit Ihren Administratoranmeldeinformationen für Azure Active Directory (AD) an. Die Azure AD-Administratoranmeldeinformationen sind nicht identisch mit HDInsight-Clusteranmeldeinformationen oder Linux-HDInsight-Knoten-SSH-Anmeldeinformationen.

## <a name="create-domain-users"></a>Erstellen von Domänenbenutzern

Informationen zum Erstellen der Domänenbenutzer **sales_user1** und **marketing_user1** finden Sie unter [Konfigurieren eines HDInsight-Clusters mit Enterprise-Sicherheitspaket (Enterprise Security Package, ESP) mithilfe von Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md). In einem Produktionsszenario stammen Domänenbenutzer aus Ihrem Active Directory-Mandanten.

## <a name="create-hbase-tables-and-import-sample-data"></a>Erstellen von HBase-Tabellen und Importieren von Beispieldaten

Sie können SSH verwenden, um eine Verbindung mit Apache HBase-Clustern herzustellen, und dann mithilfe von [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) HBase-Tabellen erstellen, Daten einfügen und Daten abfragen. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>So verwenden Sie die HBase-Shell

1. Führen Sie unter SSH den folgenden HBase-Befehl aus:
   
    ```bash
    hbase shell
    ```

2. Erstellen Sie eine HBase-Tabelle namens `Customers` mit zwei Spaltenfamilien: `Name` und `Contact`.

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. Fügen Sie Daten ein:
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. Zeigen Sie den Inhalt der Tabelle an:
    
    ```hbaseshell
    scan 'Customers'
    ```

    ![Ausgabe der HDInsight Hadoop HBase-Shell](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Erstellen von Ranger-Richtlinien

Erstellen Sie eine Ranger-Richtlinie für **sales_user1** und **marketing_user1**.

1. Öffnen Sie die **Ranger-Administratoroberfläche**. Klicken Sie unter **HBase** auf **\<ClusterName>_hbase**.

   ![Apache Ranger-Administratoroberfläche in HDInsight](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. Auf dem Bildschirm **Liste der Richtlinien** werden alle Ranger-Richtlinien angezeigt, die für diesen Cluster erstellt wurden. Möglicherweise ist eine vorkonfigurierte Richtlinie aufgelistet. Klicken Sie auf **Neue Richtlinie hinzufügen**.

    ![Liste der Apache Ranger HBase-Richtlinien](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. Geben Sie auf der Seite **Richtlinie erstellen** die folgenden Werte ein:

   |**Einstellung**  |**Empfohlener Wert**  |
   |---------|---------|
   |Richtlinienname  |  sales_customers_name_contact   |
   |HBase-Tabelle   |  Kunden |
   |HBase-Spaltenfamilie   |  Name, Kontakt |
   |HBase-Spalte   |  * |
   |Gruppe auswählen  | |
   |Benutzer auswählen  | sales_user1 |
   |Berechtigungen  | Lesen |

   Die folgenden Platzhalter können im Themennamen enthalten sein:

   * `*` weist auf null oder mehr Vorkommen von Zeichen hin.
   * `?` weist auf ein einzelnes Zeichen hin.

   ![Apache Ranger-Richtlinienerstellung: „sales“](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Warten Sie kurz, bis Ranger mit AAD synchronisiert ist, wenn unter **Benutzer auswählen** nicht automatisch ein Domänenbenutzer eingetragen wird.

4. Klicken Sie auf **Hinzufügen**, um die Richtlinie zu speichern.

5. Klicken Sie auf **Neue Richtlinie hinzufügen**, und geben Sie die folgenden Werte ein:

   |**Einstellung**  |**Empfohlener Wert**  |
   |---------|---------|
   |Richtlinienname  |  marketing_customers_contact   |
   |HBase-Tabelle   |  Kunden |
   |HBase-Spaltenfamilie   |  Kontakt |
   |HBase-Spalte   |  * |
   |Gruppe auswählen  | |
   |Benutzer auswählen  | marketing_user1 |
   |Berechtigungen  | Lesen |

   ![Apache Ranger-Richtlinienerstellung: „marketing“](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. Klicken Sie auf **Hinzufügen**, um die Richtlinie zu speichern.

## <a name="test-the-ranger-policies"></a>Testen der Ranger-Richtlinien

Basierend auf den konfigurierten Ranger-Richtlinien kann **sales_user1** alle Daten für die Spalten in den Spaltenfamilien `Name` und `Contact` anzeigen. **marketing_user1** kann nur Daten in der Spaltenfamilie `Contact` anzeigen.

### <a name="access-data-as-sales_user1"></a>Zugreifen auf Daten als „sales_user1“

1. Stellen Sie eine neue SSH-Verbindung mit dem Cluster her. Verwenden Sie den folgenden Befehl, um sich bei dem Cluster anzumelden:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Verwenden Sie den kinit-Befehl, um zum Kontext des gewünschten Benutzers zu wechseln:

   ```bash
   kinit sales_user1
   ```

2. Öffnen Sie die HBase-Shell, und sehen Sie sich die Tabelle `Customers` an.

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Beachten Sie, dass der Vertriebsbenutzer (sales) alle Spalten der Tabelle `Customers` anzeigen kann – einschließlich der beiden Spalten in der Spaltenfamilie `Name` und der fünf Spalten in der Spaltenfamilie `Contact`.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketing_user1"></a>Zugreifen auf Daten als „marketing_user1“

1. Stellen Sie eine neue SSH-Verbindung mit dem Cluster her. Führen Sie den folgenden Befehl aus, um sich als **marketing_user1** anzumelden:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Verwenden Sie den kinit-Befehl, um zum Kontext des gewünschten Benutzers zu wechseln:

   ```bash
   kinit marketing_user1
   ```

1. Öffnen Sie die HBase-Shell, und sehen Sie sich die Tabelle `Customers` an:

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

1. Beachten Sie, dass der Marketingbenutzer nur die fünf Spalten der Spaltenfamilie `Contact` anzeigen kann.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

1. Zeigen Sie die Überwachungszugriffsereignisse in der Ranger-Benutzeroberfläche an.

   ![Ranger-Benutzeroberfläche in HDInsight – Richtlinienüberwachung](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht mehr benötigen, gehen Sie wie folgt vor, um den erstellten HBase-Cluster zu löschen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Geben Sie oben im **Suchfeld** den Suchbegriff **HDInsight** ein. 
1. Wählen Sie unter **Dienste** die Option **HDInsight-Cluster** aus.
1. Klicken Sie in der daraufhin angezeigten Liste mit den HDInsight-Clustern neben dem Cluster, den Sie für dieses Tutorial erstellt haben, auf die Auslassungspunkte ( **...** ). 
1. Klicken Sie auf **Löschen**. Klicken Sie auf **Ja**.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit einem Apache HBase-Beispiel in HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)