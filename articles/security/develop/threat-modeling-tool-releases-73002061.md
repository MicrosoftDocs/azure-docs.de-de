---
title: Microsoft Threat Modeling Tool, Release 11.02.2020 – Azure
description: Dokumentation der Versionshinweise für das Threat Modeling Tool-Release 7.3.00206.1
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 9f9b162460cd2e7a624c1ad3f992011487d1b795
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516933"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Threat Modeling Tool, Updaterelease 7.3.00206.1 – 11.02.2020

Version 7.3.00206.1 für das Microsoft Threat Modeling Tool (TMT) wurde am 11. Februar 2020 veröffentlicht und umfasst die folgenden Änderungen:

- Behebung von Programmfehlern

## <a name="notable-bug-fixes"></a>Wichtige Programmfehlerbehebungen

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Fehler im Zusammenhang mit Prioritätswerten außerhalb der erwarteten Bereiche

Einige Kunden berichteten, dass beim Öffnen von Dateien, die in „Threat Modeling Tool 2016“ oder mit benutzerdefinierten Vorlagen erstellt wurden, folgende Fehlermeldung angezeigt wurde:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Das Problem wurde in diesem Release gelöst.

## <a name="system-requirements"></a>Systemanforderungen

- Unterstützte Betriebssysteme
  - [Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) oder höher
- Erforderliche .NET-Version
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) oder höher
- Zusätzliche Anforderungen
  - Eine Internetverbindung ist erforderlich, um Updates für das Tool sowie Vorlagen zu erhalten.

## <a name="documentation-and-feedback"></a>Dokumentation und Feedback

- Dokumentation für das Threat Modeling Tool befindet sich auf [docs.microsoft.com](./threat-modeling-tool.md) und umfasst Informationen [zur Verwendung des Tools](./threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Nächste Schritte

Laden Sie die aktuelle Version des [Microsoft Threat Modeling Tools](https://aka.ms/threatmodelingtool) herunter.