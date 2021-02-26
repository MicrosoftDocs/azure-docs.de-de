---
title: Penetrationstests | Microsoft-Dokumentation
description: Der Artikel bietet eine Übersicht über den Penetrationstestprozess und darüber, wie Sie Penetrationstests Ihrer in der Azure-Infrastruktur ausgeführten App durchführen können.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 9f65133f622c71189f35a1533e278ca09ab02085
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550200"
---
# <a name="penetration-testing"></a>Penetrationstests

Einer der Vorteile beim Einsatz von Azure für Testen und Bereitstellung von Anwendungen besteht darin, dass Sie schnell Umgebungen erstellen können. Sie müssen sich keine Gedanken über Anforderung, Erwerb und Aufbau eigener lokaler Hardware machen.

Das schnelle Erstellen von Umgebungen ist zwar hervorragend – Sie müssen jedoch weiterhin mit angemessener Sorgfalt für Sicherheit sorgen. Wahrscheinlich möchten Sie einen Penetrationstest der Anwendungen durchführen, die Sie in Azure bereitstellen.

Wir führen keinen Penetrationstest Ihrer Anwendung durch, aber wir verstehen, dass Sie Tests Ihrer eigenen Anwendungen durchführen möchten und müssen. Das ist eine gute Sache, denn wenn Sie die Sicherheit Ihrer Anwendungen verbessern, machen Sie das gesamte Azure-Ökosystem sicherer.

Seit dem 15. Juni 2017 benötigt Microsoft keine Vorabgenehmigung mehr, um Penetrationstests für Azure-Ressourcen durchzuführen. Dieser Prozess bezieht sich nur auf Microsoft Azure und ist nicht auf andere Microsoft Cloud-Dienste anwendbar.

>[!IMPORTANT]
>Obwohl die Benachrichtigung von Microsoft über Penetrationstestaktivitäten nicht mehr erforderlich ist, müssen Kunden weiterhin die [Microsoft Cloud Unified Penetration Testing Rules of Engagement](https://technet.microsoft.com/mt784683) (Einheitliche Einsatzregeln für Penetrationstests für die Microsoft Cloud) einhalten.

Folgende Standardtests können Sie durchführen:

* Tests an Ihren Endpunkten, um die [wichtigsten 10 OWASP-Sicherheitsrisiken (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Fuzzing](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) Ihrer Endpunkte
* [Portüberwachung](https://en.wikipedia.org/wiki/Port_scanner) Ihrer Endpunkte

Ein Penetrationstesttyp, den Sie nicht ausführen können, ist jegliche Form eines [Denial-of-Service-Angriffs (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack). Dies schließt einen selbst initiierten DoS-Angriff bzw. das Durchführen zugehöriger Tests ein, die einen DoS-Angriff bestimmen, demonstrieren oder simulieren könnten.

>[!Note]
>Microsoft hat mit BreakingPoint Cloud eine Partnerschaft zum Erstellen einer Schnittstelle gebildet, durch die Sie für Simulationen Datenverkehr für mit DDoS Protection geschützte öffentliche IP-Adressen generieren können. Weitere Informationen zur BreakingPoint Cloud-Simulation finden Sie unter [Durchführen von Simulationstests](../../ddos-protection/test-through-simulations.md).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2).
