---
title: Exportieren der Azure Cosmos DB-Emulatorzertifikate
description: Hier erfahren Sie, wie Sie das Azure Cosmos DB-Emulatorzertifikat für die Verwendung mit Java-, Python- und Node.js-Apps exportieren. Die Zertifikate sollten exportiert und für Sprachen und Runtimeumgebungen verwendet werden, in denen der Windows-Zertifikatspeicher nicht verwendet wird.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java, contperf-fy21q1
ms.openlocfilehash: 1c111ca23ce7cf749dc4a7d5ad2ad39756236ab1
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028205"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>Exportieren der Azure Cosmos DB-Emulatorzertifikate für die Verwendung mit Java-, Python- und Node.js-Apps
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Der Azure Cosmos DB-Emulator stellt eine lokale Umgebung bereit, die zu Entwicklungszwecken den Azure Cosmos DB-Dienst emuliert. Der Azure Cosmos-Emulator unterstützt ausschließlich die sichere Kommunikation über TLS-Verbindungen.

Die Zertifikate im lokalen Azure Cosmos DB-Emulator werden bei der ersten Ausführung des Emulators generiert. Es gibt zwei Zertifikate. Ein Zertifikat wird verwendet, um eine Verbindung mit dem lokalen Emulator herzustellen, und das andere dient der Verwaltung der Standardverschlüsselung der Emulatordaten im Emulator. Das Zertifikat, das Sie exportieren, ist das Verbindungszertifikat mit dem Anzeigenamen „DocumentDBEmulatorCertificate“.

Wenn Sie den Emulator zum Entwickeln von Apps in verschiedenen Sprachen wie Java, Python oder Node.js verwenden, müssen Sie das Emulatorzertifikat exportieren und in den erforderlichen Zertifikatspeicher importieren.

