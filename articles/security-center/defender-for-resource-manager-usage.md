---
title: Reagieren auf Warnungen von Azure Defender für Resource Manager
description: Erfahren Sie etwas über die erforderlichen Schritte zum Reagieren auf Warnungen von Azure Defender für Resource Manager.
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754507"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>Reagieren auf Warnungen von Azure Defender für Resource Manager

Wenn Sie eine Warnung von Azure Defender für Resource Manager erhalten, empfiehlt es sich, die Warnung wie unten beschrieben zu untersuchen und auf sie zu reagieren. Azure Defender für Resource Manager schützt alle verbundenen Ressourcen. Selbst wenn Sie mit der Anwendung oder dem Benutzer, die bzw. der die Warnung ausgelöst hat, vertraut sind, sollten Sie bei jeder Warnung die Situation unbedingt überprüfen.  


## <a name="step-1-contact"></a>Schritt 1: Contact

1. Wenden Sie sich an den Ressourcenbesitzer, um zu bestimmen, ob das Verhalten erwartet oder beabsichtigt war.
1. Wenn die Aktivität erwartet wird, schließen Sie die Warnung.
1. Wenn die Aktivität unerwartet ist, behandeln Sie die betroffenen Benutzerkonten, Abonnements und virtuellen Computer als kompromittiert und mindern Sie das Risiko, wie im nächsten Schritt beschrieben.

## <a name="step-2-immediate-mitigation"></a>Schritt 2: Sofortige Risikominderung 

1. Risikominderung für kompromittierte Benutzerkonten:
    - Wenn sie nicht bekannt sind, löschen Sie sie, da sie möglicherweise von einem Angreifer erstellt wurden.
    - Wenn sie bekannt sind, ändern Sie ihre Anmeldeinformationen für die Authentifizierung.
    - Verwenden Sie Azure-Aktivitätsprotokolle, um alle von dem Benutzer ausgeführten Aktivitäten zu überprüfen und dabei alle verdächtigen Aktionen zu identifizieren.

1. Risikominderung für kompromittierte Abonnements:
    - Entfernen Sie alle unbekannten Runbooks aus dem kompromittierten Automation-Konto.
    - Überprüfen Sie die IAM-Berechtigungen für das Abonnement, und entfernen Sie Berechtigungen für alle unbekannten Benutzerkonten.
    - Überprüfen Sie alle Azure-Ressourcen im Abonnement, und löschen Sie alle unbekannten.
    - Überprüfen und untersuchen Sie alle Sicherheitswarnungen für das Abonnement in Azure Security Center.
    - Verwenden Sie Azure-Aktivitätsprotokolle, um alle in dem Abonnement ausgeführten Aktivitäten zu überprüfen und dabei alle verdächtigen Aktionen zu identifizieren.

1. Korrigieren Sie die kompromittierten virtuellen Computer.
    - Ändern Sie die Kennwörter für alle Benutzer.
    - Führen Sie eine vollständige Antischadsoftwareüberprüfung auf dem Computer aus.
    - Führen Sie ein Reimaging der Computer aus einer schadsoftwarefreien Quelle durch.


## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurde die Reaktion auf eine Warnung von Azure Defender für Resource Manager erläutert. Zugehörige Informationen finden Sie auf den folgenden Seiten:

- [Einführung in Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md)
- [Unterdrücken von Warnungen von Azure Defender](alerts-suppression-rules.md)
- [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md)