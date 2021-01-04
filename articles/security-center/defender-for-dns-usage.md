---
title: Reagieren auf Warnungen von Azure Defender für DNS
description: Erfahren Sie etwas über die erforderlichen Schritte zum Reagieren auf Warnungen von Azure Defender für DNS.
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754539"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>Reagieren auf Warnungen von Azure Defender für DNS

Wenn Sie eine Warnung von Azure Defender für DNS erhalten, empfiehlt es sich, die Warnung wie unten beschrieben zu untersuchen und auf sie zu reagieren. Azure Defender für DNS schützt alle verbundenen Ressourcen. Selbst wenn Sie mit der Anwendung oder dem Benutzer, die bzw. der die Warnung ausgelöst hat, vertraut sind, sollten Sie bei jeder Warnung die Situation unbedingt überprüfen.  


## <a name="step-1-contact"></a>Schritt 1: Contact

1. Wenden Sie sich an den Ressourcenbesitzer, um zu bestimmen, ob das Verhalten erwartet oder beabsichtigt war.
1. Wenn die Aktivität erwartet wird, schließen Sie die Warnung.
1. Wenn die Aktivität unerwartet ist, behandeln Sie die Ressource als potenziell kompromittiert und mindern Sie das Risiko, wie im nächsten Schritt beschrieben.

## <a name="step-2-immediate-mitigation"></a>Schritt 2: Sofortige Risikominderung 

1. Isolieren Sie die Ressource vom Netzwerk, um Lateral Movement zu verhindern.
1. Führen Sie eine vollständige Antischadsoftwareüberprüfung für die Ressource aus, und befolgen Sie dabei alle resultierenden Korrekturanweisungen.
1. Überprüfen Sie die installierte und laufende Software auf der Ressource, und entfernen Sie alle unbekannten oder unerwünschten Pakete.
1. Versetzen Sie den Computer in einen bekannten gültigen Zustand zurück, installieren Sie ggf. das Betriebssystem neu, und stellen Sie die Software aus einer verifizierten schadsoftwarefreien Quelle wieder her.
1. Beheben Sie alle Azure Security Center-Empfehlungen für den Computer, und korrigieren Sie dabei hervorgehobene Sicherheitsprobleme, um zukünftige Sicherheitsprobleme zu verhindern.


## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurde die Reaktion auf eine Warnung von Azure Defender für DNS erläutert. Zugehörige Informationen finden Sie auf den folgenden Seiten:

- [Einführung in Azure Defender für DNS](defender-for-dns-introduction.md)
- [Unterdrücken von Warnungen von Azure Defender](alerts-suppression-rules.md)
- [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md)