Die .NET-Sprachen und die .NET-Runtime verwenden den Windows-Zertifikatspeicher, um eine sichere Verbindung mit dem lokalen Azure Cosmos DB-Emulator herzustellen, wenn die Anwendung auf einem Host mit Windows-Betriebssystem ausgeführt wird. Andere Sprachen bieten andere Methoden für die Verwaltung und Verwendung von Zertifikaten. Beispielsweise verwendet Java einen eigenen [Zertifikatspeicher](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), Python nutzt [Socketwrapper](https://docs.python.org/2/library/ssl.html), und Node.js verwendet [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

In diesem Artikel wird veranschaulicht, wie die TLS/SSL-Zertifikate für die Verwendung in verschiedenen Sprachen und Runtimeumgebungen exportiert werden, die nicht mit dem Windows-Zertifikatspeicher integriert sind. Weitere Informationen zum Emulator finden Sie unter [Verwenden des Azure Cosmos DB-Emulators für Entwicklungs- und Testzwecke](./local-emulator.md).

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>Exportieren des Azure Cosmos DB-TLS/SSL-Zertifikats

Sie müssen das Emulatorzertifikat exportieren, um den Emulatorendpunkt aus Sprach- und Runtimeumgebungen erfolgreich zu verwenden, die nicht mit dem Windows-Zertifikatspeicher integriert sind. Sie können das Zertifikat mithilfe des Zertifikat-Managers von Windows exportieren. Verwenden Sie die folgenden ausführlichen Anweisungen, um das Zertifikat „DocumentDBEmulatorCertificate“ als Base-64-codierte X.509-Zertifikatsdatei zu exportieren:

1. Starten Sie den Windows-Zertifikat-Manager, indem Sie „certlm.msc“ ausführen. Navigieren Sie zum Ordner „Persönlich > Zertifikate“, und öffnen Sie das Zertifikat mit dem Anzeigenamen **DocumentDbEmulatorCertificate**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Exportieren im lokalen Azure Cosmos DB-Emulator, Schritt 1":::

1. Klicken Sie nacheinander auf **Details** und **OK**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Exportieren im lokalen Azure Cosmos DB-Emulator, Schritt 2":::

1. Klicken Sie auf **In Datei kopieren...** .

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Exportieren im lokalen Azure Cosmos DB-Emulator, Schritt 3":::

1. Klicken Sie auf **Weiter**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Exportieren im lokalen Azure Cosmos DB-Emulator, Schritt 4":::

1. Klicken Sie auf **Nein, privaten Schlüssel nicht exportieren** und dann auf **Weiter**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Exportieren im lokalen Azure Cosmos DB-Emulator, Schritt 5":::

1. Klicken Sie auf **Base-64-codiert X.509 (.CER)** und dann auf **Weiter**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Exportieren im lokalen Azure Cosmos DB-Emulator, Schritt 6":::

1. Benennen Sie das Zertifikat. Wählen Sie in diesem Fall den Namen **documentdbemulatorcert**, und klicken Sie auf **Weiter**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Exportieren im lokalen Azure Cosmos DB-Emulator, Schritt 7":::

1. Klicken Sie auf **Fertig stellen**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Exportieren im lokalen Azure Cosmos DB-Emulator, Schritt 8":::

## <a name="use-the-certificate-with-java-apps"></a>Verwenden des Zertifikats mit Java-Apps

Wenn Sie Java- oder MongoDB-Anwendungen ausführen, die den auf Java basierenden Client verwenden, ist es einfacher, das Zertifikat im Java-Standardzertifikatspeicher zu installieren, als die Flags `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` zu übergeben. Die enthaltene Java-Demoanwendung (`https://localhost:8081/_explorer/index.html`) ist beispielsweise vom Standardzertifikatspeicher abhängig.

Befolgen Sie die Anweisungen unter [Hinzufügen eines Stammzertifikats zum Java-ZS-Zertifikatspeicher](/azure/developer/java/sdk/java-sdk-add-certificate-ca-store), um das X.509-Zertifikat in den Java-Standardzertifikatspeicher zu importieren. Denken Sie daran, dass Sie im Verzeichnis *%JAVA_HOME%* arbeiten, wenn Sie „keytool“ ausführen. Nachdem das Zertifikat in den Zertifikatspeicher importiert wurde, können Clients für SQL und die API für MongoDB von Azure Cosmos DB eine Verbindung mit dem Azure Cosmos DB-Emulator herstellen.

Alternativ können Sie das folgende Bash-Skript ausführen, um das Zertifikat zu importieren:

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

Sobald das TLS/SSL-Zertifikat „CosmosDBEmulatorCertificate“ installiert ist, sollte Ihre Anwendung eine Verbindung mit dem lokalen Azure Cosmos DB-Emulator herstellen und diesen verwenden können. Befolgen Sie bei auftretenden Problemen die Schritte im Artikel [Debuggen von SSL/TLS-Verbindungen](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). In den meisten Fällen wird das Zertifikat möglicherweise nicht im Speicher *%JAVA_HOME%/jre/lib/security/cacerts* installiert. Wenn Sie beispielsweise mehrere Java-Versionen installiert haben, verwendet Ihre Anwendung möglicherweise einen anderen cacerts-Speicher als den, den Sie aktualisiert haben.

## <a name="use-the-certificate-with-python-apps"></a>Verwenden des Zertifikats mit Python-Apps

Beim Herstellen einer Verbindung mit dem Emulator in Python-Apps ist die TLS-Überprüfung deaktiviert. Das [Python SDK (Version 2.0.0 oder höher)](sql-api-sdk-python.md) für die SQL-API versucht beim Herstellen einer Verbindung mit dem lokalen Emulator nicht, das TLS/SSL-Zertifikat zu verwenden. Wenn Sie die TLS-Validierung verwenden möchten, befolgen Sie die Beispiele in der Dokumentation zu [Python-Wrappern für Socketobjekte](https://docs.python.org/2/library/ssl.html).

## <a name="how-to-use-the-certificate-in-nodejs"></a>Verwenden des Zertifikats in Node.js

Beim Herstellen einer Verbindung mit dem Emulator in Node.js SDKs ist die TLS-Überprüfung deaktiviert. Das [Node.js SDK (Version 1.10.1 oder höher)](sql-api-sdk-node.md) für die SQL-API versucht beim Herstellen einer Verbindung mit dem lokalen Emulator nicht, das TLS/SSL-Zertifikat zu verwenden. Wenn Sie die TLS-Validierung verwenden möchten, befolgen Sie die Beispiele in der [Node.js-Dokumentation](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="rotate-emulator-certificates"></a>Rotieren von Emulatorzertifikaten

Sie können das erneute Generieren der Emulatorzertifikate erzwingen, indem Sie im Windows-Infobereich auf die Option **Daten zurücksetzen** des ausgeführten Azure Cosmos DB-Emulators klicken. Beachten Sie, dass durch diese Aktion auch alle lokal vom Emulator gespeicherten Daten gelöscht werden.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Zurücksetzen von Daten im lokalen Azure Cosmos DB-Emulator":::

Wenn Sie das Zertifikat im Java-Zertifikatspeicher installiert oder an anderer Stelle verwendet haben, müssen Sie es mithilfe der aktuellen Zertifikate erneut importieren. Die Anwendung kann erst dann eine Verbindung mit dem lokalen Emulator herstellen, wenn Sie die Zertifikate aktualisiert haben.

## <a name="next-steps"></a>Nächste Schritte

* [Befehlszeilen- und PowerShell-Referenz für den Azure Cosmos DB-Emulator](emulator-command-line-parameters.md)
* [Behandeln von Problemen bei Verwendung des Azure Cosmos-Emulators](troubleshoot-local-emulator.md)
