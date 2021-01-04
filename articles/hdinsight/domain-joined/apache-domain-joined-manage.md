---
title: Verwalten von Enterprise-Sicherheitspaketclustern – Azure HDInsight
description: Informationen zum Verwalten von Azure HDInsight-Clustern mit dem Enterprise-Sicherheitspaket.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 3dcb5d7ed75bda8422ba3bd461b08d3bfb2d974f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541008"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Verwalten von HDInsight-Clustern mit dem Enterprise-Sicherheitspaket

Lernen Sie die Benutzer und Rollen im Enterprise-Sicherheitspaket (Enterprise Security Package, ESP) von HDInsight kennen, und erfahren Sie, wie ESP-Cluster verwaltet werden.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Verknüpfen mit einem in die Domäne eingebundenen Cluster mithilfe von VSCode

Sie können einen normalen Cluster mithilfe eines verwalteten Apache Ambari-Benutzernamens oder einen Apache Hadoop-Sicherheitscluster mithilfe des Domänenbenutzernamens (z.B. `user1@contoso.com`) verknüpfen.

1. Öffnen Sie [Visual Studio Code](https://code.visualstudio.com/). Vergewissern Sie sich, dass die Erweiterung [Spark & Hive Tools](../hdinsight-for-vscode.md) installiert ist.

1. Führen Sie die Schritte unter [Verknüpfen eines Cluster](../hdinsight-for-vscode.md#link-a-cluster) für Visual Studio Code aus.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Verknüpfen mit einem in die Domäne eingebundenen Cluster mithilfe von IntelliJ

Sie können einen normalen Cluster mithilfe eines verwalteten Ambari-Benutzernamens oder einen Hadoop-Sicherheitscluster mithilfe des Domänenbenutzernamens (z.B. `user1@contoso.com`) verknüpfen.

1. Öffnen Sie IntelliJ IDEA. Stellen Sie sicher, dass alle [Voraussetzungen](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) erfüllt sind.

1. Führen Sie die Schritte unter [Verknüpfen eines Cluster](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) für IntelliJ aus.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Verknüpfen mit einem in die Domäne eingebundenen Cluster mithilfe von Eclipse

Sie können einen normalen Cluster mithilfe eines verwalteten Ambari-Benutzernamens oder einen Hadoop-Sicherheitscluster mithilfe des Domänenbenutzernamens (z.B. `user1@contoso.com`) verknüpfen.

1. Öffnen Sie Eclipse. Stellen Sie sicher, dass alle [Voraussetzungen](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) erfüllt sind.

1. Führen Sie die Schritte unter [Verknüpfen eines Cluster](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) für Eclipse aus.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Zugriff auf die Cluster mit dem Enterprise-Sicherheitspaket

Das Enterprise Security Package (bisher als HDInsight Premium bezeichnet) ermöglicht den Zugriff auf den Cluster durch mehrere Benutzer, wobei die Authentifizierung mit Active Directory und die Autorisierung mit Apache Ranger und Storage-ACLs (ADLS-ACLs) durchgeführt wird. Mit der Autorisierung wird für sichere Grenzen zwischen mehreren Benutzern gesorgt, und nur berechtigte Benutzer haben basierend auf den Autorisierungsrichtlinien Zugriff auf die Daten.

Die Sicherheit und die Benutzerisolation sind für einen HDInsight-Cluster mit Enterprise Security Package wichtig. Um diese Anforderungen zu erfüllen, wird der SSH-Zugriff auf den Cluster mit Enterprise-Sicherheitspaket für den lokalen Benutzer unterstützt, der zum Zeitpunkt der Cluster Erstellung ausgewählt wurde, sowie für Benutzer, die in AAD-DS (d. h. Kerberos) verfügbar sind. In der folgenden Tabelle sind die empfohlenen Zugriffsmethoden für die einzelnen Clustertypen aufgeführt:

|Workload|Szenario|Zugriffsmethode|
|--------|--------|-------------|
|Apache Hadoop|Hive – Interaktive Aufträge/Abfragen  |<ul><li>[Beeline](#beeline)</li><li>[Struktur anzeigen](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interaktive Aufträge/Abfragen, PySpark interaktiv|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin mit Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Struktur anzeigen](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Batchszenarien – Spark-Submit, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interaktive Abfrage (LLAP)|Interactive|<ul><li>[Beeline](#beeline)</li><li>[Struktur anzeigen](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Any|Installieren der benutzerdefinierten Anwendung|<ul><li>[Skriptaktionen](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter ist nicht installiert/wird in Enterprise-Sicherheitspaket nicht unterstützt.

Aus Sicht der Sicherheit ist die Verwendung der Standard-APIs hilfreich. Außerdem kommen Sie in den Genuss der folgenden Vorteile:

- **Verwaltung** : Sie können Ihren Code verwalten und Aufträge automatisieren, indem Sie Standard-APIs nutzen, z.B. Livy, HS2 usw.
- **Überwachung** : Mit SSH kann nicht überwacht werden, welche Benutzer per SSH auf den Cluster zugegriffen haben. Dies ist nicht der Fall, wenn Aufträge über Standardendpunkte erstellt werden, da sie dann im Kontext des Benutzers ausgeführt werden.

### <a name="use-beeline"></a><a name="beeline"></a>Verwenden von Beeline

Installieren Sie Beeline auf Ihrem Computer, und stellen Sie eine Verbindung über das öffentliche Internet her, indem Sie die folgenden Parameter verwenden:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Wenn Sie Beeline lokal installiert haben und eine Verbindung über ein virtuelles Azure-Netzwerk herstellen, verwenden Sie die folgenden Parameter:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Verwenden Sie die Informationen im Dokument „Verwalten von HDInsight mithilfe der Ambari-REST-API“, um den vollqualifizierten Domänennamen eines Hauptknotens zu ermitteln.

## <a name="users-of-hdinsight-clusters-with-esp"></a>Benutzer von HDInsight-Clustern mit Enterprise-Sicherheitspaket

Ein HDInsight-Cluster ohne Enterprise-Sicherheitspaket verfügt über zwei Benutzerkonten, die während der Clustererstellung erstellt werden:

- **Ambari-Administrator:** Dieses Konto wird auch als *Hadoop-Benutzer* oder *HTTP-Benutzer* bezeichnet. Dieses Konto kann für die Anmeldung bei Ambari unter `https://CLUSTERNAME.azurehdinsight.net` verwendet werden. Es kann auch zum Ausführen von Abfragen für Ambari-Ansichten, zum Ausführen von Aufträgen über externe Tools (z.B. PowerShell, Templeton, Visual Studio) und für die Authentifizierung mit dem Hive ODBC-Treiber und BI-Tools (z.B. Excel, Power BI oder Tableau) verwendet werden.

Ein HDInsight-Cluster mit Enterprise-Sicherheitspaket verfügt neben Ambari-Administrator über drei neue Benutzer.

- **Ranger-Administrator:** Dieses Konto ist das lokale Apache Ranger-Administratorkonto. Es handelt sich um keinen Active Directory-Domänenbenutzer. Dieses Konto kann verwendet werden, um Richtlinien einzurichten und andere Benutzer als Administratoren oder delegierte Administratoren festzulegen (sodass diese Benutzer Richtlinien verwalten können). Der Benutzername lautet standardmäßig *admin* , und das Kennwort ist identisch mit dem Ambari-Administratorkennwort. Das Kennwort kann auf der Seite „Settings“ (Einstellungen) in Ranger aktualisiert werden.

- **Domänenbenutzer des Clusteradministrators:** Dieses Konto ist ein Active Directory-Domänenbenutzer, der als Hadoop-Clusteradministrator (einschließlich Ambari und Ranger) festgelegt ist. Sie müssen die Anmeldeinformationen dieses Benutzers während der Clustererstellung angeben. Dieser Benutzer verfügt über die folgenden Berechtigungen:
    - Einbinden von Computern in die Domäne und Platzieren dieser Computer in der Organisationseinheit, die Sie während der Clustererstellung angeben.
    - Erstellen von Dienstprinzipalen in der Organisationseinheit, die Sie während der Clustererstellung angeben.
    - Erstellen von Reverse-DNS-Einträgen.

    Beachten Sie, dass die anderen AD-Benutzer auch über diese Berechtigungen verfügen.

    Es gibt einige Endpunkte innerhalb des Clusters (z. B. Templeton), die nicht von Ranger verwaltet werden und daher nicht sicher sind. Diese Endpunkte sind für alle Benutzer außer dem Domänenbenutzer des Clusteradministrators gesperrt.

- **Normal:** Während der Clustererstellung können Sie mehrere Active Directory-Gruppen angeben. Die Benutzer in diesen Gruppen werden mit Ranger und Ambari synchronisiert. Diese Benutzer sind Domänenbenutzer und haben nur Zugriff auf über Ranger verwaltete Endpunkte (z.B. Hiveserver2). Alle RBAC-Richtlinien und die Überwachung gelten für diese Benutzer.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Rollen von HDInsight-Clustern mit Enterprise-Sicherheitspaket

Das HDInsight-ESP hat die folgenden Rollen:

- Clusteradministrator
- Clusteroperator
- Dienstadministrator
- Dienstoperator
- Clusterbenutzer

**So zeigen Sie die Berechtigungen dieser Rollen an**

1. Öffnen Sie die Ambari-Verwaltungsoberfläche.  Informationen hierzu finden Sie unter [Öffnen der Ambari-Verwaltungsoberfläche](#open-the-ambari-management-ui).
2. Wählen Sie im Menü auf der linken Seite die Option **Rollen** aus.
3. Wählen Sie das blaue Fragezeichen aus, um die Berechtigungen anzuzeigen:

    ![Berechtigungen für ESP-HDInsight-Rollen](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Öffnen der Ambari-Verwaltungsoberfläche

1. Navigieren Sie zu `https://CLUSTERNAME.azurehdinsight.net/`, wobei CLUSTERNAME der Name Ihres Clusters ist.
1. Melden Sie sich mit dem Domänenbenutzernamen und dem Kennwort des Clusteradministrators bei Ambari an.
1. Wählen Sie oben rechts das Dropdownmenü **Administrator** und dann **Ambari verwalten** aus.

    ![ESP-HDInsight – Verwalten von Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Die Benutzeroberfläche sieht wie folgt aus:

    ![ESP-HDInsight: Oberfläche zur Verwaltung von Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Auflisten der über Active Directory synchronisierten Domänenbenutzer

1. Öffnen Sie die Ambari-Verwaltungsoberfläche.  Informationen hierzu finden Sie unter [Öffnen der Ambari-Verwaltungsoberfläche](#open-the-ambari-management-ui).
2. Wählen Sie im Menü auf der linken Seite die Option **Benutzer** aus. Es werden alle Benutzer angezeigt, die über Active Directory mit dem HDInsight-Cluster synchronisiert wurden.

    ![ESP-HDInsight-Oberfläche zur Verwaltung von Ambari – Auflisten der Benutzer](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Auflisten der über Active Directory synchronisierten Domänengruppen

1. Öffnen Sie die Ambari-Verwaltungsoberfläche.  Informationen hierzu finden Sie unter [Öffnen der Ambari-Verwaltungsoberfläche](#open-the-ambari-management-ui).
2. Wählen Sie im Menü auf der linken Seite die Option **Gruppen** aus. Es werden alle Gruppen angezeigt, die über Active Directory mit dem HDInsight-Cluster synchronisiert wurden.

    ![ESP-HDInsight-Oberfläche zur Verwaltung von Ambari – Auflisten der Gruppen](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Konfigurieren von Berechtigungen für Hive-Ansichten

1. Öffnen Sie die Ambari-Verwaltungsoberfläche.  Informationen hierzu finden Sie unter [Öffnen der Ambari-Verwaltungsoberfläche](#open-the-ambari-management-ui).
2. Wählen Sie im Menü auf der linken Seite die Option **Ansichten** aus.
3. Wählen Sie **HIVE** aus, um die Details anzuzeigen.

    ![ESP-HDInsight-Oberfläche zur Verwaltung von Ambari – Hive-Ansichten](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Klicken Sie auf den Link **Hive-Ansicht** , um Hive-Ansichten zu konfigurieren.
5. Scrollen Sie nach unten zum Abschnitt **Permissions** (Berechtigungen).

    ![ESP-HDInsight-Oberfläche zur Verwaltung von Ambari – Konfigurieren von Berechtigungen in Hive-Ansichten](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Wählen Sie **Benutzer hinzufügen** oder **Gruppe hinzufügen** aus, und geben Sie dann die Benutzer oder Gruppen an, die Hive-Ansichten verwenden können.

## <a name="configure-users-for-the-roles"></a>Konfigurieren von Benutzern für die Rollen

 Eine Liste der Rollen und der zugehörigen Berechtigungen finden Sie unter „Rollen von HDInsight-Clustern mit Enterprise-Sicherheitspaket“.

1. Öffnen Sie die Ambari-Verwaltungsoberfläche.  Informationen hierzu finden Sie unter [Öffnen der Ambari-Verwaltungsoberfläche](#open-the-ambari-management-ui).
2. Wählen Sie im Menü auf der linken Seite die Option **Rollen** aus.
3. Wählen Sie **Benutzer hinzufügen** oder **Gruppe hinzufügen** aus, um Benutzer und Gruppen verschiedenen Rollen zuzuweisen.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Konfigurieren eines HDInsight-Clusters mit Enterprise-Sicherheitspaket finden Sie unter [Konfigurieren von HDInsight-Clustern mit Enterprise-Sicherheitspaket](./apache-domain-joined-configure-using-azure-adds.md).
- Informationen zum Konfigurieren von Hive-Richtlinien und zum Ausführen von Hive-Abfragen finden Sie unter [Konfigurieren von Apache Hive-Richtlinien in HDInsight mit dem Enterprise-Sicherheitspaket](apache-domain-joined-run-hive.md).