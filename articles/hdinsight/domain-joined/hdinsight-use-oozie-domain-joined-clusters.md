---
title: Schützen von Apache Oozie-Workflows mit dem Enterprise-Sicherheitspaket – Azure HDInsight
description: Schützen Sie Apache Oozie-Workflows mit dem Azure HDInsight-Enterprise-Sicherheitspaket. Erfahren Sie, wie Sie einen Oozie-Workflow definieren und einen Oozie-Auftrag übermitteln können.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 7d7fbf5d72654c26edf09ab27f024eaf39f8c387
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64709000"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Ausführen von Apache Oozie in HDInsight Hadoop-Clustern mit Enterprise-Sicherheitspaket

Apache Oozie ist ein Workflow- und Koordinationssystem zur Verwaltung von Apache Hadoop-Aufträgen. Oozie ist in den Hadoop-Stack integriert und unterstützt die folgenden Aufträge:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Sie können Oozie auch dazu verwenden, bestimmte Aufträge für ein System zu planen, beispielsweise Java-Programme oder Shellskripts.

## <a name="prerequisite"></a>Voraussetzung

- Azure HDInsight Hadoop-Cluster mit Enterprise-Sicherheitspaket (ESP). Weitere Informationen finden Sie unter [Konfigurieren von HDInsight-Clustern mit Enterprise-Sicherheitspaket](./apache-domain-joined-configure-using-azure-adds.md).

    > [!NOTE]  
    > Eine ausführliche Anleitung zur Verwendung von Oozie in Clustern ohne ESP finden Sie unter [Verwenden von Apache Oozie-Workflows in Linux-basiertem Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Herstellen einer Verbindung mit einem ESP-Cluster

Weitere Informationen zu Secure Shell (SSH) finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her:  
   ```bash
   ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
   ```

2. Überprüfen Sie mit dem Befehl `klist`, ob die Kerberos-Authentifizierung erfolgreich war. Falls dies nicht der Fall ist, verwenden Sie `kinit`, um die Kerberos-Authentifizierung einzuleiten.

3. Melden Sie sich beim HDInsight-Gateway an, um das OAuth-Token zu registrieren, das für den Zugriff auf Azure Data Lake Storage (ADLS) erforderlich ist:   
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Der Statusantwortcode **200 OK** zeigt die erfolgreiche Registrierung an. Überprüfen Sie den Benutzernamen und das Kennwort, wenn eine Antwort mit dem Hinweis „nicht autorisiert“ (z.B. 401) empfangen wird.

## <a name="define-the-workflow"></a>Definieren des Workflows
Definitionen von Oozie-Workflows werden in der Apache-Prozessdefinitionssprache (Hadoop Process Definition Language, hPDL) geschrieben. hPDL ist eine XML-Prozessdefinitionssprache. Führen Sie zum Definieren des Workflows die folgenden Schritte aus:

1. Richten Sie den Arbeitsbereich eines Domänenbenutzers ein:
   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```
   Ersetzen Sie `DomainUser` durch den Namen des Domänenbenutzers. 
   Ersetzen Sie `DomainUserPath` durch den Pfad des Stammverzeichnisses für den Domänenbenutzer. 
   Ersetzen Sie `ClusterVersion` durch die Hortonworks Data Platform-Version (HDP) Ihres Clusters.

2. Verwenden Sie die folgende Anweisung zum Erstellen und Bearbeiten einer neuen Datei:
   ```bash
   nano workflow.xml
   ```

3. Nachdem der Nano-Editor geöffnet wurde, verwenden Sie den folgenden XML-Code als Inhalt der Datei:
   ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
   ```
4. Ersetzen Sie `clustername` durch den Namen des Clusters. 

5. Drücken Sie zum Speichern der Datei STRG+X. Geben Sie `Y` ein. Drücken Sie anschließend die **EINGABETASTE**.

    Der Workflow ist in zwei Teile gegliedert:
   * **Anmeldeinformationsabschnitt.** In diesen Abschnitt werden die Anmeldeinformationen eingegeben, die für die Authentifizierung von Oozie-Aktionen verwendet werden:

     In diesem Beispiel wird die Authentifizierung für Hive-Aktionen verwendet. Weitere Informationen finden Sie unter [Action Authentication](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html) (Aktionsauthentifizierung).

     Der Anmeldeinformationsdienst ermöglicht es Oozie-Aktionen, für den Zugriff auf Hadoop-Dienste die Identität des Benutzers anzunehmen.

   * **Aktionsabschnitt.** Dieser Abschnitt enthält drei Aktionen – „map-reduce“, die Hive-Server 2-Aktion und die Hive-Server 1-Aktion:

     - Die Aktion „map-reduce“ führt ein Beispiel aus einem Oozie-Paket für „map-reduce“ aus, das die aggregierte Wortanzahl ausgibt.

     - Die Hive-Server 2- und Hive-Server 1-Aktionen führen eine Abfrage in einer mit HDInsight bereitgestellten Hive-Beispieltabelle aus.

     Die Hive-Aktionen verwenden die im Anmeldeinformationsabschnitt definierten Anmeldeinformationen für die Authentifizierung anhand des Schlüsselworts `cred` im Aktionselement.

6. Kopieren Sie mit folgendem Befehl die Datei `workflow.xml` nach `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

7. Ersetzen Sie `domainuser` durch Ihren Benutzernamen für die Domäne.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definieren der Eigenschaftendatei für den Oozie-Auftrag

1. Verwenden Sie die folgende Anweisung zum Erstellen und Bearbeiten einer neuen Datei für Auftragseigenschaften:

   ```bash
   nano job.properties
   ```

2. Nachdem der Nano-Editor geöffnet wurde, verwenden Sie den folgenden XML-Code als Inhalt der Datei:

   ```bash
       nameNode=adl://home
       jobTracker=headnodehost:8050
       queueName=default
       examplesRoot=examples
       oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
       hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
       hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
       oozie.use.system.libpath=true
       user.name=[domainuser]
       jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
       jdbcURL=[jdbcurlvalue]
       hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
       hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   * Verwenden der `adl://home`-URI für die `nameNode`-Eigenschaft, wenn Sie Azure Data Lake Storage Gen1 als primären Clusterspeicher verwenden. Wenn Sie Azure Blob Storage verwenden, ändern Sie diese Angabe in `wasb://home`. Wenn Sie Azure Data Lake Storage Gen2 verwenden, ändern Sie diese Angabe in `abfs://home`.
   * Ersetzen Sie `domainuser` durch Ihren Benutzernamen für die Domäne.  
   * Ersetzen Sie `ClusterShortName` durch den Kurznamen für den Cluster. Wenn der Clustername „https:// *[Beispiellink]* sechadoopcontoso.azurehdisnight.net“ lautet, stellt `clustershortname` die ersten sechs Zeichen des Clusters dar: **sechad**.  
   * Ersetzen Sie `jdbcurlvalue` durch die JDBC-URL aus der Hive-Konfiguration. Ein Beispiel hierfür ist „jdbc:hive2://headnodehost:10001/;transportMode=http“.      
   * Um die Datei zu speichern, drücken Sie STRG + X, geben Sie `Y` ein, und drücken Sie auf die **EINGABETASTE**.

   Diese Eigenschaftendatei muss bei der Ausführung von Oozie-Aufträgen lokal vorhanden sein.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Erstellen benutzerdefinierter Hive-Skripts für Oozie-Aufträge

Sie können die beiden Hive-Skripts für Hive-Server 1 und Hive-Server 2 wie in den folgenden Abschnitten gezeigt erstellen.

### <a name="hive-server-1-file"></a>Datei für Hive-Server 1

1.  Erstellen und bearbeiten Sie eine Datei für Hive-Server 1-Aktionen:
    ```bash
    nano countrowshive1.hql
    ```

2.  Erstellen Sie das Skript:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Speichern Sie die Datei im Apache Hadoop Distributed File System (HDFS):
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Datei für Hive-Server 2

1.  Erstellen und bearbeiten Sie eine Datei für Hive-Server 2-Aktionen:
    ```bash
    nano countrowshive2.hql
    ```

2.  Erstellen Sie das Skript:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Speichern Sie die Datei im HDFS:
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Übermitteln von Oozie-Aufträgen

Das Übermitteln von Oozie-Aufträgen für ESP-Cluster erfolgt wie die Übermittlung von Oozie-Aufträgen in Clustern ohne ESP.

Weitere Informationen finden Sie unter [Verwenden von Apache Oozie mit Apache Hadoop zum Definieren und Ausführen eines Workflows in Linux-basiertem Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Ergebnisse einer Oozie-Auftragsübermittlung
Oozie-Aufträge werden für den Benutzer ausgeführt. Daher werden sowohl in Apache Hadoop YARN- als auch Apache Ranger-Überwachungsprotokollen die mit der Identität des Benutzers ausgeführten Aufträge angezeigt. Die Ausgabe der Befehlszeilenschnittstelle eines Oozie-Auftrags sieht wie im folgenden Codebeispiel dargestellt aus:



```bash
    Job ID : 0000015-180626011240801-oozie-oozi-W
    ------------------------------------------------------------------------------------------------
    Workflow Name : map-reduce-wf
    App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
    Status        : SUCCEEDED
    Run           : 0
    User          : alicetest
    Group         : -
    Created       : 2018-06-26 19:25 GMT
    Started       : 2018-06-26 19:25 GMT
    Last Modified : 2018-06-26 19:30 GMT
    Ended         : 2018-06-26 19:30 GMT
    CoordAction ID: -
    
    Actions
    ------------------------------------------------------------------------------------------------
    ID                      Status  Ext ID          ExtStatus   ErrCode
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
    -----------------------------------------------------------------------------------------------
```

Die Ranger-Überwachungsprotokolle für Hive-Server 2-Aktionen zeigen, dass Oozie die Aktion für den Benutzer ausführt. Die Ranger- und YARN-Ansichten sind nur für den Clusteradministrator verfügbar.

## <a name="configure-user-authorization-in-oozie"></a>Konfigurieren der Benutzerautorisierung in Oozie

Oozie verfügt über eine Konfiguration zur Benutzerautorisierung, die verhindern kann, dass Benutzer Aufträge anderer Benutzer beenden oder löschen. Legen Sie zum Aktivieren dieser Konfiguration `oozie.service.AuthorizationService.security.enabled` auf `true` fest. 

Weitere Informationen finden Sie unter [Apache Oozie Installation and Configuration](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html) (Installation und Konfiguration von Apache Oozie).

Für Komponenten wie Hive-Server 1, für die das Ranger-Plug-In nicht verfügbar ist oder nicht unterstützt wird, ist nur die undifferenzierte HDFS-Autorisierung möglich. Eine differenzierte Autorisierung ist nur mithilfe von Ranger-Plug-Ins möglich.

## <a name="get-the-oozie-web-ui"></a>Zugreifen auf die Oozie-Webbenutzeroberfläche

Die Oozie-Webbenutzeroberfläche bietet eine webbasierte Anzeige des Status von Oozie-Aufträgen im Cluster. Um auf die Webbenutzeroberfläche zuzugreifen, führen Sie in ESP-Clustern die folgenden Schritte aus:

1. Fügen Sie einen [Edgeknoten](../hdinsight-apps-use-edge-node.md) hinzu, und aktivieren Sie die [SSH-Kerberos-Authentifizierung](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Führen Sie die unter [Oozie-Web-UI](../hdinsight-use-oozie-linux-mac.md) beschriebenen Schritte aus, um das Tunneln von SSH zum Edgeknoten zu aktivieren und auf das Web-UI zuzugreifen.

## <a name="next-steps"></a>Nächste Schritte
* [Verwenden von Apache Oozie mit Apache Hadoop zum Definieren und Ausführen eines Workflows in Linux-basiertem Azure HDInsight](../hdinsight-use-oozie-linux-mac.md)
* [Herstellen einer Verbindung mit HDInsight (Apache Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)
