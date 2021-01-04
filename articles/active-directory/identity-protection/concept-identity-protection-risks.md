---
title: Was bedeutet Risiko? Azure AD Identity Protection
description: Erläutern von Risikoereignissen in Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f10d8a94be53780f732112c012600a7fb840642b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180641"
---
# <a name="what-is-risk"></a>Was bedeutet Risiko?

Risikoerkennungen in Azure AD Identity Protection umfassen alle identifizierten verdächtigen Aktionen im Zusammenhang mit Benutzerkonten im Verzeichnis.

Identity Protection bietet Organisationen Zugriff auf leistungsstarke Ressourcen, um diese verdächtigen Aktionen zu erkennen und schnell darauf zu reagieren. 

![Sicherheitsübersicht riskanter Benutzer und Anmeldungen](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> Identity Protection generiert Risikoerkennungen nur, wenn die richtigen Anmeldeinformationen verwendet werden. Wenn bei der Anmeldung falsche Anmeldeinformationen verwendet werden, stellt dies kein Risiko durch eine Gefährdung der Anmeldeinformationen dar.

## <a name="risk-types-and-detection"></a>Risikotypen und Erkennung

Es gibt zwei Arten von Risiken (**Benutzerrisiken** und **Anmelderisiken**) und zwei Arten der Erkennung oder Berechnung (**Echtzeit** und **Offline**).

Erkennungen in Echtzeit werden bei der Berichterstellung möglicherweise 5-10 Minuten nicht angezeigt. Offlineerkennungen werden bei der Berichterstellung möglicherweise 2 bis 24 Stunden nicht angezeigt.

### <a name="user-risk"></a>Benutzerrisiko

Ein Benutzerrisiko stellt die Wahrscheinlichkeit dar, dass eine bestimmte Identität oder ein bestimmtes Konto kompromittiert wurde. 

Diese Risiken werden offline anhand interner und externer Threat Intelligence-Quellen von Microsoft berechnet, z. B. Sicherheitsexperten, Strafverfolgungsbehörden, Sicherheitsteams bei Microsoft und anderen vertrauenswürdigen Quellen.

| Risikoerkennung | BESCHREIBUNG |
| --- | --- |
| Kompromittierte Anmeldeinformationen | Dieser Risikoerkennungstyp gibt an, dass die gültigen Anmeldeinformationen des Benutzers kompromittiert wurden. Wenn Internetkriminelle an gültige Kennwörter von berechtigten Benutzern gelangen, geben sie diese Anmeldeinformationen häufig weiter. Diese Freigabe erfolgt in der Regel durch eine Veröffentlichung im Darknet oder auf Paste Sites oder durch den Handel und Verkauf der Anmeldeinformationen auf dem Schwarzmarkt. Wenn der Microsoft-Dienst für kompromittierte Anmeldeinformationen Benutzeranmeldeinformationen aus dem Darknet, von Paste Sites oder anderen Quellen erwirbt, werden sie mit aktuell gültigen Anmeldeinformationen von Azure AD-Benutzern verglichen, um gültige Übereinstimmungen zu finden. Weitere Informationen zu kompromittierten Anmeldeinformationen finden Sie unter [Häufig gestellte Fragen](#common-questions). |
| Azure AD Threat Intelligence | Dieser Risikoerkennungstyp gibt Benutzeraktivitäten an, die für den angegebenen Benutzer unüblich oder mit bekannten Angriffsmustern konsistent sind (basierend auf internen und externen Threat Intelligence-Quellen von Microsoft). |

### <a name="sign-in-risk"></a>Anmelderisiko

Ein Anmelderisiko stellt die Wahrscheinlichkeit dar, dass eine bestimmte Authentifizierungsanforderung vom Identitätsbesitzer nicht autorisiert wurde. 

Diese Risiken können in Echtzeit oder offline anhand interner und externer Threat Intelligence-Quellen von Microsoft berechnet werden, z. B. Sicherheitsexperten, Strafverfolgungsbehörden, Sicherheitsteams bei Microsoft und anderen vertrauenswürdigen Quellen.

| Risikoerkennung | Erkennungstyp | BESCHREIBUNG |
| --- | --- | --- |
| Anonyme IP-Adresse | Echtzeit | Dieser Risikoerkennungstyp gibt Anmeldungen über eine anonyme IP-Adresse (z.B. Tor-Browser oder ein anonymisiertes VPN) an. Diese IP-Adressen werden in der Regel von Akteuren verwendet, die ihre Anmeldungstelemetriedaten (IP-Adresse, Standort, Gerät usw.) für potenziell böswillige Absichten verbergen möchten. |
| Ungewöhnlicher Ortswechsel | Offline | Mit diesem Risikoerkennungstyp werden zwei Anmeldungen identifiziert, die von weit entfernten Orten durchgeführt wurden und bei denen mindestens einer der Orte aufgrund des bisherigen Verhaltens atypisch für den Benutzer ist. Neben zahlreichen anderen Faktoren berücksichtigt dieser Machine Learning-Algorithmus die Zeit zwischen den beiden Anmeldungen sowie die Zeit, die der Benutzer für einen Ortswechsel benötigen würde, wovon sich ableiten lässt, dass ein anderer Benutzer die gleichen Anmeldeinformationen verwendet. <br><br> Bei diesem Algorithmus werden offensichtliche falsch positive Ergebnisse ignoriert, die zu den unmöglichen Ortswechselbedingungen beitragen. Hierzu zählen etwa VPNs und regelmäßig von anderen Benutzern der Organisation verwendete Standorte. Das System verfügt über einen anfänglichen Lernzeitraum von 14 Tagen oder 10 Anmeldungen (je nachdem, welcher Punkt früher erreicht wird), in dem das Anmeldeverhalten des neuen Benutzers erlernt wird. |
| Mit Schadsoftware verknüpfte IP-Adresse | Offline | Mit diesem Risikoerkennungstyp werden Anmeldungen von IP-Adressen identifiziert, die mit Schadsoftware infiziert sind und bekanntermaßen aktiv mit einem Botserver kommunizieren. Diese Erkennung wird ermittelt, indem IP-Adressen des Benutzergeräts mit IP-Adressen korreliert werden, die in Kontakt mit einem Botserver gestanden haben, während der Botserver aktiv war. |
| Ungewöhnliche Anmeldeeigenschaften | Echtzeit | Dieser Risikoerkennungstyp berücksichtigt den bisherigen Anmeldeverlauf (IP-Adresse, Breitengrad/Längengrad und ASN), um nach anomalen Anmeldungen zu suchen. Im System werden Informationen zu den vorherigen Standorten gespeichert, die von einem Benutzer genutzt wurden, und diese werden als „vertraute“ Standorte angesehen. Die Risikoerkennung wird ausgelöst, wenn die Anmeldung von einem Standort aus erfolgt, der in der Liste der bekannten Standorte noch nicht enthalten ist. Neu erstellte Benutzer befinden sich für einen bestimmten Zeitraum im „Lernmodus“, in dem Risikoerkennungen ungewöhnlicher Anmeldeeigenschaften deaktiviert werden, während unsere Algorithmen das Verhalten des Benutzers lernen. Die Dauer des Lernmodus ist dynamisch und hängt davon ab, wie lange es dauert, bis der Algorithmus genügend Informationen über die Anmeldemuster des Benutzers gesammelt hat. Die Mindestdauer beträgt fünf Tage. Ein Benutzer kann nach einer langen Zeit der Inaktivität erneut in den Lernmodus wechseln. Außerdem ignoriert das System Anmeldungen von vertrauten Geräten und von Standorten aus, die geografisch nahe an einem bekannten Speicherort liegen. <br><br> Diese Erkennung wird auch für die Standardauthentifizierung (bzw. ältere Protokolle) ausgeführt. Da diese Protokolle nicht über moderne Eigenschaften (z. B. Client-ID) verfügen, reichen die Telemetriedaten nicht aus, um falsch positive Ergebnisse zu reduzieren. Wir empfehlen unseren Kunden, auf eine moderne Authentifizierung umzusteigen. |
| Benutzergefährdung durch Administrator bestätigt | Offline | Diese Erkennung gibt an, dass ein Administrator auf der Benutzeroberfläche für riskante Benutzer oder mithilfe der riskyUsers-API die Option „Benutzergefährdung bestätigen“ ausgewählt hat. Überprüfen Sie den Risikoverlauf des Benutzers (auf der Benutzeroberfläche oder über die API), um zu überprüfen, welcher Administrator diese Benutzergefährdung bestätigt hat. |
| Schädliche IP-Adresse | Offline | Diese Erkennung gibt eine Anmeldung über eine schädliche IP-Adresse an. Eine IP-Adresse wird wegen hoher Fehlerraten aufgrund von ungültige Anmeldeinformationen, die von der IP-Adresse oder anderen IP-Zuverlässigkeitsquellen empfangen wurden, als schädlich eingestuft. |
| Verdächtige Regeln zur Posteingangsänderung | Offline | Diese Erkennung wird von [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules) erkannt. Diese Erkennung erstellt ein Profil Ihrer Umgebung und löst Warnungen aus, wenn verdächtige Regeln zum Löschen oder Verschieben von Nachrichten oder Ordnern für den Posteingang eines Benutzers festgelegt werden. Diese Erkennung kann darauf hinweisen, dass das Konto des Benutzers kompromittiert ist, dass Nachrichten absichtlich ausgeblendet werden und das Postfach zum Verteilen von Spam und Schadsoftware in Ihrer Organisation verwendet wird. |
| Kennwortspray | Offline | Bei einem Kennwortspray-Angriff werden mehrere Benutzernamen unter Verwendung gängiger Kennwörter im Rahmen eines koordinierten Brute-Force-Angriffs angegriffen, um nicht autorisierten Zugriff zu erhalten. Die Risikoerkennung wird ausgelöst, wenn ein Kennwortspray-Angriff erfolgt ist. |
| Unmöglicher Ortswechsel | Offline | Diese Erkennung wird von [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel) erkannt. Diese Erkennung identifiziert zwei Benutzeraktivitäten (in einer einzelnen Sitzung oder in mehreren Sitzungen), die von unterschiedlichen geografischen Standorten stammen und in einem Zeitraum liegen, der kürzer ist als die Zeit, die der Benutzer benötigt, um von Standort A zu Standort B zu gelangen. Dies deutet darauf hin, dass ein anderer Benutzer die gleichen Anmeldeinformationen verwendet. |

### <a name="other-risk-detections"></a>Andere Risikoerkennungen

| Risikoerkennung | Erkennungstyp | BESCHREIBUNG |
| --- | --- | --- |
| Zusätzliches Risiko erkannt | Echtzeit oder offline | Diese Erkennung gibt an, dass eine der oben genannten Premium-Erkennungen erkannt wurde. Da die Premium-Erkennungen nur für Azure AD Premium P2-Kunden sichtbar sind, werden sie für Kunden ohne Azure AD Premium P2-Lizenzen mit der Bezeichnung „Zusätzliches Risiko erkannt“ angezeigt. |

## <a name="common-questions"></a>Häufig gestellte Fragen

### <a name="risk-levels"></a>Risikostufen

Mit Identity Protection werden Risiken in drei Stufen eingeteilt: niedrig, mittel und hoch. Wenn Sie [benutzerdefinierte Richtlinien für den Identitätsschutz](./concept-identity-protection-policies.md#custom-conditional-access-policy) konfigurieren, können Sie diese auch so konfigurieren, dass sie auf der Ebene **Kein Risiko** ausgelöst werden. „Kein Risiko“ bedeutet, dass es keine aktiven Hinweise auf eine Kompromittierung der Identität des Benutzers gibt.

Microsoft macht zwar keine spezifischen Angaben zur Berechnung von Risiken, mit den einzelnen Stufen wird jedoch jeweils eine höhere Zuverlässigkeit bei der Einschätzung erreicht, ob eine Kompromittierung des Benutzers oder der Anmeldung vorliegt. Beispielsweise sind einmalige ungewöhnliche Anmeldeeigenschaften eines Benutzers unter Umständen nicht so riskant wie kompromittierte Anmeldeinformationen eines anderen Benutzers.

### <a name="password-hash-synchronization"></a>Kennworthashsynchronisierung

Damit Risiken wie kompromittierte Anmeldeinformationen und Kennwortsprays erkannt werden können, müssen Kennworthashes vorhanden sein. Weitere Informationen zur Kennworthashsynchronisierung finden Sie unter [Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](../hybrid/how-to-connect-password-hash-synchronization.md).

### <a name="leaked-credentials"></a>Kompromittierte Anmeldeinformationen

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Wo findet Microsoft kompromittierte Anmeldeinformationen?

Microsoft findet kompromittierte Anmeldeinformationen an verschiedenen Stellen. Dazu zählen:

- Öffentliche Paste-Websites, z. B. pastebin.com und paste.ca, auf denen böswillige Akteure in der Regel solche Materialien posten. Diese Stelle ist der erste Zwischenstopp von böswilligen Akteuren auf der Jagd nach gestohlenen Anmeldeinformationen.
- Strafverfolgungsbehörden.
- Andere Gruppen bei Microsoft, die das Darknet durchforsten.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Warum werden mir keine kompromittierten Anmeldeinformationen angezeigt?

Kompromittierte Anmeldeinformationen werden jedes Mal, wenn Microsoft einen neuen, öffentlich verfügbaren Batch findet, verarbeitet. Angesichts der Sensibilität der Daten werden die kompromittierten Anmeldeinformationen kurz nach der Verarbeitung gelöscht. Für Ihren Mandanten werden nur neue kompromittierte Anmeldeinformationen, die nach dem Aktivieren der Kennworthashsynchronisierung gefunden wurden, verarbeitet. Eine Überprüfung anhand zuvor gefundener Anmeldeinformationspaare erfolgt nicht. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Seit einiger Zeit sind keine Risikoereignisse mit kompromittierten Anmeldeinformationen mehr angezeigt worden.

Wenn Ihnen keine Risikoereignisse mit kompromittierten Anmeldeinformationen angezeigt wurden, kann das folgende Ursachen haben:

- Sie haben keine Kennworthashsynchronisierung für Ihren Mandanten aktiviert.
- Microsoft hat keine kompromittierten Anmeldeinformationspaare gefunden, die Ihren Benutzern entsprechen.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Wie oft verarbeitet Microsoft neue Anmeldeinformationen?

Anmeldeinformationen werden sofort nach deren Auffinden verarbeitet, in der Regel in mehreren Batches pro Tag.

## <a name="next-steps"></a>Nächste Schritte

- [Verfügbare Richtlinien zum Mindern von Risiken](concept-identity-protection-policies.md)
- [Sicherheitsübersicht](concept-identity-protection-security-overview.md)