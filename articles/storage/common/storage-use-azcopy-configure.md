---
title: Konfigurieren, Optimieren und Problembehandlung in AzCopy mit Azure Storage | Microsoft-Dokumentation
description: Konfigurieren, Optimieren und Problembehandlung in AzCopy.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a160591ef0a47eed097ce8db373878f32965de9b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245461"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurieren, Optimieren und Problembehandlung in AzCopy

AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Blobs oder Dateien in oder aus einem Speicherkonto zu kopieren. Dieser Artikel hilft Ihnen dabei, erweiterte Konfigurationsaufgaben durchzuführen, und hilft Ihnen bei der Behandlung von Problemen, die bei der Verwendung von AzCopy auftreten können.

> [!NOTE]
> Wenn Sie nach Inhalten suchen, die Ihnen bei den ersten Schritten mit AzCopy helfen, sehen Sie in den folgenden Artikeln nach:
> - [Übertragen von Daten mit AzCopy v10](storage-use-azcopy-v10.md)
> - [Übertragen von Daten mit AzCopy und Blob Storage](storage-use-azcopy-blobs.md)
> - [Übertragen von Daten mit AzCopy und Dateispeicher](storage-use-azcopy-files.md)
> - [Kopieren von Daten aus Amazon S3-Buckets mit AzCopy](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Konfigurieren von Proxyeinstellungen

Um die Proxyeinstellungen für AzCopy zu konfigurieren, legen Sie die Umgebungsvariable `https_proxy` fest.

| Betriebssystem | Get-Help  |
|--------|-----------|
| **Windows** | `set https_proxy=<proxy IP>:<proxy port>` |
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

AzCopy unterstützt zurzeit keine Proxys, für die eine Authentifizierung mit NTLM oder Kerberos erforderlich ist.

## <a name="optimize-throughput"></a>Optimieren des Durchsatzes

Legen Sie die Umgebungsvariable `AZCOPY_CONCURRENCY_VALUE` fest, um die Anzahl gleichzeitiger Anforderungen zu konfigurieren und die Durchsatzleistung und Ressourcennutzung zu steuern. Wenn Ihr Computer über weniger als 5 CPUs verfügt, wird der Wert dieser Variablen auf `32` festgelegt. Andernfalls ist der Standardwert gleich 16, multipliziert mit der Anzahl der CPUs. Der maximale Standardwert dieser Variablen ist `300`, aber Sie können diesen Wert manuell höher oder niedriger festlegen.

| Betriebssystem | Get-Help  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Verwenden Sie `azcopy env`, um den aktuellen Wert dieser Variablen zu überprüfen.  Wenn der Wert leer ist, ist die Variable `AZCOPY_CONCURRENCY_VALUE` auf den Standardwert `300` festgelegt.

## <a name="change-the-location-of-the-log-files"></a>Ändern des Speicherorts der Protokolldateien

Standardmäßig befinden sich die Protokolldateien im Ordner `%USERPROFILE\\.azcopy` unter Windows bzw. im Ordner `$HOME\\.azcopy` unter Mac und Linux. Sie können diesen Speicherort bei Bedarf mit diesen Befehlen ändern.

| Betriebssystem | Get-Help  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Verwenden Sie `azcopy env`, um den aktuellen Wert dieser Variablen zu überprüfen. Wenn der Wert leer ist, werden Protokolle an den Standardspeicherort geschrieben.

## <a name="change-the-default-log-level"></a>Ändern der Standardprotokollebene

Standardmäßig ist die AzCopy-Protokollebene auf `INFO` festgelegt. Wenn Sie die Ausführlichkeit des Protokolls verringern möchten, um Speicherplatz auf dem Datenträger zu sparen, überschreiben Sie die Einstellung mithilfe der Option ``--log-level``. 

Verfügbare Protokollierebenen sind: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC` und `FATAL`.

## <a name="troubleshoot-issues"></a>Behandeln von Problemen

AzCopy erstellt Protokoll- und Plandateien für jeden Auftrag. Sie können die Protokolle verwenden, um potenzielle Probleme zu untersuchen und zu beheben. 

Die Protokolle enthalten den Status des Fehlers (`UPLOADFAILED`, `COPYFAILED` und `DOWNLOADFAILED`), den vollständigen Pfad und die Ursache des Fehlers.

Standardmäßig befinden sich die Protokoll- und Plandateien im Ordner `%USERPROFILE\\.azcopy` unter Windows oder in `$HOME\\.azcopy` unter Mac und Linux.

> [!IMPORTANT]
> Geben Sie beim Senden einer Anforderung an den Microsoft-Support (oder bei der Problembehandlung unter Einbeziehung eines Drittanbieters) die bearbeitete Version des Befehls an, den Sie ausführen möchten. So wird sichergestellt, dass die SAS nicht versehentlich offengelegt wird. Die editierte Version steht am Anfang der Protokolldatei.

### <a name="review-the-logs-for-errors"></a>Überprüfen der Protokolle auf Fehler

Der folgende Befehl ruft alle Fehler mit dem Status `UPLOADFAILED` aus dem Protokoll `04dc9ca9-158f-7945-5933-564021086c79` ab:

**Windows**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Anzeigen und Fortsetzen von Aufträgen

Jeder Übertragungsvorgang erstellt einen AzCopy-Auftrag. Verwenden Sie den folgenden Befehl, um den Verlauf von Aufträgen anzuzeigen:

```
azcopy jobs list
```

Verwenden Sie den folgenden Befehl, um die Auftragsstatistiken anzuzeigen:

```
azcopy jobs show <job-id>
```

Um die Übertragungen nach ihrem Status zu filtern, verwenden Sie den folgenden Befehl:

```
azcopy jobs show <job-id> --with-status=Failed
```

Verwenden Sie den folgenden Befehl, um einen fehlgeschlagenen bzw. abgebrochenen Auftrag fortzusetzen. Dieser Befehl verwendet seinen Bezeichner zusammen mit dem SAS-Token, da er aus Sicherheitsgründen nicht persistent ist:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

Wenn Sie einen Auftrag fortsetzen, überprüft AzCopy die Plandatei. In der Plandatei werden alle Dateien aufgelistet, die zur Verarbeitung identifiziert wurden, als der Auftrag erstmalig erstellt wurde. Wenn Sie einen Auftrag fortsetzen, versucht AzCopy, alle Dateien zu übertragen, die in der Plandatei aufgeführt sind, die nicht bereits übertragen wurden.