---
title: TLS-Verschlüsselung und -Authentifizierung für Apache Kafka – Azure HDInsight
description: Einrichten von TLS-Verschlüsselung für die Kommunikation zwischen Kafka-Clients und Kafka-Brokern sowie zwischen Kafka-Brokern. Einrichten von SSL-Authentifizierung von Clients.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: 9a95970647a26ea80db9f63fb8523c6a65cc5e06
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012304"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Einrichten von TLS-Verschlüsselung und -Authentifizierung für Apache Kafka in Azure HDInsight

In diesem Artikel erfahren Sie, wie Sie TLS-Verschlüsselung (Transport Layer Security), vormals als SSL-Verschlüsselung (Secure Sockets Layer) bezeichnet, zwischen Apache Kafka-Clients und Apache Kafka-Brokern einrichten. Sie erfahren außerdem, wie Sie die Authentifizierung von Clients einrichten (auch als bidirektionale TLS bezeichnet).

> [!Important]
> Es gibt zwei Clients, die Sie für Kafka-Anwendungen verwenden können: einen Java-Client und einen Konsolenclient. Nur der Java-Client `ProducerConsumer.java` kann TLS sowohl zum Erzeugen als auch zum Nutzen verwenden. Der Konsolenproducerclient `console-producer.sh` funktioniert nicht mit TLS.

> [!Note]
> Der HDInsight Kafka-Konsolenproducer mit Version 1.1 unterstützt kein SSL.

## <a name="apache-kafka-broker-setup"></a>Einrichtung des Apache Kafka-Brokers

Das Kafka-TLS-Brokersetup verwendet vier HDInsight-Cluster-VMs auf folgende Weise:

* Hauptknoten 0 – Zertifizierungsstelle (Certificate Authority, CA)
* Workerknoten 0, 1 und 2 – Broker

> [!Note] 
> In dieser Anleitung werden selbstsignierte Zertifikate verwendet, aber die sicherste Lösung ist die Verwendung von Zertifikaten, die von vertrauenswürdigen Zertifizierungsstellen ausgestellt wurden.

Die Zusammenfassung des Brokereinrichtungsvorgangs lautet wie folgt:

1. Die folgenden Schritte werden auf jedem der drei Workerknoten wiederholt:

    1. Generieren eines Zertifikats.
    1. Erstellen einer Anforderung zur Zertifikatsignierung.
    1. Senden der Anforderung zur Zertifikatsignierung an die Zertifizierungsstelle (ZS).
    1. Anmelden bei der Zertifizierungsstelle und Signieren des Zertifikats.
    1. Zurücksenden des signierten Zertifikats mit SCP auf den Workerknoten.
    1. Senden des öffentlichen Zertifikats der ZS mit SCP an den Workerknoten.

1. Sobald Sie über alle Zertifikate verfügen, legen Sie die Zertifikate im Zertifikatspeicher ab.
1. Navigieren Sie zu Ambari, und ändern Sie die Konfigurationen.

Schließen Sie die Brokereinrichtung mit folgenden detaillierten Anweisungen ab:

> [!Important]
> In den folgenden Codeausschnitten ist wnX eine Abkürzung für einen der drei Workerknoten und sollte entsprechend durch `wn0`, `wn1` oder `wn2` ersetzt werden. `WorkerNode0_Name` und `HeadNode0_Name` sollten durch die Namen der entsprechenden Computer ersetzt werden.

