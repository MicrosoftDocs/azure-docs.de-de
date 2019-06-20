---
title: 'Microsoft Genomics: Häufig gestellte Fragen | Microsoft-Dokumentation'
titleSuffix: Azure
description: Antworten auf häufig gestellte Fragen zu Microsoft Genomics.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 2bcbf9d145d9b8b5a3c42893235906d24516405c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65792634"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Häufig gestellte Fragen

In diesem Artikel finden Sie die wichtigsten Fragen im Zusammenhang mit Microsoft Genomics. Weitere Informationen zum Microsoft Genomics-Dienst finden Sie unter [Was ist Microsoft Genomics?](overview-what-is-genomics.md) Weitere Informationen zur Problembehandlung finden Sie in unserem [Leitfaden zur Problembehandlung](troubleshooting-guide-genomics.md). 

## <a name="what-is-the-microsoft-genomics-service-gatk-4-promotion"></a>Worum geht es bei der Promotion für den Microsoft Genomics-Dienst GATK 4?
Bis zum 30. Juni 2019 werden für den Microsoft Genomics-Dienst 20 WGS-Ausführungen mit GATK4 kostenlos angeboten. Registrieren Sie sich [hier](https://aka.ms/msgatk4), um dieses Angebot zu nutzen. 

### <a name="what-are-the-common-issues-i-might-encounter-while-running-the-microsoft-genomics-service-gatk4-promotion"></a>Welche allgemeinen Probleme können bei Ausführung der GATK4-Promotion für den Microsoft Genomics-Dienst auftreten?
Im Folgenden werden mögliche allgemeine Fehler sowie die empfohlenen Lösungen aufgeführt:

| **Meldung**                                                                                                                                                                                    | **Ursache**                                                                                                    | **Lösung**                                                                                                                                                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `gatk4-promo` ist für Ihr Konto nicht aktiviert. Weitere Informationen finden Sie unter https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics.                               | Sie versuchen, GATK4-Workflows mit dem Microsoft Genomics-Dienst ohne Aktivierung auszuführen.       | [Hier](https://aka.ms/msgatk4) finden Sie eine Anleitung zur Aktivierung Ihres Kontos. Beachten Sie, dass die Testversion am 30. Juni 2019 abläuft. Nach diesem Datum können Sie Ihr Konto für die Ausführungen im Rahmen der Promotion nicht mehr aktivieren. |
| Vielen Dank, dass Sie `gatk4-promo` getestet haben. Ihr Testzeitraum ist abgelaufen. Weitere Informationen finden Sie hier: https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics                  | Die GATK4-Testversion ist am Ende des Kalenderjahrs abgelaufen, und Sie versuchen, `gatk4-promo` als process_name aufzurufen.  | Ändern Sie den Parameter „process_name“ in `gatk4` anstelle von `gatk4-promo`. Dies ist die offizielle GATK4-Version, und der Workflow wird in Rechnung gestellt, wenn Sie diesen Parameter verwenden.                                         |
| Vielen Dank, dass Sie `gatk4-promo` getestet haben. Sie haben alle zugeordneten Ausführungen verwendet. Weitere Informationen finden Sie unter https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics. | Sie haben all Ihre 20 Ausführungen im Rahmen der Promotion für GATK4 erfolgreich übermittelt.                               | Senden Sie neue GATK4-Ausführungen, indem Sie das Argument „process_name“ auf `gatk4` anstelle von `gatk4-promo` festlegen. Ihr Workflow wird in Rechnung gestellt, wenn Sie diesen Parameter verwenden.                                                          |        


## <a name="can-i-run-gatk4-workflows-on-microsoft-genomics-without-signing-up-for-the-gatk4-promotion"></a>Kann ich GATK4-Workflows in Microsoft Genomics ausführen, ohne mich für die GATK4-Promotion zu registrieren?
Ja. Legen Sie in der Datei „config.txt“ für den Microsoft Genomics-Dienst den process_name als `gatk4` fest. Beachten Sie, dass die Abrechnung zu den regulären Preisen erfolgt und dass die 20 kostenlosen Ausführungen nicht auf Ihr Microsoft Genomics-Konto angewendet werden.



## <a name="what-is-the-sla-for-microsoft-genomics"></a>Wie lautet die Vereinbarung zum Servicelevel für Microsoft Genomics?
Wir garantieren, dass der Microsoft Genomics-Dienst 99,9% der Zeit für den Empfang von Workflow-API-Anforderungen zur Verfügung steht. Weitere Informationen finden Sie unter [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Wie wird die Nutzung von Microsoft Genomics auf meiner Rechnung ausgewiesen?
Microsoft Genomics wird nach der Anzahl der pro Workflow verarbeiteten Gigabasen abgerechnet. Weitere Informationen finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Wo finde ich eine Liste aller möglichen Befehle und Argumente für den `msgen`-Client?
Sie erhalten eine vollständige Liste der verfügbaren Befehle und Argumente, indem Sie `msgen help` ausführen. Wenn keine weiteren Argumente angegeben werden, wird eine Liste der verfügbaren Hilfeabschnitte angezeigt, jeweils einer für `submit`, `list`, `cancel` und `status`. Um Hilfe für einen bestimmten Befehl zu erhalten, geben Sie `msgen help command` ein. Beispielsweise listet `msgen help submit` alle Übermittlungsoptionen auf.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Was sind die am häufigsten verwendeten Befehle für den `msgen`-Client?
Zu den am häufigsten verwendeten Befehlsargumenten für den `msgen`-Client zählen u.a.: 

 |**Befehl**          |  **Feldbeschreibung** |
 |:--------------------|:-------------         |
 |`list`               |Gibt eine Liste der Aufträge zurück, die Sie gesendet haben. Argumente finden Sie unter `msgen help list`.  |
 |`submit`             |Übermittelt eine Workflowanforderung an den Dienst. Argumente finden Sie unter `msgen help submit`.|
 |`status`             |Gibt den Status des Workflows zurück, der von `--workflow-id` angegeben wird. Siehe auch `msgen help status`. |
 |`cancel`             |Sendet eine Anforderung zum Abbrechen der Verarbeitung des Workflows, der von `--workflow-id` angegeben wird. Siehe auch `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Wo erhalte ich den Wert für `--api-url-base`?
Wechseln Sie zum Azure-Portal, und öffnen Sie die Seite Ihres Genomics-Kontos. Klicken Sie unter der Überschrift **Verwaltung** auf **Zugriffsschlüssel**. Dort finden Sie die API-URL und Ihre Zugriffsschlüssel.

## <a name="where-do-i-get-the-value-for---access-key"></a>Wo erhalte ich den Wert für `--access-key`?
Wechseln Sie zum Azure-Portal, und öffnen Sie die Seite Ihres Genomics-Kontos. Klicken Sie unter der Überschrift **Verwaltung** auf **Zugriffsschlüssel**. Dort finden Sie die API-URL und Ihre Zugriffsschlüssel.

## <a name="why-do-i-need-two-access-keys"></a>Warum benötige ich zwei Zugriffsschlüssel?
Sie benötigen zwei Zugriffsschlüssel, wenn Sie diese aktualisieren (neu erstellen) möchten, ohne die Nutzung des Diensts zu unterbrechen. Wenn Sie beispielsweise den ersten Schlüssel aktualisieren möchten, müssen alle neuen Workflows den zweiten Schlüssel verwenden. Warten Sie dann auf den Abschluss aller Workflows, die den ersten Schlüssel verwenden, bevor Sie den ersten Schlüssel aktualisieren.

## <a name="do-you-save-my-storage-account-keys"></a>Speichern Sie meine Speicherkontoschlüssel?
Ihr Speicherkontoschlüssel wird verwendet, um kurzfristige Zugriffstoken für den Microsoft Genomics-Dienst zu erstellen, um Ihre Eingabedateien zu lesen und die Ausgabedateien zu schreiben. Die Standardgültigkeitsdauer von Token beträgt 48 Stunden. Die Tokendauer kann mit der Option `-sas/--sas-duration` des „submit“-Befehls geändert werden. Der Wert wird in Stunden angegeben.

## <a name="what-genome-references-can-i-use"></a>Welche Genomreferenzen kann ich verwenden?

Diese Referenzen werden unterstützt:

 |Verweis              | Wert von `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (keine alternative Analyse) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Wie formatiere ich meine Befehlszeilenargumente als Konfigurationsdatei? 

msgen versteht Konfigurationsdateien im folgendem Format:
* Alle Optionen werden als Schlüssel-Wert-Paare bereitgestellt, wobei die Werte durch einen Doppelpunkt von den Schlüsseln getrennt sind.
  Leerraum wird ignoriert.
* Zeilen, die mit `#` beginnen, werden ignoriert.
* Befehlszeilenargumente im Langformat können in Schlüssel umgewandelt werden, indem die führenden Bindestriche entfernt und Bindestriche zwischen Wörtern durch Unterstriche ersetzt werden. Hier einige Konvertierungsbeispiele:

  |Befehlszeilenargument            | Konfigurationsdateizeile |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base: https://url*    |
  |`-k/--access-key KEY`            | *access_key:KEY*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie für die ersten Schritte mit Microsoft Genomics die folgenden Ressourcen:
- Steigen Sie ein, indem Sie Ihren ersten Workflow über den Microsoft Genomics-Dienst ausführen. [Ausführen eines Workflows über den Microsoft Genomics-Dienst](quickstart-run-genomics-workflow-portal.md)
- Übermitteln Sie Ihre eigenen Daten zur Verarbeitung durch den Microsoft Genomics-Dienst: [Paired FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ oder BAM](quickstart-input-multiple.md) 

