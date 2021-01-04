---
title: SAS-Fehlermeldungen von virtuellen Computern – Azure Marketplace
description: Häufig gestellte Fragen bei der Arbeit mit Shared Access Signatures (SAS).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 1c89887117c10ca77ec4c04b3adbe3e2d9923479
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126837"
---
# <a name="virtual-machine-sas-failure-messages"></a>SAS-Fehlermeldungen von virtuellen Computern

Unten sind einige häufige Probleme im Zusammenhang mit der Verwendung von Shared Access Signatures sowie Lösungsvorschläge aufgeführt. Shared Access Signatures dienen zur Identifizierung und gemeinsamen Verwendung der hochgeladenen VHDs für Ihre Lösung.

| Problem | Fehlermeldung | Fix |
| --------- | ------------------- | ------- |
| *Fehler beim Kopieren von Images* |  |  |
| „?“ wurde im SAS-URI nicht gefunden. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualisieren Sie den SAS-URI mit den empfohlenen Tools. |
| Die Parameter „st“ und „se“ sind im SAS-URI nicht enthalten. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualisieren Sie den SAS-URI mit geeigneten Werten für **Startdatum** und **Enddatum**. |
| „sp=rl“ ist im SAS-URI nicht enthalten. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualisieren Sie den SAS-URI mit den Berechtigungen `Read` und `List`. |
| Der VHD-Name im SAS-URI enthält Leerzeichen. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Entfernen Sie die Leerzeichen aus dem SAS-URI. |
| SAS-URI-Autorisierungsfehler | `Failure: Copying Images. Not able to download blob due to authorization error.` | Überprüfen und korrigieren Sie das SAS-URI-Format. Generieren Sie ihn bei Bedarf neu. |
| Die Parameter „st“ und „se“ des SAS-URI verfügen nicht über eine vollständige Angabe für Datum und Uhrzeit. | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | Für den SAS-URI muss für die Parameter **Startdatum** und **Enddatum** (Teilzeichenfolgen `st` und `se`) das vollständige Format mit Datum und Uhrzeit verwendet werden, z. B. `11-02-2017T00:00:00Z`. Gekürzte Versionen sind ungültig (bei einigen Azure CLI-Befehlen werden standardmäßig ggf. gekürzte Werte generiert). |
|  |  |  |

Ausführlichere Informationen finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](../storage/common/storage-sas-overview.md).

## <a name="next-steps"></a>Nächste Schritte

- [Generieren des SAS-URI](azure-vm-get-sas-uri.md)