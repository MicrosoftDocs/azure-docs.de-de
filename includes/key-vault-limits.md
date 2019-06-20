---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b9d87fd7929607da8407ae5bbfb2f6dd6d69dab
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178496"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Schlüsseltransaktionen (maximale Transaktionen innerhalb von 10 Sekunden pro Tresor und Region<sup>1</sup>):

|Schlüsseltyp|HSM-Schlüssel<br>Erstellungsschlüssel|HSM-Schlüssel<br>Alle anderen Transaktionen|Softwareschlüssel<br>Erstellungsschlüssel|Softwareschlüssel<br>Alle anderen Transaktionen|
|:---|---:|---:|---:|---:|
|RSA 2.048 Bit|5|1\.000|10|2\.000|
|RSA 3.072 Bit|5|250|10|500|
|RSA 4.096 Bit|5|125|10|250|
|ECC P-256|5|1\.000|10|2\.000|
|ECC P-384|5|1\.000|10|2\.000|
|ECC P-521|5|1\.000|10|2\.000|
|ECC SECP256K1|5|1\.000|10|2\.000|

> [!NOTE]
> In der vorstehenden Tabelle ist zu erkennen, dass für RSA-Softwareschlüssel mit 2.048 Bit 2.000 GET-Transaktionen pro 10 Sekunden zulässig sind. Für RSA-HSM-Schlüssel mit 2.048 Bit sind 1.000 GET-Transaktionen pro 10 Sekunden zulässig.
>
> Die genannten Schwellenwerte für die Drosselung sind gewichtet, und ihre Summe wird erzwungen. Wie in der vorherigen Tabelle gezeigt fällt für das Ausführen von GET-Vorgängen mit RSA-HSM-Schlüsseln bei Verwendung eines 4.096-Bit-Schlüssels ein achtmal größerer Aufwand als mit einem 2.048-Bit-Schlüssel an. Der Grund dafür ist: 1.000/125 = 8.
>
> Innerhalb eines 10-Sekunden-Intervalls kann ein Azure Key Vault-Client *nur einen* der folgenden Vorgänge ausführen, bevor der HTTP-Statuscode `429` zur Drosselung auftritt:
> - 2\.000 GET-Transaktionen für RSA 2.048-Bit-Softwareschlüssel
> - 1\.000 GET-Transaktionen für RSA 2.048-Bit-HSM-Schlüssel
> - 125 GET-Transaktionen für RSA 4.096-Bit-HSM-Schlüssel
> - 124 GET-Transaktionen für RSA 4.096-Bit-HSM-Schlüssel und 8 GET-Transaktionen für RSA 2.048-Bit-HSM-Schlüssel

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Geheimnisse, Schlüssel für verwaltete Speicherkonten und Tresortransaktionen:
| Transaktionstyp | Maximal zulässige Transaktionen innerhalb von 10 Sekunden pro Tresor und Region<sup>1</sup> |
| --- | --- |
| Alle Transaktionen |2\.000 |

Informationen zur Handhabung der Drosselung bei Überschreiten dieser Grenzwerte finden Sie unter [Anleitung zur Drosselung von Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> Für alle Transaktionsarten gilt als abonnementweiter Grenzwert das Fünffache des Schlüsseltresorlimits. „HSM – andere Transaktionen“ pro Abonnement ist beispielsweise auf 5.000 Transaktionen in 10 Sekunden pro Abonnent beschränkt.
