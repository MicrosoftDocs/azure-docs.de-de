---
title: Verwalten von HDInsight-Clustern mit dem Enterprise-Sicherheitspaket – Azure
description: Informationen zum Verwalten von HDInsight-Clustern mit dem Enterprise-Sicherheitspaket.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: 951bd74c67c77c944a17e41646c4fe49ef46b33f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64725662"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Verwalten von HDInsight-Clustern mit dem Enterprise-Sicherheitspaket
Lernen Sie die Benutzer und Rollen im Enterprise-Sicherheitspaket (Enterprise Security Package, ESP) von HDInsight kennen, und erfahren Sie, wie ESP-Cluster verwaltet werden.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Verknüpfen mit einem in die Domäne eingebundenen Cluster mithilfe von VSCode

Sie können einen normalen Cluster mithilfe eines verwalteten Apache Ambari-Benutzernamens oder einen Apache Hadoop-Sicherheitscluster mithilfe des Domänenbenutzernamens (z.B. user1@contoso.com) verknüpfen.
1. Öffnen Sie die Befehlspalette, indem Sie **STRG+UMSCHALT+P** drücken, und geben Sie dann **HDInsight: Link a Cluster** (HDInsight: Cluster verknüpfen) ein.

   ![Befehl „Cluster verknüpfen“](./media/apache-domain-joined-manage/link-cluster-command.png)

2. Geben Sie die URL des HDInsight-Clusters ein, geben Sie den Benutzername und das Kennwort ein, und wählen Sie den Clustertyp aus. Es werden Erfolgsinformationen angezeigt, wenn die Überprüfung bestanden wurde.
   
   ![Dialogfeld „Cluster verknüpfen“](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]  
   > Der verknüpfte Benutzername und das Kennwort werden verwendet, wenn der Cluster im Azure-Abonnement angemeldet ist und einen Cluster verknüpft hat. 
   
3. Sie können einen verknüpften Cluster mithilfe des Befehls **Cluster auflisten** anzeigen. Jetzt können Sie ein Skript an diesen verknüpften Cluster übermitteln.

   ![Verknüpfter Cluster](./media/apache-domain-joined-manage/linked-cluster.png)

4. Sie können die Verknüpfung eines Clusters auch aufheben, indem Sie in der Befehlspalette **HDInsight: Unlink a Cluster** (HDInsight: Verknüpfung eines Clusters aufheben) eingeben.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Verknüpfen mit einem in die Domäne eingebundenen Cluster mithilfe von IntelliJ

