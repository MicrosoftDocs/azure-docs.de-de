---
title: Installieren von Drittanbieteranwendungen in Azure HDInsight
description: Es wird beschrieben, wie Sie Apache Hadoop-Anwendungen von Drittanbietern in Azure HDInsight installieren.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 3c1c7ac8fe8b7adf287bcde30a054df5ebaa63d7
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337439"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Installieren von Apache Hadoop-Anwendungen von Drittanbietern in Azure HDInsight

Hier erfahren Sie, wie Sie [Apache Hadoop](https://hadoop.apache.org/)-Anwendungen von Drittanbietern in Azure HDInsight installieren. Eine Anleitung zur Installation Ihrer eigenen Anwendung finden Sie unter [Installieren benutzerdefinierter HDInsight-Anwendungen](hdinsight-apps-install-custom-applications.md).

Eine HDInsight-Anwendung kann von Benutzern in einem HDInsight-Cluster installiert werden. Diese Anwendungen können von Microsoft oder von unabhängigen Softwareanbietern (Independent Software Vendors, ISVs) bezogen oder aber selbst entwickelt werden.  

Die folgende Liste zeigt die veröffentlichten Anwendungen:

|Application |Clustertyp(en) | BESCHREIBUNG |
|---|---|---|
|[AtScale Intelligence-Plattform](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop |AtScale wandelt Ihren HDInsight-Cluster in einen OLAP-Server mit horizontaler Skalierung um, wodurch Sie vertraute Business Intelligence-Tools (von Microsoft Excel, Power BI und Tableau Software bis hin zu Qlikview), die Sie bereits besitzen und zu schätzen wissen, interaktiv zum Abfragen von Milliarden von Zeilen von Daten verwenden können. |
|[CDAP für HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |hbase |CDAP ist die erste einheitliche Integrationsplattform für Big Data, die die Amortisationszeit für Hadoop beschleunigt und Informatikern ermöglicht, Self-Service-Daten bereitzustellen. CDAP ist Open Source und erweiterbar, wodurch Hürden für die Innovation entfernt werden. Anforderungen: 4 Regionsknoten und eine D3 v2-Instanz (mindestens). |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Die skalierbare Self-Service-Plattform von Datameer zum Vorbereiten, Untersuchen und Steuern Ihrer Daten für die Analyse, mit der Sie komplexe Daten aus mehreren Quellen schneller in wertvolle unternehmensrelevante Informationen verwandeln und schnell hilfreiche Erkenntnisse für das gesamte Unternehmen gewinnen können. |
|[Dataiku DSS in HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS ist eine Data Science-Plattform für Unternehmen, mit der Data Scientists und Data Analysts zusammenarbeiten können, um neue Datenprodukte und Dienste effizienter zu entwerfen und auszuführen, wodurch Rohdaten in aussagekräftig Vorhersagen konvertiert werden. |
|[WANdisco Fusion HDI App](https://community.wandisco.com/s/article/Use-WANdisco-Fusion-for-parallel-operation-of-ADLS-Gen1-and-Gen2) |Hadoop, Spark, HBase, Storm, Kafka |Die Beibehaltung der Datenkonsistenz in einer verteilten Umgebung ist eine große Herausforderung für Datenvorgänge. WANdisco Fusion ist eine professionelle Softwareplattform, die dieses Problem löst, indem unstrukturierte Datenkonsistenz zwischen beliebigen Umgebungen ermöglicht wird. |
|[H2O SparklingWater für HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water unterstützt die folgenden verteilten Algorithmen: GLM, Naïve Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, Anomalieerkennung und Autoencoders. |
|[Striim für Echtzeitdatenintegration in HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.striimbyol) |Hadoop, HBase, Storm, Spark, Kafka |Striim (Aussprache: Stream) ist eine End-to-End-Integrations- und Intelligence-Plattform für Streamingdaten, die eine kontinuierliche Erfassung, Verarbeitung und Analyse unterschiedlicher Datenströme ermöglicht. |
|[Jumbune – BigData-Analyse für Unternehmensbeschleunigung](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |Jumbune unterstützt Unternehmen im Allgemeinen wie folgt: 1\. Beschleunigt die Workloadleistung von Tez, MapReduce & Spark-basiertes Hive, Java, und Scala. 2. Überwacht Hadoop-Cluster proaktiv. 3\. Richtet Datenqualitätsverwaltung für verteilte Dateisysteme ein. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |Unterstützt von Apache Kylin ermöglicht Kyligence Enterprise Business Intelligence für Big Data. Kyligence Enterprise ist eine professionelle OLAP-Engine für Hadoop, mit der Business Analysten Business Intelligence für Hadoop mit Data Warehouse- und BI-Methoden entwerfen können, die dem Industriestandard entsprechen. |
|[Starburst Presto für Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburstdatainc1579800938563.starburst-presto?tab=Overview) |Hadoop |Presto ist eine schnelle und skalierbare verteilte SQL-Abfrage-Engine. Dank der architekturbedingten Trennung der Speicher- und Computekomponenten eignet sich Presto perfekt zum Abfragen von Daten in Azure Data Lake Storage, Azure Blob Storage, SQL- und NoSQL-Datenbanken sowie in anderen Datenquellen. |
|[StreamSets Data Collector für HDInsight Cloud](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |StreamSets Data Collector ist eine einfache, leistungsstarke Engine, die Daten in Echtzeit übermittelt. Mit Data Collector können Sie Daten in Ihren Datenströmen weiterleiten und verarbeiten. Eine kostenlose 30-tägige Testlizenz steht zur Verfügung. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.trifacta-db?tab=Overview) |Hadoop, Spark, HBase |Trifacta Wrangler Enterprise für HDInsight unterstützt unternehmensweites Data Wrangling für Daten bei jeder Skalierung. Beim Ausführen von Trifacta in Azure entstehen die Kosten aus den Kosten für das Trifacta-Abonnement und den Kosten Azure-Infrastrukturkosten für die virtuellen Computer. |
|[Unifi Data Platform](https://unifisoftware.com/platform/) |Hadoop, HBase, Storm, Spark |Unifi Data Platform ist eine nahtlos integrierte Sammlung von Self-Service-Datentools, mit der Unternehmensbenutzer datenbezogene Herausforderungen bewältigen können, um höhere Umsätze zu erzielen, Kosten zu senken oder die Komplexität des Betriebs zu verringern. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Unravel Data-App für HDInsight Spark-Cluster. |
|[Waterline – KI-gesteuerter Datenkatalog](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |Waterline katalogisiert, strukturiert und steuert Daten und nutzt KI, um Daten automatisch mit geschäftlichen Begriffen zu markieren. Der Geschäftsdatenkatalog von Waterline in eine wichtige Erfolgskomponente für Self-Service-Analysen, Compliance und Governance sowie für IT-Verwaltungsinitiativen. |

Für die Anleitungen in diesem Artikel wird das Azure-Portal verwendet. Sie können die Azure Resource Manager-Vorlage auch aus dem Portal exportieren oder eine Kopie der Resource Manager-Vorlage von Anbietern erhalten und Azure PowerShell und die klassische Azure-Befehlszeilenschnittstelle zum Bereitstellen der Vorlage verwenden.  Weitere Informationen finden Sie unter [Erstellen von Apache Hadoop-Clustern in HDInsight mit Resource Manager-Vorlagen](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie HDInsight-Anwendungen in einem vorhandenen HDInsight-Cluster installieren möchten, benötigen Sie einen HDInsight-Cluster. Informationen zum Erstellen eines solchen Clusters finden Sie unter [Erstellen von Clustern](hadoop/apache-hadoop-linux-tutorial-get-started.md). Im Zuge der HDInsight-Clustererstellung können Sie auch HDInsight-Anwendungen installieren.

## <a name="install-applications-to-existing-clusters"></a>Installieren von Anwendungen für vorhandene Cluster
Das folgende Verfahren veranschaulicht, wie Sie HDInsight-Anwendungen für einen vorhandenen HDInsight-Cluster installieren.

**Installieren einer HDInsight-Anwendung**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie im linken Menü zu **Alle Dienste** > **Analytics** > **HDInsight-Cluster**.
3. Wählen Sie dann einen HDInsight-Cluster aus der Liste aus.  Falls Sie noch keinen Cluster besitzen, müssen Sie zuerst einen erstellen.  Weitere Informationen finden Sie unter [Erstellen von Clustern](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. Klicken Sie in der Kategorie **Einstellungen** auf **Anwendungen**. Im Hauptfenster wird eine Liste der installierten Anwendungen angezeigt. 
   
    ![HDInsight-Anwendungen – Portal-Menü](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Klicken Sie im Menü auf **+ Hinzufügen**. Ihnen wird eine Liste der verfügbaren Anwendungen angezeigt.  Wenn **+ Hinzufügen** ausgegraut ist, sind keine Anwendungen für diese Version des HDInsight-Clusters verfügbar.
   
    ![HDInsight-Anwendungen – Verfügbare Anwendungen](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Klicken Sie auf eine der verfügbaren Anwendungen, und folgen Sie dann den Anweisungen zum Akzeptieren der rechtlichen Bedingungen.

Sie können den Status der Installation über die Portalbenachrichtigungen verfolgen. (Klicken Sie hierzu oben im Portal auf das Glockensymbol). (Klicken Sie oben im Portal auf das Glockensymbol.) Nach der Installation wird die Anwendung in der Liste „Installierte Apps“ angezeigt.

## <a name="install-applications-during-cluster-creation"></a>Installieren von Anwendungen während der Clustererstellung

Sie haben die Möglichkeit, die HDInsight-Anwendung beim Erstellen eines Clusters zu installieren. Während des Prozesses werden HDInsight-Anwendungen installiert, nachdem der Cluster erstellt wurde und sich im ausgeführten Zustand befindet. Wählen Sie in der Registerkarte **Konfiguration + Preise** **+ Anwendung hinzufügen** aus, um bei der Clustererstellung im Azure-Portal Anwendungen zu installieren.

![Clusterkonfiguration für Anwendungen im Azure-Portal](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>Auflisten der installierten HDInsight-Apps und Eigenschaften
Im Portal wird eine Liste mit den installierten HDInsight-Anwendungen für einen Cluster und den Eigenschaften der einzelnen installierten Anwendungen angezeigt.

**Auflisten von HDInsight-Anwendungen und Eigenschaften**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie im linken Menü zu **Alle Dienste** > **Analytics** > **HDInsight-Cluster**.
3. Wählen Sie dann einen HDInsight-Cluster aus der Liste aus.
4. Klicken Sie in der Kategorie **Einstellungen** auf **Anwendungen**. Im Hauptfenster wird eine Liste der installierten Anwendungen angezeigt. 
   
    ![HDInsight-Anwendungen – Installierte Apps](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Wählen Sie eine der installierten Anwendungen aus, um die Eigenschaften anzuzeigen. Die Eigenschaft zeigt Folgendes:

    |Eigenschaft | BESCHREIBUNG |
    |---|---|
    |App-Name |Der Anwendungsname. |
    |Status |Anwendungsstatus |
    |Webseite |Die URL der Webanwendung, die Sie auf dem Edgeknoten bereitgestellt haben. Die Anmeldeinformationen sind mit den HTTP-Benutzeranmeldeinformationen identisch, die Sie für den Cluster konfiguriert haben. |
    |SSH-Endpunkt |Sie können über SSH eine Verbindung mit dem Edgeknoten herstellen. Die SSH-Anmeldeinformationen sind mit den SSH-Benutzeranmeldeinformationen identisch, die Sie für den Cluster konfiguriert haben. Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |BESCHREIBUNG | Anwendungsbeschreibung |

6. Klicken Sie zum Löschen einer Anwendung mit der rechten Maustaste darauf, und klicken Sie dann im Kontextmenü auf **Löschen**.

## <a name="connect-to-the-edge-node"></a>Herstellen einer Verbindung mit dem Edgeknoten
Sie können per HTTP und SSH eine Verbindung mit dem Edgeknoten herstellen. Sie finden die Endpunktinformationen im [Portal](#list-installed-hdinsight-apps-and-properties). Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

Die HTTP-Endpunkt-Anmeldeinformationen sind die HTTP-Benutzeranmeldeinformationen, die Sie für den HDInsight-Cluster konfiguriert haben. Die SSH-Endpunkt-Anmeldeinformationen sind die SSH-Anmeldeinformationen, die Sie für den HDInsight-Cluster konfiguriert haben.

## <a name="troubleshoot"></a>Problembehandlung
Weitere Informationen finden Sie unter [Behandeln von Installationsproblemen](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Nächste Schritte
* [Installieren benutzerdefinierter HDInsight-Anwendungen](hdinsight-apps-install-custom-applications.md): Hier erfahren Sie, wie Sie eine nicht veröffentlichte HDInsight-Anwendung in HDInsight bereitstellen.
* [Veröffentlichen von HDInsight-Anwendungen](hdinsight-apps-publish-applications.md): Hier erfahren Sie, wie Sie benutzerdefinierte HDInsight-Anwendungen im Azure Marketplace veröffentlichen.
* [MSDN: Installieren einer HDInsight-Anwendung](/rest/api/hdinsight/hdinsight-application): Hier erfahren Sie, wie Sie HDInsight-Anwendungen definieren.
* [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md): Hier erfahren Sie, wie Sie mithilfe der Skriptaktion zusätzliche Anwendungen installieren.
* [Erstellen von Linux-basierten Apache Hadoop-Clustern in HDInsight mit Resource Manager-Vorlagen](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Hier erfahren Sie, wie Sie Resource Manager-Vorlagen für die Erstellung von HDInsight-Clustern aufrufen.
* [Verwenden leerer Edgeknoten in HDInsight](hdinsight-apps-use-edge-node.md): Erfahren Sie, wie Sie einen leeren Edgeknoten zum Zugreifen auf HDInsight-Cluster, Testen von HDInsight-Anwendungen und Hosten von HDInsight-Anwendungen verwenden.