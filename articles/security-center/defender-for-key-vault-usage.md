---
title: Reagieren auf Warnungen von Azure Defender für Key Vault
description: Erfahren Sie etwas über die erforderlichen Schritte zum Reagieren auf Warnungen von Azure Defender für Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 67c556e44f07240b1ad1bcde61f40042da46def8
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122196"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>Reagieren auf Warnungen zu Azure Defender für Key Vault
Wenn Sie eine Warnung von Azure Defender für Key Vault erhalten, empfiehlt es sich, die Warnung wie unten beschrieben zu untersuchen und auf sie zu reagieren. Azure Defender für Key Vault schützt Anwendungen und Anmeldeinformationen. Selbst wenn Sie mit der Anwendung oder dem Benutzer, die bzw. der die Warnung ausgelöst hat, vertraut sind, sollten Sie bei jeder Warnung die Situation unbedingt überprüfen.  

Jede Warnung von Azure Defender für Key Vault schließt die folgenden Elemente ein:

- ObjectID
- Benutzerprinzipalname oder IP-Adresse der verdächtigen Ressource

> [!TIP]
> Basierend auf dem *Typ* des aufgetretenen Zugriffs sind einige Felder möglicherweise nicht verfügbar. Wenn auf Ihren Schlüsseltresor beispielsweise durch eine Anwendung zugegriffen wurde, wird kein zugeordneter Benutzerprinzipalname angezeigt. Wenn der Datenverkehr von außerhalb von Azure stammt, wird keine Objekt-ID angezeigt.

## <a name="step-1-contact"></a>Schritt 1: Contact

1. Überprüfen Sie, ob der Datenverkehr aus Ihrem Azure-Mandanten stammt. Wenn die Key Vault-Firewall aktiviert ist, haben Sie wahrscheinlich dem Benutzer oder der Anwendung, der bzw. die diese Warnung ausgelöst hat, den Zugriff gewährt.
1. Wenn Sie die Quelle des Datenverkehrs nicht überprüfen können, fahren Sie fort mit [Schritt 2: Sofortige Risikominderung](#step-2-immediate-mitigation).
1. Wenn Sie die Quelle des Datenverkehrs in Ihrem Mandanten ermitteln können, wenden Sie sich an den Benutzer oder den Besitzer der Anwendung. 

> [!CAUTION]
> Azure Defender für Key Vault dient zur Erkennung verdächtiger Aktivitäten, die durch gestohlene Anmeldeinformationen verursacht werden. Verwerfen Sie die Warnung **nicht**, nur weil Sie den Benutzer oder die Anwendung kennen. Wenden Sie sich an den Besitzer der Anwendung oder den Benutzer, und vergewissern Sie sich, dass die Aktivität legitim war. Sie können ggf. eine Unterdrückungsregel erstellen, um Rauschen auszuschließen. Weitere Informationen finden Sie unter [Unterdrücken von Warnungen von Azure Defender](alerts-suppression-rules.md).


## <a name="step-2-immediate-mitigation"></a>Schritt 2: Sofortige Risikominderung 
Wenn Sie den Benutzer oder die Anwendung nicht erkannt haben oder wenn Sie der Ansicht sind, dass der Zugriff nicht autorisiert werden sollte:

- Wenn der Datenverkehr von einer unbekannten IP-Adresse stammt:
    1. Aktivieren Sie die Azure Key Vault-Firewall, wie unter [Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken](../key-vault/general/network-security.md) beschrieben.
    1. Konfigurieren Sie die Firewall mit vertrauenswürdigen Ressourcen und virtuellen Netzwerken.

- Wenn die Quelle der Warnung eine nicht autorisierte Anwendung oder ein verdächtiger Benutzer war:
    1. Öffnen Sie die Einstellungen für die Zugriffsrichtlinien des Schlüsseltresors.
    1. Entfernen Sie den zugehörigen Sicherheitsprinzipal, oder schränken Sie die Vorgänge ein, die der Sicherheitsprinzipal ausführen darf.  

- Wenn die Quelle der Warnung über eine Azure Active Directory-Rolle in Ihrem Mandanten verfügt:
    1. Wenden Sie sich an den Administrator.
    1. Ermitteln Sie, ob Azure Active Directory-Berechtigungen verringert oder widerrufen werden müssen.

## <a name="step-3-identify-impact"></a>Schritt 3: Ermitteln der Auswirkungen 
Nachdem die Auswirkungen behoben wurden, untersuchen Sie die betroffenen Geheimnisse in Ihrem Schlüsseltresor:
1. Öffnen Sie die Seite „Sicherheit“ für Ihre Azure Key Vault-Instanz, und zeigen Sie die ausgelöste Warnung an.
1. Wählen Sie die genaue ausgelöste Warnung aus.
    Überprüfen Sie die Liste der Geheimnisse, auf die zugegriffen wurde, und den Zeitstempel.
1. Wenn Sie Key Vault-Diagnoseprotokolle aktiviert haben, überprüfen Sie optional die vorherigen Vorgänge für die entsprechende IP-Adresse, den Benutzerprinzipal oder die Objekt-ID des Aufrufers.  

## <a name="step-4-take-action"></a>Schritt 4: Ausführen einer Aktion 
Wenn Sie die Liste der Geheimnisse, Schlüssel und Zertifikate, auf die der verdächtige Benutzer oder die Anwendung zugegriffen hat, zusammengestellt haben, sollten Sie diese Objekte umgehend rotieren.

1. Betroffene Geheimnisse sollten deaktiviert oder aus Ihrem Schlüsseltresor gelöscht werden.
1. Wenn die Anmeldeinformationen für eine bestimmte Anwendung verwendet wurden:
    1. Wenden Sie sich an den Administrator der Anwendung, und bitten Sie ihn, die Umgebung auf die Verwendung der kompromittierten Anmeldeinformationen seit der Kompromittierung zu überprüfen.
    1. Wenn die kompromittierten Anmeldeinformationen verwendet wurden, sollte der Besitzer der Anwendung ermitteln, auf welche Informationen zugegriffen wurde, und die Auswirkungen minimieren.


## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurde die Reaktion auf eine Warnung von Azure Defender für Key Vault erläutert. Zugehörige Informationen finden Sie auf den folgenden Seiten:

- [Einführung in Azure Defender für Key Vault](defender-for-key-vault-introduction.md)
- [Unterdrücken von Warnungen von Azure Defender](alerts-suppression-rules.md)
- [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md)