1. Führen Sie das anfängliche Setup auf Hauptknoten 0 durch, der für HDInsight die Rolle der Zertifizierungsstelle (ZS) übernimmt.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Führen Sie das gleiche anfängliche Setup für jeden der Broker durch (Workerknoten 0, 1 und 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Führen Sie auf allen Workerknoten die folgenden Schritte mit dem folgenden Codeausschnitt aus.
    1. Erstellen Sie einen Keystore, und füllen Sie ihn mit einem neuen privaten Zertifikat.
    1. Erstellen Sie eine Anforderung zur Zertifikatsignierung.
    1. Senden Sie die Anforderung zur Zertifikatsignierung mit SCP an die Zertifizierungsstelle (headnode0).

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Führen Sie auf dem ZS-Computer den folgenden Befehl aus, um die Dateien „ca-cert“ und „ca-key“ zu erstellen:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Wechseln Sie zum ZS-Computer, und lassen Sie alle empfangenen Anforderungen zur Zertifikatsignierung ausführen:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Senden Sie die signierten Zertifikate von der ZS (headnode0) zurück an die Workerknoten.

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Senden Sie das öffentliche Zertifikat der ZS an die einzelnen Workerknoten.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Fügen Sie auf jedem Workerknoten das öffentliche Zertifikat der ZS dem Truststore und dem Keystore hinzu. Fügen Sie dann das eigene signierte Zertifikat des Workerknotens dem Keystore hinzu.

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Aktualisieren der Kafka-Konfiguration zur Verwendung von TLS und Neustarten der Broker

Sie haben jetzt jeden Kafka-Broker mit einem Keystore und Truststore eingerichtet sowie die richtigen Zertifikate importiert. Ändern Sie nun die zugehörigen Kafka-Konfigurationseigenschaften mit Ambari, und starten Sie die Kafka-Broker dann neu.

Führen Sie die folgenden Schritte aus, um die Konfigurationsänderung abzuschließen:

1. Melden Sie sich im Azure-Portal an, und wählen Sie Ihren Azure HDInsight Apache Kafka-Cluster aus.
1. Wechseln Sie in die Ambari-Benutzeroberfläche, indem Sie unter **Clusterdashboards** auf **Ambari Home** klicken.
1. Legen Sie unter **Kafka-Broker** die Eigenschaft **Listener** auf `PLAINTEXT://localhost:9092,SSL://localhost:9093` fest.
1. Legen Sie unter **Erweiterter Kafka-Broker** die Eigenschaft **security.inter.broker.protocol** auf `SSL` fest.

    ![Bearbeiten von Kafka-SSL-Konfigurationseigenschaften in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Legen Sie unter **Benutzerdefinierter Kafka-Broker** die Eigenschaft **ssl.client.auth** auf `required` fest. Dieser Schritt ist nur erforderlich, wenn Sie sowohl die Authentifizierung als auch die Verschlüsselung einrichten.

    ![Bearbeiten von Kafka-SSL-Konfigurationseigenschaften in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Wechseln Sie bei Verwendung der HDI-Version 3.6 zur Ambari-Benutzeroberfläche, und fügen Sie die folgenden Konfigurationen unter **Advanced kafka-env** und unter der Eigenschaft **kafka-env template** hinzu.

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Der Screenshot zeigt diese Änderungen auf der Ambari-Konfigurationsoberfläche.

    Für HDI, Version 3.6:

    ![Bearbeiten der Eigenschaft „kafka-env template“ in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    Für HDI, Version 4.0:

     ![Bearbeiten der Eigenschaft „kafka-env template“ in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)

1. Starten Sie alle Kafka-Broker neu.

## <a name="client-setup-without-authentication"></a>Einrichten des Clients (ohne Authentifizierung)

Wenn Sie keine Authentifizierung benötigen, finden Sie im Anschluss eine Zusammenfassung der Schritte zum ausschließlichen Einrichten der TLS-Verschlüsselung:

1. Melden Sie sich bei der Zertifizierungsstelle (aktiver Hauptknoten) an.
1. Kopieren Sie das Zertifizierungsstellenzertifikat vom Computer der Zertifizierungsstelle (wn0) auf den Clientcomputer.
1. Melden Sie sich beim Clientcomputer (hn1) an, und navigieren Sie zum Ordner `~/ssl`.
1. Importieren Sie das Zertifizierungsstellenzertifikat in den Truststore.
1. Importieren Sie das Zertifizierungsstellenzertifikat in den Keystore.

Diese Schritte werden in den folgenden Codeausschnitten genauer beschrieben:

1. Melden Sie sich beim Zertifizierungsstellenknoten an:

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Kopieren Sie das Zertifizierungsstellenzertifikat auf den Clientcomputer.

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Melden Sie sich beim Clientcomputer (Standbyhauptknoten) an.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importieren Sie das Zertifizierungsstellenzertifikat in den Truststore.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importieren Sie das Zertifizierungsstellenzertifikat in den Keystore.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Erstellen Sie die `client-ssl-auth.properties`-Datei auf dem Clientcomputer (hn1). Die Datei sollte folgende Zeilen enthalten:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. Starten Sie den Adminclient mit Producer- und Consumer Optionen, um sicherzustellen, dass sowohl Producer als auch Consumer and Port 9093 funktionieren. Informationen zu den erforderlichen Schritten zum Überprüfen des Setups mit dem Konsolenproducer/-consumer finden Sie im Abschnitt [Überprüfung](apache-kafka-ssl-encryption-authentication.md#verification).

## <a name="client-setup-with-authentication"></a>Einrichten des Clients (mit Authentifizierung)

> [!Note]
> Die folgenden Schritte sind nur erforderlich, wenn Sie TLS-Verschlüsselung **und** Authentifizierung einrichten. Wenn Sie nur Verschlüsselung einrichten, lesen Sie die Informationen unter [Einrichten des Clients (ohne Authentifizierung)](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

In den folgenden vier Schritten werden die Aufgaben zusammengefasst, die zum Ausführen des Clientsetups erforderlich sind:

1. Melden Sie sich beim Clientcomputer (Standbyhauptknoten) an.
1. Erstellen Sie einen Java-Keystore, und rufen Sie ein signiertes Zertifikat für den Broker ab. Kopieren Sie dann das Zertifikat auf den virtuellen Computer, auf dem die Zertifizierungsstelle ausgeführt wird.
1. Wechseln Sie zum Zertifizierungsstellencomputer (aktiver Hauptknoten), um das Clientzertifikat zu signieren.
1. Wechseln Sie zum Clientcomputer (Standbyhauptknoten), und navigieren Sie zum Ordner `~/ssl`. Kopieren Sie das signierte Zertifikat auf den Clientcomputer.

Die Details zu den einzelnen Schritten sind unten angegeben.

1. Melden Sie sich beim Clientcomputer (Standbyhauptknoten) an.

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Entfernen Sie alle vorhandenen SSL-Verzeichnisse.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Erstellen Sie einen Java-Keystore und eine Zertifikatsignieranforderung. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Kopieren Sie die Zertifikatsignieranforderung in die Zertifizierungsstelle.

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Wechseln Sie zum Zertifizierungsstellencomputer (aktiver Hauptknoten), und signieren Sie das Clientzertifikat.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Kopieren Sie das signierte Clientzertifikat von der Zertifizierungsstelle (aktiver Hauptknoten) auf den Clientcomputer.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Kopieren Sie das Zertifizierungsstellenzertifikat auf den Clientcomputer.

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. Melden Sie sich beim Clientcomputer (Standbyhauptknoten) an, und navigieren Sie zum SSL-Verzeichnis.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Erstellen Sie den Clientspeicher mit einem signierten Zertifikat, und importieren Sie das Zertifizierungsstellenzertifikat in den Keystore und den Truststore auf dem Clientcomputer (hn1):

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Erstellen Sie eine `client-ssl-auth.properties`-Datei auf dem Clientcomputer (hn1). Die Datei sollte folgende Zeilen enthalten:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Überprüfung

Führen Sie diese Schritte auf dem Clientcomputer aus.

> [!Note]
> Wenn HDInsight 4.0 und Kafka 2.1 installiert sind, können Sie Ihr Setup mit dem Konsolenproducer/-consumer überprüfen. Führen Sie andernfalls den Kafka-Producer an Port 9092 aus, und senden Sie Nachrichten an das Thema. Verwenden Sie anschließend den Kafka-Consumer an Port 9093 (nutzt TLS).

### <a name="kafka-21-or-above"></a>Kafka 2.1 oder höher

1. Erstellen Sie ein Thema, sofern noch nicht vorhanden.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Starten Sie den Konsolenproducer, und geben Sie den Pfad zu `client-ssl-auth.properties` als Konfigurationsdatei für den Producer an.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Öffnen Sie eine andere SSH-Verbindung mit dem Clientcomputer, starten Sie den Konsolenconsumer, und geben Sie den Pfad zu `client-ssl-auth.properties` als Konfigurationsdatei für den Consumer an.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1.1

1. Erstellen Sie ein Thema, sofern noch nicht vorhanden.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Starten Sie den Konsolenproducer, und geben Sie den Pfad zu „client-ssl-auth.properties“ als Konfigurationsdatei für den Producer an.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. Öffnen Sie eine andere SSH-Verbindung mit dem Clientcomputer, starten Sie den Konsolenconsumer, und geben Sie den Pfad zu `client-ssl-auth.properties` als Konfigurationsdatei für den Consumer an.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Was ist Apache Kafka in HDInsight?](apache-kafka-introduction.md)
