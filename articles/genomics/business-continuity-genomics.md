---
title: Übersicht über Geschäftskontinuität
titleSuffix: Microsoft Genomics
description: Diese Übersicht beschreibt die Funktionen, die Microsoft Genomics für Geschäftskontinuität und Notfallwiederherstellung bereitstellt.
keywords: Geschäftskontinuität, Notfallwiederherstellung
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 3097041c96f70dda384f0b116bbbc9bc9130be4d
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395749"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Übersicht über Geschäftskontinuität bei Verwenden von Microsoft Genomics
Diese Übersicht beschreibt die Funktionen, die Microsoft Genomics für Geschäftskontinuität und Notfallwiederherstellung bereitstellt. Erfahren Sie mehr über Optionen zur Wiederherstellung nach Störfällen, wie z. B. Ausfall einer Azure-Region, die zu Datenverlust führen können. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics-Funktionen zur Unterstützung von Geschäftskontinuität 
Wenngleich selten, kann es in einem Azure-Rechenzentrum zu einem Ausfall kommen, der zu einer Betriebsunterbrechung führen kann, die einige Minuten bis wenige Stunden dauern kann. Bei einem Ausfall werden alle derzeit im Rechenzentrum laufenden Aufträge abgebrochen, und der Microsoft Genomics-Dienst leitet dann die Aufträge nicht automatisch an eine sekundäre Region weiter. 

* Eine Möglichkeit ist, einfach zu warten, bis das Rechenzentrum wieder online ist, nachdem sein Ausfall behoben wurde. Wenn der Ausfall kurz ist, erkennt der Microsoft Genomics-Dienst automatisch die fehlerhaften Aufträge, und der Workflow wird automatisch neu gestartet.

* Eine weitere Möglichkeit besteht darin, den Workflow proaktiv in eine andere Datenregion zu übertragen. Microsoft Genomics stellt Instanzen in mehreren [Azure-Regionen](https://azure.microsoft.com/regions/services/) bereit, wobei jede regionsspezifische Instanz unabhängig ist. Wenn bei einer der Microsoft Genomics-Instanzen ein regionaler Ausfall eintritt, werden andere Regionen, in denen Instanzen von Microsoft Genomics ausgeführt werden, weiterhin Aufträge verarbeiten. Diese Umstellung auf eine andere Region erfolgt unter der Kontrolle des Benutzers und ist jederzeit möglich.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Manuelles Failover von Microsoft Genomics-Workflows in eine anderen Region
Bei einem regionalen Ausfall eines Rechenzentrums können Sie Microsoft Genomics-Workflows in eine sekundäre Region übermitteln, und zwar basierend auf Ihren individuellen Datenhoheits- und Geschäftskontinuitätsanforderungen. Für ein manuelles Failover von Microsoft Genomics-Workflows verwenden Sie ein anderes regionsspezifisches Genomics-Konto und übermitteln den Auftrag mit den entsprechenden regionsspezifischen Anmeldeinformationen für Genomics und das Speicherkonto.

Insbesondere müssen Sie diese Schritte ausführen:
* Erstellen Sie im Azure-Portal ein Genomics-Konto in der sekundären Region. 
* Migrieren Sie Ihre Eingabedaten in ein Speicherkonto in der sekundären Region, und richten Sie einen Ausgabeordner in der sekundären Region ein.
* Übermitteln Sie den Workflow in die sekundäre Region.

Nachdem die ursprüngliche Region wiederhergestellt wurde, migriert der Microsoft Genomics-Dienst die Daten aus der sekundären Region nicht zurück in die ursprüngliche Region. Sie können wählen, ob Sie die Eingabe- und Ausgabedateien aus der sekundären Region zurück in die ursprüngliche Region verschieben möchten.  Wenn Sie sich dafür entscheiden, die Daten zu verschieben, erfolgt dies außerhalb des Genomics-Diensts, weshalb alle Kosten im Zusammenhang mit der Datenverschiebung in Ihre Zuständigkeit fallen. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Vorbereiten eines möglichen regionsspezifischen Ausfalls
Wenn Sie sich bei einem Ausfall des Rechenzentrums eine schnellere Wiederherstellung wünschen, können Sie einige Schritte unternehmen, um die Zeit zu verkürzen, die Sie benötigen, um Ihre Microsoft Genomics-Workflows manuell an eine sekundäre Region weiterzuleiten:

* Bestimmen Sie eine geeignete sekundäre Region, und erstellen Sie in dieser Region proaktiv ein Genomics-Konto.
* Duplizieren Sie die Daten in der primären und sekundären Region, damit Ihre Daten in der sekundären Region sofort verfügbar sind. Dies kann manuell oder über die Funktion [georedundanter Speicher](../storage/common/storage-redundancy.md) erfolgen, die in Azure Storage verfügbar ist. 

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie sich mit den Optionen für Geschäftskontinuität und Notfallwiederherstellung bei Verwendung des Microsoft Genomics-Diensts vertraut gemacht. Weitere Informationen zur Geschäftskontinuität und Notfallwiederherstellung in Azure im Allgemeinen finden Sie unter [Technischer Leitfaden zur Resilienz in Azure](/azure/architecture/resiliency/recovery-loss-azure-region).