Sie können einen normalen Cluster mithilfe eines verwalteten Ambari-Benutzernamens oder einen Hadoop-Sicherheitscluster mithilfe des Domänenbenutzernamens (z.B. user1@contoso.com) verknüpfen. 
1. Klicken Sie im **Azure-Explorer** auf **Cluster verknüpfen**.

   ![Kontextmenü „Cluster verknüpfen“](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Geben Sie **Clustername**, **Benutzername** und **Kennwort** ein. Überprüfen Sie im Falle eines Authentifizierungsfehlers den Benutzernamen und das Kennwort. Fügen Sie optional das Speicherkonto und den Speicherschlüssel hinzu, und wählen Sie anschließend unter „Speichercontainer“ einen Container aus. Die Speicherinformationen beziehen sich auf den Speicher-Explorer in der linken Struktur.
   
   ![Dialogfeld „Cluster verknüpfen“](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]  
   > Wir verwenden den verknüpften Speicherschlüssel, den Benutzernamen und das Kennwort, wenn der Cluster im Azure-Abonnement angemeldet ist und einen Cluster verknüpft hat.
   > ![Speicher-Explorer in IntelliJ](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

   
3. Ein verknüpfter Cluster wird im Knoten **HDInsight** angezeigt, wenn die eingegebenen Informationen richtig sind. Jetzt können Sie eine Anwendung an diesen verknüpften Cluster übermitteln.

   ![Verknüpfter Cluster](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Sie können die Verknüpfung eines Clusters im **Azure-Explorer** auch aufheben.
   
   ![Cluster mit aufgehobener Verknüpfung](./media/apache-domain-joined-manage/unlink.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Verknüpfen mit einem in die Domäne eingebundenen Cluster mithilfe von Eclipse

Sie können einen normalen Cluster mithilfe eines verwalteten Ambari-Benutzernamens oder einen Hadoop-Sicherheitscluster mithilfe des Domänenbenutzernamens (z.B. user1@contoso.com) verknüpfen.
1. Klicken Sie im **Azure-Explorer** auf **Cluster verknüpfen**.

   ![Kontextmenü „Cluster verknüpfen“](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Geben Sie **Clustername**, **Benutzername** und **Kennwort** ein, und klicken Sie anschließend auf „OK“, um den Cluster zu verknüpfen. Geben Sie optional das Speicherkonto und den Speicherschlüssel ein, und wählen Sie anschließend den Speichercontainer aus, damit der Speicher-Explorer in der linken Strukturansicht funktioniert.
   
   ![Dialogfeld „Cluster verknüpfen“](./media/apache-domain-joined-manage/link-cluster-dialog.png)
   
   > [!NOTE]  
   > Wir verwenden den verknüpften Speicherschlüssel, den Benutzernamen und das Kennwort, wenn der Cluster im Azure-Abonnement angemeldet ist und einen Cluster verknüpft hat.
   > ![Speicher-Explorer in Eclipse](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Ein verknüpfter Cluster wird im Knoten **HDInsight** angezeigt, nachdem Sie auf die Schaltfläche „OK“ geklickt haben, wenn die eingegebenen Informationen richtig sind. Jetzt können Sie eine Anwendung an diesen verknüpften Cluster übermitteln.

   ![Verknüpfter Cluster](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Sie können die Verknüpfung eines Clusters im **Azure-Explorer** auch aufheben.
   
   ![Cluster mit aufgehobener Verknüpfung](./media/apache-domain-joined-manage/unlink.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Greifen Sie mit dem Enterprise Security Package auf die Cluster zu.

Das Enterprise Security Package (bisher als HDInsight Premium bezeichnet) ermöglicht den Zugriff auf den Cluster durch mehrere Benutzer, wobei die Authentifizierung mit Active Directory und die Autorisierung mit Apache Ranger und Storage-ACLs (ADLS-ACLs) durchgeführt wird. Mit der Autorisierung wird für sichere Grenzen zwischen mehreren Benutzern gesorgt, und nur berechtigte Benutzer haben basierend auf den Autorisierungsrichtlinien Zugriff auf die Daten.

Die Sicherheit und die Benutzerisolation sind für einen HDInsight-Cluster mit Enterprise Security Package wichtig. Zur Erfüllung dieser Anforderungen ist der SSH-Zugriff auf den Cluster mit Enterprise Security Package blockiert. In der folgenden Tabelle sind die empfohlenen Zugriffsmethoden für die einzelnen Clustertypen aufgeführt:

|Workload|Szenario|Zugriffsmethode|
|--------|--------|-------------|
|Apache Hadoop|Hive – Interaktive Aufträge/Abfragen  |<ul><li>[Beeline](#beeline)</li><li>[Struktur anzeigen](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interaktive Aufträge/Abfragen, PySpark interaktiv|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin mit Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Struktur anzeigen](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Batchszenarien – Spark-Submit, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interaktive Abfrage (LLAP)|Interactive|<ul><li>[Beeline](#beeline)</li><li>[Struktur anzeigen](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Beliebig|Installieren der benutzerdefinierten Anwendung|<ul><li>[Skriptaktionen](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter ist nicht installiert/wird in Enterprise-Sicherheitspaket nicht unterstützt.

Aus Sicht der Sicherheit ist die Verwendung der Standard-APIs hilfreich. Außerdem kommen Sie in den Genuss der folgenden Vorteile:

1.  **Verwaltung**: Sie können Ihren Code verwalten und Aufträge automatisieren, indem Sie Standard-APIs nutzen, z.B. Livy, HS2 usw.
2.  **Überwachung**: Mit SSH kann nicht überwacht werden, welche Benutzer per SSH auf den Cluster zugegriffen haben. Dies ist nicht der Fall, wenn Aufträge über Standardendpunkte erstellt werden, da sie dann im Kontext des Benutzers ausgeführt werden. 



### <a name="beeline"></a>Verwenden von Beeline 
Installieren Sie Beeline auf Ihrem Computer, und stellen Sie eine Verbindung über das öffentliche Internet her, indem Sie die folgenden Parameter verwenden: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Wenn Sie Beeline lokal installiert haben und eine Verbindung über ein virtuelles Azure-Netzwerk herstellen, verwenden Sie die folgenden Parameter: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Verwenden Sie die Informationen im Dokument „Verwalten von HDInsight mithilfe der Ambari-REST-API“, um den vollqualifizierten Domänennamen eines Hauptknotens zu ermitteln.














## <a name="users-of-hdinsight-clusters-with-esp"></a>Benutzer von HDInsight-Clustern mit Enterprise-Sicherheitspaket
Ein HDInsight-Cluster ohne Enterprise-Sicherheitspaket verfügt über zwei Benutzerkonten, die während der Clustererstellung erstellt werden:

* **Ambari-Administrator:** Dieses Konto wird auch als *Hadoop-Benutzer* oder *HTTP-Benutzer* bezeichnet. Dieses Konto kann für die Anmeldung bei Ambari unter https://&lt;Clustername>.azurehdinsight.net verwendet werden. Es kann auch zum Ausführen von Abfragen für Ambari-Ansichten, zum Ausführen von Aufträgen über externe Tools (z.B. PowerShell, Templeton, Visual Studio) und für die Authentifizierung mit dem Hive ODBC-Treiber und BI-Tools (z.B. Excel, Power BI oder Tableau) verwendet werden.

Ein HDInsight-Cluster mit Enterprise-Sicherheitspaket verfügt neben Ambari-Administrator über drei neue Benutzer.

* **Ranger-Administrator:**  Dieses Konto ist das lokale Apache Ranger-Administratorkonto. Es handelt sich um keinen Active Directory-Domänenbenutzer. Dieses Konto kann verwendet werden, um Richtlinien einzurichten und andere Benutzer als Administratoren oder delegierte Administratoren festzulegen (sodass diese Benutzer Richtlinien verwalten können). Der Benutzername lautet standardmäßig *admin*, und das Kennwort ist identisch mit dem Ambari-Administratorkennwort. Das Kennwort kann auf der Seite „Settings“ (Einstellungen) in Ranger aktualisiert werden.
* **Domänenbenutzer des Clusteradministrators:** Dieses Konto ist ein Active Directory-Domänenbenutzer, der als Hadoop-Clusteradministrator (einschließlich Ambari und Ranger) festgelegt ist. Sie müssen die Anmeldeinformationen dieses Benutzers während der Clustererstellung angeben. Dieser Benutzer verfügt über die folgenden Berechtigungen:

  * Einbinden von Computern in die Domäne und Platzieren dieser Computer in der Organisationseinheit, die Sie während der Clustererstellung angeben.
  * Erstellen von Dienstprinzipalen in der Organisationseinheit, die Sie während der Clustererstellung angeben.
  * Erstellen von Reverse-DNS-Einträgen.

    Beachten Sie, dass die anderen AD-Benutzer auch über diese Berechtigungen verfügen.

    Es gibt einige Endpunkte innerhalb des Clusters (z.B. Templeton), die nicht von Ranger verwaltet werden und daher nicht sicher sind. Diese Endpunkte sind für alle Benutzer außer dem Domänenbenutzer des Clusteradministrators gesperrt.
* **Normal:** Während der Clustererstellung können Sie mehrere Active Directory-Gruppen angeben. Die Benutzer in diesen Gruppen werden mit Ranger und Ambari synchronisiert. Diese Benutzer sind Domänenbenutzer und haben nur Zugriff auf über Ranger verwaltete Endpunkte (z.B. Hiveserver2). Alle RBAC-Richtlinien und die Überwachung gelten für diese Benutzer.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Rollen von HDInsight-Clustern mit Enterprise-Sicherheitspaket
Das HDInsight-ESP hat die folgenden Rollen:

* Clusteradministrator
* Clusteroperator
* Dienstadministrator
* Dienstoperator
* Clusterbenutzer

**So zeigen Sie die Berechtigungen dieser Rollen an**

1. Öffnen Sie die Ambari-Verwaltungsoberfläche.  Informationen hierzu finden Sie unter [Öffnen der Ambari-Verwaltungsoberfläche](#open-the-ambari-management-ui).
2. Klicken Sie im linken Menü auf **Roles** (Rollen).
3. Klicken Sie auf das blaue Fragezeichen, um die Berechtigungen anzuzeigen:

    ![Berechtigungen für ESP-HDInsight-Rollen](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Öffnen der Ambari-Verwaltungsoberfläche

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Öffnen Sie Ihren HDInsight-Cluster.
3. Klicken Sie im Hauptmenü auf **Dashboard**, um Ambari zu öffnen.
4. Melden Sie sich mit dem Domänenbenutzernamen und dem Kennwort des Clusteradministrators bei Ambari an.
5. Klicken Sie oben rechts auf das Dropdownmenü **Admin** und dann auf **Manage Ambari** (Ambari verwalten).

    ![ESP-HDInsight – Verwalten von Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Die Benutzeroberfläche sieht wie folgt aus:

    ![ESP-HDInsight-Oberfläche zur Verwaltung von Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Auflisten der über Active Directory synchronisierten Domänenbenutzer
1. Öffnen Sie die Ambari-Verwaltungsoberfläche.  Informationen hierzu finden Sie unter [Öffnen der Ambari-Verwaltungsoberfläche](#open-the-ambari-management-ui).
2. Klicken Sie im linken Menü auf **Benutzer**. Es werden alle Benutzer angezeigt, die über Active Directory mit dem HDInsight-Cluster synchronisiert wurden.

    ![ESP-HDInsight-Oberfläche zur Verwaltung von Ambari – Auflisten der Benutzer](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Auflisten der über Active Directory synchronisierten Domänengruppen
1. Öffnen Sie die Ambari-Verwaltungsoberfläche.  Informationen hierzu finden Sie unter [Öffnen der Ambari-Verwaltungsoberfläche](#open-the-ambari-management-ui).
2. Klicken Sie im linken Menü auf **Groups** (Gruppen). Es werden alle Gruppen angezeigt, die über Active Directory mit dem HDInsight-Cluster synchronisiert wurden.

    ![ESP-HDInsight-Oberfläche zur Verwaltung von Ambari – Auflisten der Gruppen](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Konfigurieren von Berechtigungen für Hive-Ansichten
1. Öffnen Sie die Ambari-Verwaltungsoberfläche.  Informationen hierzu finden Sie unter [Öffnen der Ambari-Verwaltungsoberfläche](#open-the-ambari-management-ui).
2. Klicken Sie im linken Menü auf **Views** (Ansichten).
3. Klicken Sie auf **HIVE**, um die Details anzuzeigen.

    ![ESP-HDInsight-Oberfläche zur Verwaltung von Ambari – Hive-Ansichten](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Klicken Sie auf den Link **Hive View** (Hive-Ansicht), um Hive-Ansichten zu konfigurieren.
5. Scrollen Sie nach unten zum Abschnitt **Permissions** (Berechtigungen).

    ![ESP-HDInsight-Oberfläche zur Verwaltung von Ambari – Konfigurieren von Berechtigungen in Hive-Ansichten](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Klicken Sie auf **Add User** (Benutzer hinzufügen) oder **Add Group** (Gruppe hinzufügen), und geben Sie dann die Benutzer oder Gruppen an, die Hive-Ansichten verwenden können.

## <a name="configure-users-for-the-roles"></a>Konfigurieren von Benutzern für die Rollen
 Eine Liste der Rollen und der zugehörigen Berechtigungen finden Sie unter „Rollen von HDInsight-Clustern mit Enterprise-Sicherheitspaket“.

1. Öffnen Sie die Ambari-Verwaltungsoberfläche.  Informationen hierzu finden Sie unter [Öffnen der Ambari-Verwaltungsoberfläche](#open-the-ambari-management-ui).
2. Klicken Sie im linken Menü auf **Roles** (Rollen).
3. Klicken Sie auf **Add User** (Benutzer hinzufügen) oder **Add Group** (Gruppe hinzufügen), um Benutzer und Gruppen zu verschiedenen Rollen zuzuweisen.

## <a name="next-steps"></a>Nächste Schritte
* Informationen zum Konfigurieren eines HDInsight-Clusters mit Enterprise-Sicherheitspaket finden Sie unter [Konfigurieren von HDInsight-Clustern mit Enterprise-Sicherheitspaket](apache-domain-joined-configure.md).
* Informationen zum Konfigurieren von Hive-Richtlinien und zum Ausführen von Hive-Abfragen finden Sie unter [Konfigurieren von Apache Hive-Richtlinien in HDInsight mit dem Enterprise-Sicherheitspaket](apache-domain-joined-run-hive.md).
