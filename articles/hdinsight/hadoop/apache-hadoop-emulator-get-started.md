---
title: Anleitung zur Verwendung einer Apache Hadoop-Sandbox, Emulator – Azure HDInsight
description: 'Um sich mit dem Apache Hadoop-Ökosystem vertraut zu machen, können Sie eine Hadoop-Sandbox von Hortonworks auf einem virtuellen Azure-Computer einrichten. '
keywords: hadoop emulator,hadoop sandbox
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: how-to
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 4f19797fa1c79261dc3459e2b924ed45d0b7e36e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547961"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Erste Schritte mit einer Apache Hadoop-Sandbox, einem Emulator auf einem virtuellen Computer

Hier erfahren Sie, wie Sie die Apache Hadoop-Sandbox von Hortonworks auf einem virtuellen Computer installieren, um sich mit dem Hadoop-Ökosystem vertraut zu machen. Die Sandbox bietet eine lokale Entwicklungsumgebung, in der Sie Hadoop, Hadoop Distributed File System (HDFS) und die Übermittlung von Aufträgen kennenlernen. Nachdem Sie sich mit Hadoop vertraut gemacht haben, können Sie Hadoop in Azure verwenden und einen HDInsight-Cluster erstellen. Weitere Informationen für Ihren Einstieg finden Sie unter [Erste Schritte mit Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Voraussetzungen

* [Oracle VirtualBox](https://www.virtualbox.org/). Sie können die Software [hier](https://www.virtualbox.org/wiki/Downloads) herunterladen und installieren.

## <a name="download-and-install-the-virtual-machine"></a>Herunterladen und Installieren des virtuellen Computers

1. Navigieren Sie zu den [Cloudera-Downloads](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

1. Klicken Sie unter **Choose Installation Type** (Installationsart auswählen) auf **VIRTUALBOX** , um die aktuelle Hortonworks Sandbox auf eine VM herunterzuladen. Melden Sie sich an, oder füllen Sie das Produktinteresse-Formular aus.

1. Klicken Sie auf die Schaltfläche **HDP SANDBOX (LATEST)** , um den Download zu starten.

Eine Anleitung zum Einrichten der Sandbox finden Sie im [Sandbox Deployment and Install Guide](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/) (Leitfaden zur Sandbox-Bereitstellung und -Installation).

Informationen zum Herunterladen einer älteren HDP Sandbox-Version erhalten Sie über die Links unter **Older Versions** (Frühere Versionen).

## <a name="start-the-virtual-machine"></a>Starten des virtuellen Computers

1. Öffnen Sie Oracle VirtualBox.
1. Klicken Sie im Menü **File** (Datei) auf **Import Appliance** (Appliance importieren), und geben Sie dann das Bild Hortonworks Sandbox-Image an.
1. Wählen Sie „Hortonworks Sandbox“, **Start** und anschließend **Normal Start** (Normaler Start) aus. Nach Abschluss des Startvorgangs für den virtuellen Computer werden Anmeldeanweisungen angezeigt.

    ![Normaler Start des VirtualBox-Managers](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Öffnen Sie einen Webbrowser, und navigieren Sie zur angezeigten URL (in der Regel `http://127.0.0.1:8888`).

## <a name="set-sandbox-passwords"></a>Festlegen der Sandbox-Kennwörter

1. Wählen Sie im Schritt **Get Started** (Erste Schritte) der Seite „Hortonworks Sandbox“ die Option **View Advanced Options** (Erweiterte Optionen anzeigen) aus. Verwenden Sie die Informationen auf dieser Seite, um sich mittels SSH bei der Sandbox anzumelden. Verwenden Sie den angegebenen Namen und das angegebene Kennwort.

   > [!NOTE]
   > Sollte kein SSH-Client installiert sein, können Sie die webbasierte SSH verwenden, die vom virtuellen Computer unter **http://localhost:4200/** bereitgestellt wird.

    Beim erstmaligen Herstellen einer Verbindung über SSH werden Sie aufgefordert, das Kennwort für das Stammkonto zu ändern. Geben Sie ein neues Kennwort ein, das für die Anmeldung mithilfe von SSH verwenden.

2. Geben Sie nach der Anmeldung den folgenden Befehl ein:

    ```bash
    ambari-admin-password-reset
    ```

    Geben Sie ein Kennwort für das Ambari-Administratorkonto ein, wenn Sie dazu aufgefordert werden. Dieses wird verwendet, wenn Sie auf die Ambari-Webbenutzeroberfläche zugreifen.

## <a name="use-hive-commands"></a>Verwenden von Hive-Befehlen

1. Verwenden Sie bei einer SSH-Verbindung mit der Sandbox den folgenden Befehl, um die Hive-Shell zu starten:

    ```bash
    hive
    ```

2. Verwenden Sie nach dem Start der Shell Folgendes, um die Tabellen anzuzeigen, die zusammen mit der Sandbox bereitgestellt werden:

    ```hiveql
    show tables;
    ```

3. Verwenden Sie Folgendes, um zehn Zeilen aus der Tabelle `sample_07` abzurufen:

    ```hiveql
    select * from sample_07 limit 10;
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Visual Studio mit der Sandbox von Hortonworks](./apache-hadoop-visual-studio-tools-get-started.md)

* [Einführung in Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Hadoop-Tutorial – Erste Schritte mit HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)