---
title: Azure Data Box-Problembehandlung für die Verwendung der REST-Schnittstelle | Microsoft-Dokumentation
description: Beschreibt die Behandlung von Problemen mit Azure Data Box, wenn die Datenkopie über die REST-Schnittstelle erfolgt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: c5ceeb2e6419cab7945454087edd4c821db28343
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204219"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Erfahren Sie mehr über das Behandeln von Problemen mit Azure Data Box-Blobspeicher.

Diese Artikel enthält ausführliche Informationen zur Behandlung von Problemen, die auftreten können, wenn Sie Daten über die REST-Schnittstelle von Data Box in den Data Box-Blobspeicher kopieren. Diese Probleme können auftreten, wenn Sie Data Box-Blobspeicher mit anderen Anwendungen oder Clientbibliotheken wie Azure Storage-Explorer, AzCopy oder die Azure Storage-Bibliothek für Python verwenden.

## <a name="errors-seen-in-azure-storage-explorer"></a>In Azure Storage-Explorer angezeigte Fehler

In diesem Abschnitt werden einige der Probleme beschrieben, die bei Verwendung von Azure Storage-Explorer mit Data Box-Blobspeicher auftreten können.

|Fehlermeldung  |Empfohlene Maßnahme |
|---------|---------|
|Untergeordnete Ressourcen können nicht abgerufen werden. Der Wert eines der HTTP-Header weist nicht das richtige Format auf.|Wählen Sie im Menü **Bearbeiten** die Option **Target Azure Stack APIs** (Azure Stack-APIs als Ziel verwenden) aus. <br>Starten Sie Azure Storage-Explorer neu.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Vergewissern Sie sich, dass der Endpunktname `<accountname>.blob.<serialnumber>.microsoftdatabox.com` der Datei „Hosts“ in folgendem Pfad hinzugefügt wurde: <li>`C:\Windows\System32\drivers\etc\hosts` unter Windows oder </li><li> `/etc/hosts` unter Linux.</li>|
|Untergeordnete Ressourcen können nicht abgerufen werden. <br>Details: selbstsigniertes Zertifikat |Importieren Sie das SSL-Zertifikat für Ihr Gerät in Azure Storage-Explorer: <li>Laden Sie das Zertifikat aus dem Azure-Portal herunter. Weitere Informationen finden Sie unter [Herunterladen des Zertifikats](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Wählen Sie im Menü **Bearbeiten** die Option **SSL-Zertifikate** und dann **Zertifikate importieren** aus.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>In AzCopy für Windows auftretende Fehler

In diesem Abschnitt werden einige der Probleme beschrieben, die bei Verwendung von AzCopy für Windows mit Data Box-Blobspeicher auftreten können.

|Fehlermeldung  |Empfohlene Maßnahme |
|---------|---------|
|Der AzCopy-Befehl reagiert eine Minute lang nicht mehr, dann wird dieser Fehler angezeigt: <br>Fehler beim Aufzählen des Verzeichnisses „https://…“ Der Remotename konnte nicht aufgelöst werden: `<accountname>.blob.<serialnumber>.microsoftdatabox.com`.|Vergewissern Sie sich, dass der Endpunktname `<accountname>.blob.<serialnumber>.microsoftdatabox.com` der Datei „Hosts“ unter `C:\Windows\System32\drivers\etc\hosts` hinzugefügt wurde.|
|Der AzCopy-Befehl reagiert eine Minute lang nicht mehr, dann wird dieser Fehler angezeigt: <br>Fehler beim Analysieren des Quellspeicherorts. Die zugrunde liegende Verbindung wurde geschlossen: Es konnte keine Vertrauensstellung für den sicheren SSL/TLS-Kanal eingerichtet werden.|Importieren Sie das SSL-Zertifikat für Ihr Gerät in den Zertifikatspeicher des Systems. Weitere Informationen finden Sie unter [Herunterladen des Zertifikats](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>In AzCopy für Linux auftretende Fehler

In diesem Abschnitt werden einige der Probleme beschrieben, die bei Verwendung von AzCopy für Linux mit Data Box-Blobspeicher auftreten können.

|Fehlermeldung  |Empfohlene Maßnahme |
|---------|---------|
|Der AzCopy-Befehl reagiert 20 Minuten lang nicht mehr, dann wird dieser Fehler angezeigt: <br>Fehler beim Analysieren des Quellspeicherorts `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`. Gerät oder Adresse nicht vorhanden|Vergewissern Sie sich, dass der Endpunktname `<accountname>.blob.<serialnumber>.microsoftdatabox.com` der Datei „Hosts“ unter `/etc/hosts` hinzugefügt wurde.|
|Der AzCopy-Befehl reagiert 20 Minuten lang nicht mehr, dann wird dieser Fehler angezeigt: <br>Fehler beim Analysieren des Quellspeicherorts „…“. Es konnte keine SSL-Verbindung hergestellt werden.|Importieren Sie das SSL-Zertifikat für Ihr Gerät in den Zertifikatspeicher des Systems. Weitere Informationen finden Sie unter [Herunterladen des Zertifikats](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>In der Azure Storage-Bibliothek für Python auftretende Fehler

In diesem Abschnitt werden einige der wichtigsten Probleme bei der Bereitstellung von Data Box Disk unter Verwendung eines Linux-Clients zum Kopieren von Daten beschrieben.

|Fehlermeldung  |Empfohlene Maßnahme |
|---------|---------|
|Der Wert eines der HTTP-Header weist nicht das richtige Format auf. |Die installierte Version der Microsoft Azure Storage-Bibliothek für Python wird von Data Box nicht unterstützt. Unterstützte Versionen finden Sie in den Azure Data Box-Blobspeicheranforderungen.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] …|Legen Sie vor dem Ausführen von Python die Umgebungsvariable REQUESTS_CA_BUNDLE auf den Pfad der Base64-codierten SSL-Zertifikatsdatei fest. (Weitere Informationen zum [Herunterladen des Zertifikats](data-box-deploy-copy-data-via-rest.md#download-certificate).) <br>Beispiel:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Alternativ können Sie das Zertifikat dem Zertifikatspeicher des Systems hinzufügen und dann diese Umgebungsvariable auf den Pfad dieses Speichers festlegen. <br> Beispiel für Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Häufige Fehler

Diese Fehler sind nicht für eine Anwendung spezifisch.

|Fehlermeldung  |Empfohlene Maßnahme |
|---------|---------|
|Bei der Verbindung ist eine Zeitüberschreitung aufgetreten. |Melden Sie sich bei dem Data Box-Gerät an, und vergewissern Sie sich, dass es nicht gesperrt ist. Bei jedem Neustart des Geräts bleibt es gesperrt, bis sich ein Benutzer anmeldet.|

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Systemanforderungen für Data Box-Blobspeicher](data-box-system-requirements-rest.md).
