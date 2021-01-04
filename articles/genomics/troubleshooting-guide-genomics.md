---
title: Handbuch zur Problembehandlung
titleSuffix: Microsoft Genomics
description: Erfahren Sie etwas über Strategien für die Problembehandlung bei der Verwendung von Microsoft Genomics, einschließlich der Fehlermeldungen und ihrer Behebung.
keywords: troubleshooting, error, debugging
services: genomics
author: ruchir
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: c508c10d619cde1a16d89b446c5cfd1a3ce81daf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023822"
---
# <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung

Hier finden Sie Tipps zur Problembehandlung für einige der häufigsten Probleme, die bei der Verwendung des Microsoft Genomics-Diensts (MSGEN) auftreten können.

 Häufig gestellte Fragen (FAQ), die nicht im Zusammenhang mit der Problembehandlung stehen, finden Sie unter [Häufig gestellte Fragen](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Schritt 1: Suchen nach Fehlercodes, die dem Workflow zugeordnet sind

Gehen Sie folgendermaßen vor, um die Fehlermeldungen im Zusammenhang mit dem Workflow zu ermitteln:

1. Verwenden Sie die Befehlszeile, und geben Sie `msgen status` ein.
2. Untersuchen Sie den Inhalt der Datei „standardoutput.txt“.

### <a name="1-using-the-command-line-msgen-status"></a>1. Verwenden von `msgen status` in der Befehlszeile

```bash
msgen status -u URL -k KEY -w ID 
```




Es gibt drei erforderliche Argumente:

* URL: Der Basis-URI für die API
* KEY: Der Zugriffsschlüssel für Ihr Genomics-Konto
    * Navigieren Sie für die Suche nach URL und KEY zum Azure-Portal, und öffnen Sie die Seite Ihres Microsoft Genomics-Kontos. Klicken Sie unter der Überschrift **Verwaltung** auf **Zugriffsschlüssel**. Dort finden Sie die API-URL und Ihre Zugriffsschlüssel.

  
* ID: Die Workflow-ID
    * Um Ihre Workflow-ID zu ermitteln, geben Sie den Befehl `msgen list` ein. Unter der Voraussetzung, dass Ihre Konfigurationsdatei die URL und Ihre Zugriffsschlüssel enthält und sich am selben Speicherort wie die Datei „msgen.exe“ befindet, sieht der Befehl folgendermaßen aus: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Die Ausgabe dieses Befehls sieht folgendermaßen aus:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  Alternativ können Sie den Pfad zur Konfigurationsdatei einschließen, anstatt URL und KEY direkt einzugeben. Wenn Sie diese Argumente in die Befehlszeile sowie in die Konfigurationsdatei einschließen, besitzen die Befehlszeilenargumente Vorrang.  

Für die Workflow-ID 1001 und die Datei „config.txt“, die sich im gleichen Pfad wie die ausführbare msgen-Datei befindet, sieht der Befehl folgendermaßen aus:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2.  Untersuchen des Inhalts der Datei „standardoutput.txt“ 
Suchen Sie nach dem Ausgabecontainer für den betreffenden Workflow. MSGEN erstellt nach jeder Ausführung des Workflows einen Ordner `[workflowfilename].logs.zip`. Entzippen Sie den Ordner, um seinen Inhalt anzuzeigen:

* outputFileList.txt: Eine Liste der während des Workflows erstellten Ausgabedateien
* standarderror.txt: Diese Datei ist leer.
* „standardoutput.txt“: protokolliert alle Statusmeldungen auf oberster Ebene (einschließlich Fehler), die beim Ausführen des Workflows aufgetreten sind.
* GATK-Protokolldateien: Alle anderen Dateien im Ordner `logs`

Überprüfen Sie zur Problembehandlung den Inhalt der Datei „standardoutput.txt“, und notieren Sie sich die aufgetretenen Fehlermeldungen.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Schritt 2: Anwenden der empfohlenen Schritte für häufig auftretende Fehler

In diesem Abschnitt werden die häufigsten Fehler kurz vorgestellt, die vom Microsoft Genomics-Dienst (msgen) ausgegeben werden, sowie die Strategien, mit denen Sie sie beheben können. 

Der Microsoft Genomics-Dienst (msgen) kann die folgenden zwei Arten von Fehlern auslösen:

1. Interne Dienstfehler: Fehler, die dienstintern sind und nicht durch Korrigieren von Parametern oder Eingabedateien behoben werden können. Manchmal kann ein erneutes Übermitteln des Workflows diese Fehler beheben.
2. Eingabefehler: Fehler, die durch Verwenden der richtigen Argumente oder Korrigieren von Dateiformaten behoben werden können.

### <a name="1-internal-service-errors"></a>1. Interne Dienstfehler

Ein interner Dienstfehler ist vom Benutzer nicht behebbar. Sie können den Workflow erneut übermitteln. Wenn das nicht hilfreich ist, wenden Sie sich an den Microsoft Genomics-Support.

| Fehlermeldung                                                                                                                            | Empfohlene Schritte zur Problembehandlung                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ein interner Fehler ist aufgetreten. Versuchen Sie, den Workflow erneut zu übermitteln. Wenn dieser Fehler erneut auftritt, wenden Sie sich an den Microsoft Genomics-Support, um Unterstützung zu erhalten. | Übermitteln Sie den Workflow erneut. Wenden Sie sich an den Microsoft Genomics-Support, wenn das Problem weiterhin besteht, indem Sie ein Support[ticket](file-support-ticket-genomics.md ) erstellen, um Unterstützung zu erhalten. |

### <a name="2-input-errors"></a>2. Eingabefehler

Diese Fehler können vom Benutzer behoben werden. Basierend auf dem Dateityp und dem Fehlercode gibt der Microsoft Genomics-Dienst unterschiedliche Fehlercodes aus. Führen Sie die unten aufgeführten empfohlenen Schritte zur Problembehandlung aus.

| Dateityp | Fehlercode | Fehlermeldung                                                                           | Empfohlene Schritte zur Problembehandlung                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Any          | 701        | Read [readId] has [numberOfBases] bases, but the limit is [maxReadLength] (Der Lesevorgang [ReadId] weist [NumberOfBases] Basen auf, der Grenzwert ist aber [MaxReadLength]).           | Der häufigste Grund für diesen Fehler ist eine Dateibeschädigung, die zur Verkettung von zwei Lesevorgängen führt. Überprüfen Sie Ihre Eingabedateien. |
| BAM          | 200        |   Unable to read file '[yourFileName]' (Die Datei '[ihrDateiname]' kann nicht gelesen werden).                                                                                       | Check the format of the BAM file (Überprüfen Sie das Format der BAM-Datei). Übermitteln Sie den Workflow mit einer ordnungsgemäß formatierten Datei erneut.                                                                           |
| BAM          | 201        |  Unable to read BAM file [File_name] (Die BAM-Datei [Dateiname] kann nicht gelesen werden).                                                                                      |Check the format of the BAM file (Überprüfen Sie das Format der BAM-Datei).  Übermitteln Sie den Workflow mit einer ordnungsgemäß formatierten Datei.                                                                            |
| BAM          | 202        | Unable to read BAM file [File_name] (Die BAM-Datei [Dateiname] kann nicht gelesen werden). File too small and missing header (Die Datei ist zu klein, und der Dateiheader fehlt).                                                                                        | Check the format of the BAM file (Überprüfen Sie das Format der BAM-Datei).  Übermitteln Sie den Workflow mit einer ordnungsgemäß formatierten Datei.                                                                            |
| BAM          | 203        |   Unable to read BAM file [File_name] (Die BAM-Datei [Dateiname] kann nicht gelesen werden). Header of file was corrupt (Der Header der Datei war fehlerhaft).                                                                                      |Check the format of the BAM file (Überprüfen Sie das Format der BAM-Datei).  Übermitteln Sie den Workflow mit einer ordnungsgemäß formatierten Datei.                                                                           |
| BAM          | 204        |    Unable to read BAM file [File_name] (Die BAM-Datei [Dateiname] kann nicht gelesen werden). Header of file was corrupt (Der Header der Datei war fehlerhaft).                                                                                     | Check the format of the BAM file (Überprüfen Sie das Format der BAM-Datei).  Übermitteln Sie den Workflow mit einer ordnungsgemäß formatierten Datei.                                                                           |
| BAM          | 205        |    Unable to read BAM file [File_name] (Die BAM-Datei [Dateiname] kann nicht gelesen werden). Header of file was corrupt (Der Header der Datei war fehlerhaft).                                                                                     | Check the format of the BAM file (Überprüfen Sie das Format der BAM-Datei).  Übermitteln Sie den Workflow mit einer ordnungsgemäß formatierten Datei.                                                                            |
| BAM          | 206        |   Unable to read BAM file [File_name] (Die BAM-Datei [Dateiname] kann nicht gelesen werden). Header of file was corrupt (Der Header der Datei war fehlerhaft).                                                                                      | Check the format of the BAM file (Überprüfen Sie das Format der BAM-Datei).  Übermitteln Sie den Workflow mit einer ordnungsgemäß formatierten Datei.                                                                            |
| BAM          | 207        |  Unable to read BAM file [File_name] (Die BAM-Datei [Dateiname] kann nicht gelesen werden). File truncated near offset [offset] (Die Datei wurde in der Nähe von Offset [Offset] abgeschnitten).                                                                                       | Check the format of the BAM file (Überprüfen Sie das Format der BAM-Datei).  Übermitteln Sie den Workflow mit einer ordnungsgemäß formatierten Datei.                                                                            |
| BAM          | 208        |   Invalid BAM file (Ungültige BAM-Datei). The ReadID [Read_Id] has no sequence in file [File_name] (Die ReadID [Read_Id] enthält in der Datei [Dateiname] keine Reihenfolge).                                                                                      | Check the format of the BAM file (Überprüfen Sie das Format der BAM-Datei).  Übermitteln Sie den Workflow mit einer ordnungsgemäß formatierten Datei.                                                                             |
| FASTQ        | 300        |  Unable to read FASTQ file (Die FASTQ-Datei kann nicht gelesen werden). [File_name] doesn't end with a newline ([Dateiname] endet nicht mit einer neuen Zeile).                                                                                     | Korrigieren Sie das Format der FASTQ-Datei, und übermitteln Sie den Workflow erneut.                                                                           |
| FASTQ        | 301        |   Unable to read FASTQ file [File_name] (Die FASTQ-Datei [Dateiname] kann nicht gelesen werden). FASTQ record is larger than buffer size at offset: [_offset] (Der FASTQ-Datensatz ist größer als die Puffergröße am Offset: [_offset]).                                                                                      | Korrigieren Sie das Format der FASTQ-Datei, und übermitteln Sie den Workflow erneut.                                                                         |
| FASTQ        | 302        |     FASTQ Syntax error (FASTQ-Syntaxfehler). File [File_name] has a blank line (Die Datei [Dateiname] weist eine leere Zeile auf).                                                                                    | Korrigieren Sie das Format der FASTQ-Datei, und übermitteln Sie den Workflow erneut.                                                                         |
| FASTQ        | 303        |       FASTQ Syntax error (FASTQ-Syntaxfehler). File[File_name] has an invalid starting character at offset: [_offset],  line type: [line_type], character: [_char] (Die Datei [Dateiname] weist ein ungültiges Startzeichen am Offset auf: [_offset], Zeilentyp: [zeilen_typ], Zeichen: [_char]).                                                                                  | Korrigieren Sie das Format der FASTQ-Datei, und übermitteln Sie den Workflow erneut.                                                                         |
| FASTQ        | 304      |  FASTQ Syntax error at readID [_ReadID] (FASTQ-Syntaxfehler bei ReadID [_ReadID]).  First read of batch doesn’t have readID ending in /1 in file [File_name] (Erster Lesevorgang für Batch hat keine ReadID, die auf /1 in Datei [Dateiname] endet).                                                                                       | Korrigieren Sie das Format der FASTQ-Datei, und übermitteln Sie den Workflow erneut.                                                                         |
| FASTQ        | 305        |  FASTQ Syntax error at readID [_readID] (FASTQ-Syntaxfehler bei ReadID [_readID]). Second read of batch doesn’t have readID ending in /2 in file [File_name] (Zweiter Lesevorgang für Batch hat keine ReadID, die auf /2 in Datei [Dateiname] endet).                                                                                      | Korrigieren Sie das Format der FASTQ-Datei, und übermitteln Sie den Workflow erneut.                                                                          |
| FASTQ        | 306        |  FASTQ Syntax error at readID [_ReadID] (FASTQ-Syntaxfehler bei ReadID [_ReadID]). First read of pair doesn’t have readID ending in /1 in file [File_name] (Erster Lesevorgang des Paars hat keine ReadID, die auf /1 in Datei [Dateiname] endet).                                                                                       | Korrigieren Sie das Format der FASTQ-Datei, und übermitteln Sie den Workflow erneut.                                                                          |
| FASTQ        | 307        |   FASTQ Syntax error at readID [_ReadID] (FASTQ-Syntaxfehler bei ReadID [_ReadID]). ReadID doesn’t end with /1 or/2 (ReadID endet nicht auf /1 oder /2). File [File_name] can't be used as a paired FASTQ file (Datei [Dateiname] kann nicht als gekoppelte FASTQ-Datei verwendet werden).                                                                                      |Korrigieren Sie das Format der FASTQ-Datei, und übermitteln Sie den Workflow erneut.                                                                          |
| FASTQ        | 308        |  FASTQ read error (FASTQ-Lesefehler). Reads of both ends responded differently (Lesevorgänge an beiden Enden haben unterschiedlich geantwortet). Did you choose the correct FASTQ files? (Haben Sie die richtigen FASTQ-Dateien ausgewählt?)                                                                                       | Korrigieren Sie das Format der FASTQ-Datei, und übermitteln Sie den Workflow erneut.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Schritt 3: Kontaktaufnahme mit dem Microsoft Genomics-Support

Wenn bei der Auftragsausführung weiterhin Fehler auftreten oder wenn Sie weitere Fragen haben, wenden Sie sich über das Azure-Portal an den Microsoft Genomics-Support. Weitere Informationen zum Übermitteln einer Supportanfrage finden Sie [hier](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie häufige Probleme mit dem Microsoft Genomics-Dienst behandeln und lösen. Weitere Informationen und allgemeinere häufig gestellte Fragen finden Sie unter [Häufig gestellte Fragen](frequently-asked-questions-genomics.md). 
