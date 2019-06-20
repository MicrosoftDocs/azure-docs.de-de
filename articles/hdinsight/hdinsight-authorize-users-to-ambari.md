---
title: Autorisieren von Benutzern für Ambari-Ansichten – Azure HDInsight
description: Erfahren Sie, wie Sie Benutzer- und Gruppenberechtigungen in Ambari für HDInsight-Cluster verwalten, bei denen das ESP aktiviert ist.
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 69ae1bd05b64912b3d53ca88b468a72a90ff5a74
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64718317"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autorisieren von Benutzern für Apache Ambari-Ansichten

[HDInsight-Cluster mit aktiviertem Enterprise-Sicherheitspaket (ESP)](./domain-joined/apache-domain-joined-introduction.md) stellen Funktionen auf Unternehmensniveau bereit, einschließlich einer auf Azure Active Directory basierenden Authentifizierung. Sie können [neue Benutzer synchronisieren](hdinsight-sync-aad-users-to-cluster.md), die zu Azure AD-Gruppen mit Clusterzugriff hinzugefügt wurden, um diesen bestimmten Benutzern das Ausführen bestimmter Aktionen zu ermöglichen. Arbeiten mit Benutzern, Gruppen und Berechtigungen in [Apache Ambari](https://ambari.apache.org/) wird sowohl für ESP-HDInsight-Cluster als auch für den standardmäßigen HDInsight-Cluster unterstützt.

Active Directory-Benutzer können sich mit ihren Domänenanmeldeinformationen bei den Clusterknoten anmelden. Darüber hinaus können sie Clusterinteraktionen mit ihren Domänenanmeldeinformationen auch bei anderen genehmigten Endpunkten wie [Hue](https://gethue.com/), Ambari-Ansichten, ODBC, JDBC, PowerShell und REST-APIs authentifizieren.

> [!WARNING]  
> Ändern Sie nicht das Kennwort für den Ambari-Watchdog (hdinsightwatchdog) in Ihrem Linux-basierten HDInsight-Cluster. Durch eine Kennwortänderung wird die Möglichkeit zum Verwenden von Skriptaktionen oder zum Durchführen von Skalierungsvorgängen mit Ihren Cluster deaktiviert.

Falls noch nicht geschehen, führen Sie [diese Anweisungen](./domain-joined/apache-domain-joined-configure.md) aus, um einen neuen ESP-Cluster bereitzustellen.

## <a name="access-the-ambari-management-page"></a>Zugreifen auf die Ambari-Verwaltungsseite

Navigieren Sie zur **Ambari-Verwaltungsseite** der [Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md) und rufen Sie **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** auf. Geben Sie den Benutzernamen und das Kennwort für den Clusteradministrator ein, die Sie beim Erstellen des Clusters definiert haben. Wählen Sie anschließend im Ambari-Dashboard die Option **Ambari verwalten** unterhalb des **Administrator**-Menüs aus:

![Ambari verwalten](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Erteilen von Berechtigungen für Apache Hive-Ansichten

Ambari verfügt über Ansichtsinstanzen für u.a. [Apache Hive](https://hive.apache.org/) und [Apache TEZ](https://tez.apache.org/). Wechseln Sie zur **Ambari-Verwaltungsseite**, um den Zugriff auf eine oder mehrere Hive-Ansichtsinstanzen zu gewähren.

1. Wählen Sie auf der Verwaltungsseite den Link **Ansichten** unterhalb der Menüüberschrift **Ansichten** auf der linken Seite aus.

    ![Link „Ansichten“](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. Erweitern Sie auf der Seite „Ansichten“ die Zeile **HIVE**. Eine Hive-Ansicht wird standardmäßig beim Hinzufügen des Hive-Diensts zum Cluster erstellt. Sie können bei Bedarf auch weitere Hive-Ansichtsinstanzen erstellen. Wählen Sie eine Hive-Ansicht aus:

    ![Ansichten > Hive-Ansicht](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Scrollen Sie auf der Seite „Ansichten“ nach unten. Im Abschnitt *Berechtigungen* stehen zwei Möglichkeiten zur Auswahl, um Domänenbenutzern Berechtigungen für die Ansicht zu erteilen:

**Erteilen einer Berechtigung für diese Benutzer** ![Erteilen einer Berechtigung für diese Benutzer](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Erteilen einer Berechtigung für diese Gruppen** ![Erteilen einer Berechtigung für diese Gruppen](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

1. Klicken Sie zum Hinzufügen eines Benutzers auf die Schaltfläche **Benutzer hinzufügen**.

   * Wenn Sie mit der Eingabe des Benutzernamens beginnen, wird eine Dropdownliste mit den zuvor definierten Namen angezeigt.

     ![AutoVervollständigen des Benutzernamens](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

   * Wählen Sie einen Namen aus, oder geben Sie den vollständigen Benutzernamen ein. Klicken Sie auf die Schaltfläche **Neu**, um diesen Benutzernamen als neuen Benutzer hinzuzufügen.

   * Aktivieren Sie zum Speichern der Änderungen das **blaue Kontrollkästchen**.

     ![Eingegebener Benutzer](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

1. Klicken Sie zum Hinzufügen einer Gruppe auf die Schaltfläche **Gruppe hinzufügen**.

   * Beginnen Sie mit der Eingabe des Gruppennamens. Die Vorgehensweise bei der Auswahl eines bereits existierenden Gruppennamens oder beim Hinzufügen einer neuen Gruppe entspricht der Vorgehensweise beim Hinzufügen von Benutzern.
   * Aktivieren Sie zum Speichern der Änderungen das **blaue Kontrollkästchen**.

     ![Eingegebene Gruppe](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Es kann nützlich sein, Benutzer direkt einer Ansicht hinzuzufügen, wenn Sie einem Benutzer die Berechtigungen zuweisen möchten, diese Ansicht zu verwenden, jedoch nicht möchten, dass er Mitglied einer Gruppe mit zusätzlichen Berechtigungen wird. Für einen möglichst geringen Verwaltungsaufwand ist es möglicherweise einfacher, Berechtigungen für Gruppen zuzuweisen.

## <a name="grant-permissions-to-apache-tez-views"></a>Erteilen von Berechtigungen für Apache TEZ-Ansichten

[Apache TEZ](https://tez.apache.org/)-Ansichtsinstanzen ermöglichen Benutzern das Überwachen und Debuggen aller Tez-Aufträge, die von [Apache Hive](https://hive.apache.org/)-Abfragen und [Apache Pig](https://pig.apache.org/)-Skripts übermittelt wurden. Eine Tez-Ansichtsinstanz wird standardmäßig bei der Bereitstellung des Clusters erstellt.

Erweitern Sie wie zuvor beschrieben auf der Seite „Ansichten“ die Zeile **TEZ**, um Benutzer und Gruppen einer Tez-Ansichtsinstanz zuzuweisen.

![Ansichten > Tez-Ansicht](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Wiederholen Sie die Schritte 3 bis 5 im vorherigen Abschnitt, um Benutzer und Gruppen hinzuzufügen.

## <a name="assign-users-to-roles"></a>Zuweisen von Benutzern zu Rollen

Es gibt fünf Sicherheitsrollen für Benutzer und Gruppen, die nachfolgend in absteigender Reihenfolge der Zugriffsberechtigungen aufgeführt werden:

* Clusteradministrator
* Clusteroperator
* Dienstadministrator
* Dienstoperator
* Clusterbenutzer

Wechseln Sie zum Verwalten der Rollen zur **Ambari-Verwaltungsseite**, und wählen Sie in der Menügruppe *Cluster* auf der linken Seite den Link **Rollen** aus.

![Menülink „Rollen“](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Klicken Sie auf der Seite „Rollen“ auf das blaue Fragezeichen neben der Tabellenüberschrift **Rollen**, um die Liste der Berechtigungen anzuzeigen, die den Rollen jeweils zugeordnet sind.

![Menülink „Rollen“](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

Auf dieser Seite stehen Ihnen zwei verschiedene Ansichten zur Verwaltung der Rollen für Benutzer und Gruppen zur Verfügung: Block- und Listenansicht.

### <a name="block-view"></a>Blockansicht

Bei der Blockansicht wird jede Rolle in einer eigenen Zeile angezeigt, und es stehen die zuvor beschriebenen Optionen **Zuweisen von Rollen an diese Benutzer** und **Zuweisen von Rollen an diese Gruppen** zur Verfügung.

![Blockansicht für Rollen](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Listenansicht

Die Listenansicht stellt schnelle Bearbeitungsfunktionen in zwei Kategorien bereit: Benutzer und Gruppen.

* Bei der Kategorie „Benutzer“ der Listenansicht wird eine Liste aller Benutzer angezeigt. Hier können Sie für jeden Benutzer eine Rolle aus der Dropdownliste auswählen.

    ![Listenansicht für Rollen – Benutzer](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  Bei der Kategorie „Gruppen“ der Listenansicht werden alle Gruppen und die der jeweiligen Gruppe zugewiesene Rolle angezeigt. In diesem Beispiel wird die Liste der Gruppen mit den Azure AD-Gruppen synchronisiert, die in der Eigenschaft **Zugriff auf die Benutzergruppe** der Domäneneinstellungen für den Cluster angegeben sind. Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Erstellen eines HDInsight-Clusters mit dem ESP](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp).

    ![Listenansicht für Rollen – Gruppen](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    In der vorherigen Abbildung wird die Gruppe „hiveusers“ (Hive-Benutzer) der Rolle *Clusterbenutzer* zugewiesen. Dies ist eine schreibgeschützte Rolle, mit der die Benutzer dieser Gruppe Dienstkonfigurationen und Clustermetriken anzeigen, jedoch nicht ändern können.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Anmelden bei Ambari als schreibgeschützter Benutzer

Dem Azure AD-Domänenbenutzer „hiveuser1“ wurden Berechtigungen für Hive- und Tez-Ansichten zugewiesen. Nach dem Eingeben der Domänenanmeldeinformationen dieses Benutzers (Azure AD-Benutzername im E-Mail-Format und Kennwort) in der Ambari-Webbenutzeroberfläche wird der Benutzer zur Seite „Ambari-Ansichten“ umgeleitet. Hier kann der Benutzer jede beliebige Ansicht auswählen, auf die zugegriffen werden kann. Der Zugriff auf andere Bereiche dieser Website, z.B. Dashboard, Dienste, Hosts, Warnungen oder Administratorseiten, ist für diesen Benutzer nicht möglich.

![Schreibgeschützter Benutzer](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Anmelden bei Ambari als Clusterbenutzer

Dem Azure AD-Domänenbenutzer „hiveuser2“ wurde die Rolle *Clusterbenutzer* zugewiesen. Mit dieser Rolle kann auf das Dashboard und alle Menüelemente zugegriffen werden. Ein Clusterbenutzer verfügt über weniger zulässige Optionen als ein Administrator. So kann der Benutzer „hiveuser2“ beispielsweise Konfigurationen für jeden der Dienste anzeigen, sie jedoch nicht bearbeiten.

![Benutzer mit Rolle „Clusterbenutzer“](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren von Apache Hive-Richtlinien in HDInsight mit ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Verwalten von HDInsight-Clustern mit dem Enterprise-Sicherheitspaket](./domain-joined/apache-domain-joined-manage.md)
* [Verwenden der Apache-Hive-Ansicht mit Apache Hadoop in HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synchronisieren von Azure AD-Benutzern mit dem Cluster](hdinsight-sync-aad-users-to-cluster.md)
