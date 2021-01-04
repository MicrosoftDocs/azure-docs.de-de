---
title: Kennwortschutz in Azure Active Directory
description: Es wird beschrieben, wie Sie mit dem Azure Active Directory-Kennwortschutz unsichere Kennwörter für Ihre Umgebung dynamisch sperren.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b019c12103a0324e21ef7c06b0c7a5d7fe14e6ec
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743886"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>Ausschließen von ungeeigneten Kennwörtern mit dem Azure Active Directory-Kennwortschutz

Viele Sicherheitsanleitungen enthalten die Empfehlung, dass dasselbe Kennwort nicht mehrfach verwendet werden sollte, dass es komplex sein muss und dass einfache Versionen wie *Kennwort123* vermieden werden sollten. Sie können für Ihre Benutzer zwar eine [Anleitung zum Wählen von Kennwörtern](https://www.microsoft.com/research/publication/password-guidance) bereitstellen, aber unsichere Kennwörter werden trotzdem häufig verwendet. Der Azure AD-Kennwortschutz erkennt und blockiert bekannte unsichere Kennwörter und deren Varianten und kann außerdem unsichere Ausdrücke blockieren, die spezifisch für Ihre Organisation sind.

Beim Azure AD-Kennwortschutz werden globale Standardlisten mit gesperrten Kennwörtern automatisch auf alle Benutzer eines Azure AD-Mandanten angewendet. Zur Unterstützung Ihrer eigenen Geschäfts- und Sicherheitsanforderungen können Sie Einträge in einer benutzerdefinierten Liste mit gesperrten Kennwörtern definieren. Wenn Benutzer ihre Kennwörter ändern oder zurücksetzen, werden diese anhand dieser Listen mit gesperrten Kennwörtern überprüft, um die Nutzung von sicheren Kennwörtern zu erzwingen.

Sie sollten auch zusätzliche Features wie [Azure AD Multi-Factor Authentication](concept-mfa-howitworks.md) nutzen und sich nicht nur auf die sicheren Kennwörter verlassen, die mit dem Azure AD-Kennwortschutz erzwungen werden. Weitere Informationen zur Verwendung mehrerer Sicherheitsebenen für Ihre Anmeldeereignisse finden Sie unter [Ihr Kennwort spielt keine Rolle](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

> [!IMPORTANT]
> In diesem Konzeptartikel wird für Administratoren beschrieben, wie der Azure AD-Kennwortschutz funktioniert. Wenn Sie bereits als Endbenutzer für die Self-Service-Kennwortzurücksetzung registriert sind und den Zugriff auf Ihr Konto verloren haben, navigieren Sie zu [https://aka.ms/sspr](https://aka.ms/sspr).
>
> Wenn Ihr IT-Team die Möglichkeit zum Zurücksetzen Ihres eigenen Kennworts nicht aktiviert hat, wenden Sie sich an den Helpdesk, um weitere Unterstützung zu erhalten.

## <a name="global-banned-password-list"></a>Liste global gesperrter Kennwörter

Das Azure AD Identity Protection-Team analysiert ständig die Azure AD-Sicherheitstelemetriedaten und sucht nach unsicheren oder kompromittierten Kennwörtern, die häufig verwendet werden. Bei der Analyse wird vor allem nach grundlegenden Ausdrücken gesucht, die häufig als Basis für unsichere Kennwörter dienen. Wenn unsichere Ausdrücke gefunden werden, werden sie in die *globale Liste mit den gesperrten Kennwörtern* aufgenommen. Der Inhalt der globalen Liste mit den gesperrten Kennwörtern basiert nicht auf einer externen Datenquelle, sondern auf den Ergebnissen der Azure AD-Sicherheitstelemetriedaten und -Analyse.

Wenn ein Kennwort für einen Benutzer auf einem Azure AD-Mandanten geändert oder zurückgesetzt wird, wird die aktuelle Version der globalen Liste mit den gesperrten Kennwörtern verwendet, um die Sicherheit des Kennworts zu überprüfen. Diese Überprüfung führt zu einer höheren Sicherheit der Kennwörter aller Azure AD-Kunden.

Die globale Liste mit den gesperrten Kennwörtern wird automatisch auf alle Benutzer eines Azure AD-Mandanten angewendet. Sie müssen nichts aktivieren oder konfigurieren, und sie kann nicht deaktiviert werden. Diese globale Liste mit gesperrten Kennwörtern wird auf Benutzer angewendet, wenn diese ihr eigenes Kennwort über Azure AD ändern oder zurücksetzen.

> [!NOTE]
> Cyberkriminelle verwenden bei ihren Angriffen ähnliche Strategien, um häufig verwendete unsichere Kennwörter und deren Varianten zu identifizieren. Zur Verbesserung der Sicherheit wird der Inhalt der globalen Liste mit den gesperrten Kennwörtern von Microsoft nicht veröffentlicht.

## <a name="custom-banned-password-list"></a>Benutzerdefinierte Liste gesperrter Kennwörter

Einige Organisationen möchten die Sicherheit verbessern und zusätzlich zur globalen Liste mit den gesperrten Kennwörtern eigene Anpassungen verwenden. Zum Hinzufügen Ihrer eigenen Einträge können Sie eine *benutzerdefinierte Liste mit gesperrten Kennwörtern* nutzen. Bei den Ausdrücken, die der benutzerdefinierten Liste mit den gesperrten Kennwörtern hinzugefügt werden, sollte der Schwerpunkt auf organisationsspezifischen Ausdrücken liegen, z. B.:

- Markennamen
- Produktnamen
- Standorte, z. B. die Hauptniederlassung des Unternehmens
- Interne unternehmensspezifische Ausdrücke
- Abkürzungen mit einer bestimmten unternehmensspezifischen Bedeutung

Nachdem die Ausdrücke der benutzerdefinierten Liste mit den gesperrten Kennwörtern hinzugefügt wurden, werden sie mit den Ausdrücken in der globalen Liste mit den gesperrten Kennwörtern kombiniert. Ereignisse zum Ändern oder Zurücksetzen von Kennwörtern werden dann anhand der kombinierten Listen mit den gesperrten Kennwörtern überprüft.

> [!NOTE]
> Die benutzerdefinierte Liste mit gesperrten Kennwörtern ist auf maximal 1.000 Ausdrücke beschränkt. Dieses Feature ist nicht für die Sperrung von äußerst umfangreichen Listen mit Kennwörtern ausgelegt.
>
> Um die Vorteile der benutzerdefinierten Liste mit den gesperrten Kennwörtern bestmöglich zu nutzen, sollten Sie sich zuerst mit der [Auswertung von Kennwörtern](#how-are-passwords-evaluated) vertraut machen, bevor Sie der benutzerdefinierten Sperrliste Ausdrücke hinzufügen. Bei diesem Ansatz können Sie große Mengen von unsicheren Kennwörtern und deren Varianten effizient erkennen und blockieren.

![Ändern der benutzerdefinierten Liste gesperrter Kennwörter unter „Authentifizierungsmethoden“](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

Angenommen, der Kunde ist *Contoso*. Das Unternehmen befindet sich in London und stellt ein Produkt mit dem Namen *Widget* her. Für diesen Beispielkunden wäre es sowohl unnötig als auch weniger sicher, bestimmte Varianten dieser Ausdrücke zu blockieren, z. B.:

- „Contoso!1“
- "Contoso@London"
- „ContosoWidget“
- „!Contoso“
- „LondonHQ“

Stattdessen ist es viel effizienter und sicherer, nur die grundlegenden Schlüsselausdrücke zu sperren, z. B. Folgendes:

- „Contoso“
- „London“
- „Widget“

Der Algorithmus für die Kennwortüberprüfung blockiert dann automatisch unsichere Varianten und Kombinationen.

Arbeiten Sie das folgende Tutorial durch, um in die Nutzung einer benutzerdefinierten Liste mit gesperrten Kennwörtern einzusteigen:

> [!div class="nextstepaction"]
> [Tutorial: Konfigurieren von benutzerdefinierten gesperrten Kennwörtern](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Kennwort-Spray-Angriffe und kompromittierte Kennwortlisten von Drittanbietern

Mit dem Azure AD-Kennwortschutz können Sie sich gegen Kennwort-Spray-Angriffe verteidigen. Bei den meisten Kennwort-Spray-Angriffen wird nicht versucht, ein einzelnes Konto mehr als nur einige Male anzugreifen. Dies würde nämlich zu einer erhöhten Entdeckungswahrscheinlichkeit führen, indem das Konto gesperrt oder eine andere Verteidigungsmaßnahme angewendet wird.

Bei der Mehrheit der Kennwort-Spray-Angriffe wird stattdessen nur eine geringe Anzahl von bekanntermaßen sehr unsicheren Kennwörtern für die einzelnen Konten eines Unternehmens übermittelt. Dieses Verfahren ermöglicht es dem Angreifer, schnell nach einem leicht zu kompromittierenden Konto zu suchen und mögliche Schwellenwerte für die Erkennung zu vermeiden.

Beim Azure AD-Kennwortschutz werden alle bekannten unsicheren Kennwörter, die ggf. bei Kennwort-Spray-Angriffen zum Einsatz kommen, effizient blockiert. Dieser Schutz basiert auf realen Sicherheitstelemetriedaten von Azure AD, mit denen die globale Liste mit den gesperrten Kennwörtern erstellt wird.

Es gibt einige Websites von Drittanbietern, auf denen Millionen von Kennwörtern aufgelistet sind, die bei früheren öffentlich bekannten Sicherheitsverletzungen kompromittiert wurden. Es kommt häufig vor, dass Drittanbieterprodukte zur Kennwortüberprüfung auf einem Brute-Force-Vergleich mit diesen Millionen von Kennwörtern basieren. Diese Verfahren sind aber angesichts der typischen Strategien, die bei Kennwort-Spray-Angriffen zum Einsatz kommen, nicht die beste Möglichkeit, um die allgemeine Kennwortsicherheit zu verbessern.

> [!NOTE]
> Die globale Liste mit den gesperrten Kennwörtern basiert nicht auf Datenquellen von Drittanbietern, z. B. Listen mit kompromittierten Kennwörtern.

Die globale Liste mit den gesperrten Kennwörtern ist gegenüber den langen Listen der Drittanbieter zwar vergleichsweise kurz, aber die Ausdrücke stammen aus realen Sicherheitstelemetriedaten wirklicher Kennwort-Spray-Angriffe. Mit diesem Ansatz wird die allgemeine Sicherheit und Effektivität verbessert, und darüber hinaus werden für den Algorithmus für die Kennwortüberprüfung intelligente Verfahren mit Fuzzyübereinstimmungen genutzt. Daher können mit dem Azure AD-Kennwortschutz Millionen von häufig genutzten unsicheren Kennwörtern erkannt und für die Verwendung in Ihrem Unternehmen blockiert werden.

## <a name="on-premises-hybrid-scenarios"></a>Lokale Hybridszenarien

Viele Organisationen verfügen über ein Hybrididentitätsmodell, das lokale AD DS-Umgebungen (Active Directory Domain Services) umfasst. Um die Sicherheitsvorteile des Azure AD-Kennwortschutzes auch auf Ihre AD DS-Umgebung auszuweiten, können Sie Komponenten auf Ihren lokalen Servern installieren. Mit diesen Agents wird erzwungen, dass bei Kennwortänderungsereignissen in der lokalen AD DS-Umgebung dieselbe Kennwortrichtlinie wie in Azure AD eingehalten wird.

Weitere Informationen finden Sie unter [Erzwingen des Azure AD-Kennwortschutzes für Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="how-are-passwords-evaluated"></a>Auswerten von Kennwörtern

Wenn ein Benutzer sein Kennwort ändert oder zurücksetzt, wird das neue Kennwort auf Sicherheit und Komplexität überprüft, indem es mit der kombinierten Liste mit Ausdrücken aus der globalen und der benutzerdefinierten Liste mit den gesperrten Kennwörtern verglichen wird.

Falls das Kennwort eines Benutzers ein gesperrtes Kennwort enthält, kann es ggf. trotzdem akzeptiert werden, sofern das gesamte Kennwort ansonsten sicher genug ist. Ein neu konfiguriertes Kennwort durchläuft die folgenden Schritte, um seine allgemeine Sicherheit beurteilen und feststellen zu können, ob es akzeptiert oder abgelehnt werden sollte:

### <a name="step-1-normalization"></a>Schritt 1: Normalisierung

Ein neues Kennwort durchläuft zunächst einen Normalisierungsprozess. Diese Technik ermöglicht es, einen kleinen Satz verbotener Kennwörter auf einen viel größeren Satz potenziell schwacher Kennwörter zu übertragen.

Die Normalisierung besteht aus den beiden folgenden Schritten:

* Alle Großbuchstaben werden in Kleinbuchstaben umgewandelt.
* Anschließend werden häufige Zeichenersetzungen durchgeführt, z. B.:

   | Ursprünglicher Buchstabe | Ersetzter Buchstabe |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | s                  |
   | \@              | a                  |

Betrachten Sie das folgende Beispiel:

* Das Kennwort „blank“ ist gesperrt.
* Ein Benutzer versucht, sein Kennwort in „Bl@nK“ zu ändern.
* „Bl@nk“ ist zwar nicht gesperrt, aber vom Normalisierungsprozess wird dieses Kennwort in „blank“ konvertiert.
* Dieses Kennwort wird abgelehnt.

### <a name="step-2-check-if-password-is-considered-banned"></a>Schritt 2: Überprüfen, ob das Kennwort als gesperrt angesehen wird

Für das Kennwort wird dann ein Abgleich auf andere Übereinstimmungen durchgeführt, und es wird eine Bewertung mit einer Punktzahl generiert. Anhand dieser abschließenden Bewertung wird ermittelt, ob die angeforderte Kennwortänderung akzeptiert oder abgelehnt wird.

#### <a name="fuzzy-matching-behavior"></a>Verhaltens der Fuzzyübereinstimmung

Die Fuzzyübereinstimmung wird für das normalisierte Kennwort verwendet, um festzustellen, ob es ein Kennwort enthält, das entweder in der globalen oder in der benutzerdefinierten Liste der gesperrten Kennwörter enthalten ist. Der Abgleichsprozess basiert auf einer Edit-Distanz eines (1) Vergleichs.

Betrachten Sie das folgende Beispiel:

* Das Kennwort „abcdef“ ist gesperrt.
* Ein Benutzer versucht, sein Kennwort in eine der folgenden Varianten zu ändern:

   * „abcdeg“: *letztes Zeichen von „f“ in „g“ geändert*
   * „abcdefg“: *„g“ am Ende angefügt*
   * „abcde“: *„f“ am Ende gelöscht*

* Alle obigen Kennwörter ergeben keine genaue Übereinstimmung mit dem gesperrten Kennwort „abcdef“.

    Da der „Änderungsabstand“ für jedes Beispiel aber nur ein Zeichen bzw. einen Schritt gegenüber dem gesperrten Ausdruck „abcdef“ beträgt, werden alle als Übereinstimmung mit „abcdef“ eingestuft.
* Diese Kennwörter werden abgelehnt.

#### <a name="substring-matching-on-specific-terms"></a>Abgleich der Teilzeichenfolge (für bestimmte Ausdrücke)

Der Abgleich der Teilzeichenfolge wird für das normalisierte Kennwort genutzt, um eine Überprüfung auf den Vor- und Nachnamen des Benutzers sowie auf den Mandantennamen durchzuführen. Der Abgleich mit dem Mandantennamen erfolgt nicht, wenn Kennwörter auf einem AD DS-Domänencontroller für lokale Hybridszenarien überprüft werden.

> [!IMPORTANT]
> Ein Abgleich der Teilzeichenfolge wird nur für Namen und andere Ausdrücke erzwungen, die mindestens vier Zeichen lang sind.

Betrachten Sie das folgende Beispiel:

* Ein Benutzer mit dem Namen „Poll“ möchten sein Kennwort in „p0LL23fb“ ändern.
* Nach der Normalisierung lautet dieses Kennwort „poll23fb“.
* Durch den Abgleich der Teilzeichenfolge wird festgestellt, dass das Kennwort den Vornamen des Benutzers („Poll“) enthält.
* Obwohl „poll23fb“ in keiner Liste mit den gesperrten Kennwörtern enthalten war, wurde beim Abgleich der Teilzeichenfolge „Poll“ im Kennwort gefunden.
* Dieses Kennwort wird abgelehnt.

#### <a name="score-calculation"></a>Bewertungsberechnung

Der nächste Schritt besteht darin, alle Fälle von verbotenen Kennwörtern in dem normalisierten neuen Kennwort des Benutzers zu identifizieren. Punkte werden anhand der folgenden Kriterien zugewiesen:

1. Für jedes gesperrte Kennwort, das sich im Kennwort eines Benutzers befindet, wird ein Punkt vergeben.
1. Jedes verbleibende eindeutige Zeichen erhält einen Punkt.
1. Ein Kennwort muss mindestens fünf (5) Punkte aufweisen, um akzeptiert zu werden.

In den beiden nächsten Beispielszenarien verwendet Contoso den Azure AD-Kennwortschutz und hat „contoso“ auf die benutzerdefinierte Liste mit den gesperrten Kennwörtern gesetzt. Außerdem ist „blank“ in der globalen Liste enthalten.

Im folgenden Beispielszenario ändert ein Benutzer sein Kennwort in „C0ntos0Blank12“:

* Nach der Normalisierung lautet dieses Kennwort „contosoblank12“.
* Beim Abgleichprozess wird ermittelt, dass dieses Kennwort zwei gesperrte Kennwörter enthält: „contoso“ und „blank“.
* Dieses Kennwort erhält dann die folgende Bewertung:

    *[contoso] + [blank] + [1] + [2] = 4 Punkte*

* Da für das Kennwort keine fünf (5) Punkte erreicht wurden, wird es abgelehnt.

Wir sehen uns nun ein etwas anderes Beispiel an, um zu veranschaulichen, wie zusätzliche Komplexität bei einem Kennwort zur erforderlichen Anzahl von Punkten und somit zur Akzeptanz führen kann. Im folgenden Beispielszenario ändert ein Benutzer sein Kennwort in „ContoS0Bl@nkf9“:

* Nach der Normalisierung lautet dieses Kennwort „contosoblankf9!“.
* Beim Abgleichprozess wird ermittelt, dass dieses Kennwort zwei gesperrte Kennwörter enthält: „contoso“ und „blank“.
* Dieses Kennwort erhält dann die folgende Bewertung:

    *[contoso] + [blank] + [f] + [9] + [!] = 5 Punkte*

* Da für dieses Kennwort mindestens fünf (5) Punkte erzielt werden, wird es akzeptiert.

> [!IMPORTANT]
> Der Algorithmus für gesperrte Kennwörter und die globale Liste mit den gesperrten Kennwörtern können sich in Azure basierend auf der laufenden Sicherheitsanalyse und -forschung jederzeit ändern.
>
> In Hybridszenarien werden aktualisierte Algorithmen für den lokalen DC-Agent-Dienst erst nach einem Upgrade der DC-Agent-Software wirksam.

## <a name="what-do-users-see"></a>Anzeige für Benutzer

Wenn ein Benutzer versucht, ein Kennwort auf einen Wert zurückzusetzen, der gesperrt werden würde, wird die folgende Fehlermeldung angezeigt:

*„Ihr Kennwort ist aufgrund eines enthaltenen Worts, Ausdrucks oder Musters leider leicht zu erraten. Wiederholen Sie den Vorgang mit einem anderen Kennwort.“*

## <a name="license-requirements"></a>Lizenzanforderungen

| Benutzer | Kennwortschutz für Azure AD mit globaler Liste gesperrter Kennwörter | Kennwortschutz für Azure AD mit benutzerdefinierter Liste gesperrter Kennwörter|
|-------------------------------------------|---------------------------|---------------------------|
| Reine Cloudbenutzer                          | Azure AD Free             | Azure AD Premium P1 oder P2 |
| Über lokale AD DS-Instanz synchronisierte Benutzer | Azure AD Premium P1 oder P2 | Azure AD Premium P1 oder P2 |

> [!NOTE]
> Lokale AD DS-Benutzer, die nicht mit Azure AD synchronisiert werden, profitieren basierend auf der vorhandenen Lizenzierung für synchronisierte Benutzer ebenfalls vom Azure AD-Kennwortschutz.

Weitere Informationen zur Lizenzierung einschließlich der Kosten finden Sie unter [Azure Active Directory – Preise](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie das folgende Tutorial durch, um in die Nutzung einer benutzerdefinierten Liste mit gesperrten Kennwörtern einzusteigen:

> [!div class="nextstepaction"]
> [Tutorial: Konfigurieren von benutzerdefinierten gesperrten Kennwörtern](tutorial-configure-custom-password-protection.md)

Sie haben dann auch die Möglichkeit, den [lokalen Azure AD-Kennwortschutz zu aktivieren](howto-password-ban-bad-on-premises-deploy.md).
