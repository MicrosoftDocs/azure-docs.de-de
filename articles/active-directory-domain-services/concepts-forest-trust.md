---
title: Funktionsweise von Vertrauensstellungen für Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie mehr darüber, wie Gesamtstruktur-Vertrauensstellungen mit Azure AD Domain Services funktionieren.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 5c72ab7d085de558ee95f3c602ccc6be6160b322
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620204"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Funktionsweise von Vertrauensstellungen für Ressourcengesamtstrukturen in Azure Active Directory Domain Services

Active Directory Domain Services (AD DS) verwendet Domänen- und Gesamtstruktur-Vertrauensstellungen, um Sicherheit über mehrere Domänen oder Gesamtstrukturen hinweg zu bieten. Damit Authentifizierung über Vertrauensstellungen hinweg erfolgen kann, muss Windows zunächst überprüfen, ob die von einem Benutzer, Computer oder Dienst angeforderte Domäne eine Vertrauensstellung mit der Domäne des anfordernden Kontos hat.

Um auf diese Vertrauensstellung zu überprüfen, berechnet das Windows-Sicherheitssystem einen Vertrauenspfad zwischen dem Domänencontroller (DC) für den Server, der die Anforderung empfängt, und einem Domänencontroller in der Domäne des anfordernden Kontos.

Die von AD DS bereitgestellten Zugriffssteuerungsmechanismen und das verteilte Windows-Sicherheitsmodell stellen eine Umgebung für das Arbeiten mit Domänen- und Gesamtstruktur-Vertrauensstellungen bereit. Damit diese Vertrauensstellungen ordnungsgemäß funktionieren, muss jede Ressource oder jeder Computer einen direkten Vertrauenspfad zu einem Domänencontroller in der Domäne haben, in der sie oder er sich befindet.

Der Vertrauenspfad wird vom Netzwerkanmeldungsdienst (Anmeldedienst) implementiert. Dies geschieht mit einer authentifizierten RPC-Verbindung (Remote Procedure Call, Remoteprozeduraufruf) mit der Zertifizierungsstelle der vertrauenswürdigen Domäne. Ein geschützter Kanal erstreckt sich auch bis zu anderen AD DS-Domänen über Vertrauensstellungen zwischen Domänen. Dieser geschützte Kanal wird verwendet, um Sicherheitsinformationen abzurufen und zu überprüfen, einschließlich Sicherheits-IDs (SIDs) für Benutzer und Gruppen.

Einen Überblick darüber, wie Vertrauensstellungen auf Azure AD DS angewendet werden, finden Sie unter [Tutorial: Erstellen einer ausgehenden Gesamtstruktur-Vertrauensstellung zu einer lokalen Domäne in Azure Active Directory Domain Services (Vorschauversion)][create-forest-trust].

Um mit der Verwendung von Vertrauensstellungen in Azure AD DS zu beginnen, [erstellen Sie eine verwaltete Domäne, die Gesamtstruktur-Vertrauensstellungen verwendet][tutorial-create-advanced].

## <a name="trust-relationship-flows"></a>Vertrauensstellungsweiterleitungen

Die Weiterleitung von geschützten Datenflüssen über Vertrauensstellungen bestimmt die Elastizität einer Vertrauensstellung. Die Art und Weise, wie Sie eine Vertrauensstellung erstellen oder konfigurieren, bestimmt, wie weit Datenflüsse in innerhalb von Gesamtstrukturen oder über Gesamtstrukturen hinaus weitergeleitet werden.

Die Weiterleitung von Datenflüssen über Vertrauensstellungen wird durch die Richtung der jeweiligen Vertrauensstellung bestimmt. Eine Vertrauensstellung kann unidirektional oder bidirektional und transitiv oder nicht transitiv sein.

Die folgende Abbildung zeigt, dass alle Domänen in *Struktur 1* und *Struktur 2* standardmäßig transitive Vertrauensstellungen haben. Daraus folgt, dass Benutzer in *Struktur 1* auf Ressourcen in Domänen in *Struktur 2* und Benutzer in *Struktur 1* auf Ressourcen in *Struktur 2* zugreifen können, wenn in der jeweiligen Ressource die entsprechenden Berechtigungen zugewiesen sind.

![Abbildung der Vertrauensstellung zwischen zwei Gesamtstrukturen](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Unidirektionale und bidirektionale Vertrauensstellungen

Eine Vertrauensstellungen, die Zugriff auf Ressourcen ermöglicht, kann unidirektional oder bidirektional sein.

Eine unidirektionale Vertrauensstellung ergibt einen unidirektionalen Authentifizierungspfad zwischen zwei Domänen. In einer unidirektionalen Vertrauensstellung zwischen *Domäne A* und *Domäne B* können Benutzer in *Domäne A* auf Ressourcen in *Domäne B* zugreifen. Dagegen können Benutzer in *Domäne B* nicht auf Ressourcen in *Domäne A* zugreifen.

Je nach Typ der zu erstellenden Vertrauensstellung können unidirektionale Vertrauensstellungen entweder nicht transitiv oder transitiv sein.

In einer bidirektionalen Vertrauensstellung gilt: *Domäne A* vertraut *Domäne B*, und *Domäne B* vertraut *Domäne A*. Diese Konfiguration bedeutet, dass Authentifizierungsanforderungen zwischen den beiden Domänen in beide Richtungen übermittelt werden können. Je nach Typ der zu erstellenden Vertrauensstellung können bidirektionale Beziehungen nicht transitiv oder transitiv sein.

Alle Domänenvertrauensstellungen in einer AD DS-Gesamtstruktur sind bidirektionale transitive Vertrauensstellungen. Wenn eine neue untergeordnete Domäne erstellt wird, wird automatisch eine bidirektionale transitive Vertrauensstellung zwischen der neuen untergeordneten Domäne und der übergeordneten Domäne erstellt.

### <a name="transitive-and-non-transitive-trusts"></a>Transitive und nicht transitive Vertrauensstellungen

Transitivität bestimmt, ob eine Vertrauensstellung außerhalb der beiden Domänen, mit denen sie erstellt wurde, erweitert werden kann.

* Eine transitive Vertrauensstellung kann verwendet werden, um Vertrauensstellungen mit anderen Domänen zu erweitern.
* Eine nicht transitive Vertrauensstellung kann verwendet werden, um Vertrauensstellungen mit anderen Domänen zu verweigern.

Jedes Mal, wenn Sie eine neue Domäne in einer Gesamtstruktur erstellen, wird automatisch eine bidirektionale transitive Vertrauensstellung zwischen der neuen Domäne und deren übergeordneter Domäne erstellt. Wird der neuen Domäne eine untergeordnete Domäne hinzugefügt, wird der Vertrauenspfad nach oben durch die Domänenhierarchie geleitet, wodurch der ursprüngliche Vertrauenspfad erweitert wird, der zwischen der neuen Domäne und deren übergeordneter Domäne erstellt wurde. Transitive Vertrauensstellungen werden nach oben durch eine Domänenstruktur geleitet, während diese erstellt wird. Dadurch werden transitive Vertrauensstellungen zwischen allen Domänen der Domänenstruktur erstellt.

Authentifizierungsanforderungen werden über diese Vertrauenspfade gesendet, sodass Konten aus jeder Domäne in der Gesamtstruktur durch jede andere Domäne in der Gesamtstruktur authentifiziert werden können. Nach einem einzigen Anmeldevorgang können Konten mit den entsprechenden Berechtigungen auf Ressourcen in jeder Domäne in der Gesamtstruktur zugreifen.

## <a name="forest-trusts"></a>Gesamtstruktur-Vertrauensstellungen

Mit Gesamtstruktur-Vertrauensstellungen können Sie segmentierte AD DS-Infrastrukturen verwalten und auf Ressourcen und andere Objekte in mehreren Gesamtstrukturen zugreifen. Gesamtstruktur-Vertrauensstellungen sind nützlich für Dienstanbieter, für Unternehmen, die Fusionen oder Übernahmen abwickeln, für Extranets für kooperierende Unternehmen sowie für Unternehmen, die eine Lösung für administrative Autonomie suchen.

Durch Verwenden von Gesamtstruktur-Vertrauensstellungen können Sie zwei verschiedene Gesamtstrukturen verknüpfen, um eine unidirektionale oder bidirektionale transitive Vertrauensstellung zu erstellen. Eine Gesamtstruktur-Vertrauensstellung ermöglicht es Administratoren, zwei AD DS-Gesamtstrukturen über eine einzige Vertrauensstellung zu verbinden, um eine nahtlose Authentifizierungs- und Autorisierungsumgebung für die Gesamtstrukturen bereitzustellen.

Eine Gesamtstruktur-Vertrauensstellung kann nur zwischen der Stammdomäne einer Gesamtstruktur und der Stammdomäne einer anderen Gesamtstruktur erstellt werden. Gesamtstruktur-Vertrauensstellungen können nur zwischen zwei Gesamtstrukturen erstellt werden und können nicht implizit auf eine dritte Gesamtstruktur erweitert werden. Das heißt, wird eine Gesamtstruktur-Vertrauensstellung zwischen *Gesamtstruktur 1* und *Gesamtstruktur 2* erstellt, und wird eine weitere Gesamtstruktur-Vertrauensstellung zwischen *Gesamtstruktur 2* und *Gesamtstruktur 3* erstellt, hat *Gesamtstruktur 1* keine implizite Vertrauensstellung mit *Gesamtstruktur 3*.

Die folgende Abbildung zeigt zwei getrennte Gesamtstruktur-Vertrauensstellungen zwischen drei AD DS-Gesamtstrukturen in einer einzigen Organisation.

![Abbildung der Gesamtstruktur Vertrauensstellungen innerhalb einer einzigen Organisation](./media/concepts-forest-trust/forest-trusts-diagram.png)

Diese Beispielkonfiguration ermöglicht die folgenden Zugriffe:

* Benutzer in *Gesamtstruktur 2* können auf Ressourcen in jeder Domäne in *Gesamtstruktur 1* oder in *Gesamtstruktur 3* zugreifen.
* Benutzer in *Gesamtstruktur 3* können auf Ressourcen in jeder Domäne in *Gesamtstruktur 2* zugreifen.
* Benutzer in *Gesamtstruktur 1* können auf Ressourcen in jeder Domäne in *Gesamtstruktur 2* zugreifen.

Diese Konfiguration lässt es nicht zu, dass Benutzer in *Gesamtstruktur 1* auf Ressourcen in *Gesamtstruktur 3* zugreifen (oder umgekehrt). Sollen Benutzer in *Gesamtstruktur 1* und *Gesamtstruktur 3* Ressourcen gemeinsam nutzen können, muss zwischen den beiden Gesamtstrukturen eine bidirektionale transitive Vertrauensstellung erstellt werden.

Wird eine unidirektionale Gesamtstruktur-Vertrauensstellung zwischen zwei Gesamtstrukturen erstellt, können Mitglieder der vertrauenswürdigen Gesamtstruktur Ressourcen in der vertrauenden Gesamtstruktur verwenden. Die Vertrauensstellung funktioniert jedoch nur in einer Richtung.

Wird beispielsweise eine unidirektionale Gesamtstruktur-Vertrauensstellung zwischen *Gesamtstruktur 1* (die vertrauenswürdige Gesamtstruktur) und *Gesamtstruktur 2* (die vertrauende Gesamtstruktur) erstellt, ergibt sich Folgendes:

* Mitglieder von *Gesamtstruktur 1* können auf Ressourcen zugreifen, die sich in *Gesamtstruktur 2* befinden.
* Mitglieder von *Gesamtstruktur 2* können nicht über dieselbe Vertrauensstellung auf Ressourcen zugreifen, die sich in *Gesamtstruktur 1* befinden.

> [!IMPORTANT]
> Eine Azure AD Domain Services-Ressourcengesamtstruktur unterstützt nur eine unidirektionale Gesamtstruktur-Vertrauensstellung zu einem lokalen Active Directory.

### <a name="forest-trust-requirements"></a>Anforderungen für eine Gesamtstruktur-Vertrauensstellung

Bevor Sie eine Gesamtstruktur-Vertrauensstellung erstellen können, müssen Sie überprüfen, ob die richtige DNS-Infrastruktur (Domain Name System) vorhanden ist. Gesamtstruktur-Vertrauensstellungen können nur erstellt werden, wenn eine der folgenden DNS-Konfigurationen verfügbar ist:

* Ein DNS-Stammserver ist der DNS-Stammserver für beide Gesamtstruktur-DNS-Namespaces: Die Stammzone enthält Delegierungen für jeden der DNS-Namespaces, und die Stammhinweise aller DNS-Server umfassen den DNS-Stammserver.
* Wenn kein gemeinsam genutzter DNS-Stammserver vorhanden ist und die DNS-Stammserver in jedem Gesamtstruktur-DNS-Namespace bedingte DNS-Weiterleitungen für jeden DNS-Namespace verwenden, um Abfragen auf Namen im anderen Namespace weiterzuleiten.

    > [!IMPORTANT]
    > Für eine Azure AD Domain Services-Ressourcengesamtstruktur muss diese DNS-Konfiguration verwendet werden. Azure Active Directory Domain Services kann keinen anderen DNS-Namespace als den DNS-Namespace der Ressourcengesamtstruktur hosten. Bedingte Weiterleitungen ist die richtige Konfiguration.

* Wenn kein gemeinsam genutzter DNS-Stammserver vorhanden ist, werden für die DNS-Stammserver in jedem Gesamtstruktur-DNS-Namespaces sekundäre DNS-Zonen verwendet, die in jedem DNS-Namespace konfiguriert sind, um Abfragen auf Namen im anderen Namespace weiterzuleiten.

Damit Sie eine Gesamtstruktur-Vertrauensstellung erstellen können, müssen Sie Mitglied der Gruppe „Domänen-Admins“ (in der Stammdomäne der Gesamtstruktur) oder der Gruppe „Organisations-Admins“ in Active Directory sein. Jeder Vertrauensstellung wird ein Kennwort zugewiesen, das die Administratoren in beiden Gesamtstrukturen kennen müssen. Mitglieder von „Organisations-Admins“ in beiden Gesamtstrukturen können die Vertrauensstellungen in beiden Gesamtstrukturen auf einmal erstellen, und es wird (in diesem Szenario) automatisch ein kryptografisch zufälliges Kennwort generiert und für beide Gesamtstrukturen geschrieben.

Die ausgehende Gesamtstruktur-Vertrauensstellung für Azure AD Domain Services wird im Azure-Portal erstellt. Sie erstellen die Vertrauensstellung nicht manuell mit der verwalteten Domäne selbst. Die eingehende Gesamtstruktur-Vertrauensstellung muss von einem Benutzer mit den Berechtigungen konfiguriert werden, die zuvor im lokalen Active Directory festgehalten wurden.

## <a name="trust-processes-and-interactions"></a>Vertrauensstellungsprozesse und -interaktionen

Viele Transaktionen zwischen Domänen oder Gesamtstrukturen hängen von Domänen- oder Gesamtstruktur-Vertrauensstellungen ab, um verschiedene Aufgaben auszuführen. In diesem Abschnitt sind die Prozesse und Interaktionen beschrieben, die auftreten, wenn über Vertrauensstellungen auf Ressourcen zugegriffen wird und wenn Authentifizierungsverweise ausgewertet werden.

### <a name="overview-of-authentication-referral-processing"></a>Übersicht über die Verarbeitung von Authentifizierungsverweisen

Wenn eine Authentifizierungsanforderung auf eine Domäne verweist, muss der Domänencontroller in dieser Domäne ermitteln, ob eine Vertrauensstellung mit der Domäne vorhanden ist, von der die Anforderung stammt. Auch die Richtung der Vertrauensstellung, und ob diese transitiv oder nicht transitiv ist, muss bestimmt werden, bevor der Benutzer authentifiziert wird, sodass er auf Ressourcen in der Domäne zugreifen kann. Der jeweilige Authentifizierungsvorgang zwischen vertrauenswürdigen Domänen variiert je nach verwendetem Authentifizierungsprotokoll. Das Kerberos V5- und das NTLM-Protokoll verarbeiten Verweise zur Authentifizierung in einer Domäne unterschiedlich.

### <a name="kerberos-v5-referral-processing"></a>Kerberos V5-Weiterleitungsverarbeitung

Das Kerberos V5-Authentifizierungsprotokoll ist für Clientauthentifizierungs- und -autorisierungsinformationen auf den Netzwerkanmeldungsdienst auf den Domänencontrollern angewiesen. Das Kerberos-Protokoll stellt eine Verbindung mit einem Online-Schlüsselverteilungscenter (Key Distribution Center, KDC) und dem Active Directory-Kontospeicher für Sitzungstickets her.

Das Kerberos-Protokoll verwendet außerdem Vertrauensstellungen für bereichsübergreifende Ticket-Granting Services (TGS) und zum Überprüfen von Privilege Attribute Certificates (PACs) über einen geschützten Kanal. Das Kerberos-Protokoll führt eine bereichsübergreifende Authentifizierung nur mit Kerberos-Bereichen aus, für die kein Windows-Betriebssystem verwendet wird, wie dies z. B. für einen MIT-Kerberos-Bereich gilt, und es muss nicht mit dem Anmeldedienst interagieren.

Wenn der Client Kerberos V5 zur Authentifizierung verwendet, fordert er von einem Domänencontroller in seiner Kontodomäne ein Ticket für den Server in der Zieldomäne an. Das Kerberos-KDC fungiert als vertrauenswürdiger Vermittler zwischen dem Client und dem Server und stellt einen Sitzungsschlüssel bereit, mit dem sich die beiden Parteien wechselseitig authentifizieren können. Ist die Zieldomäne nicht mit der aktuellen Domäne identisch, folgt das KDC einem logischen Prozess, um zu bestimmen, ob eine Authentifizierungsanforderung weitergeleitet werden kann:

1. Wird der aktuellen Domäne direkt von der Domäne des Servers vertraut, der angefordert wird?
    * Wenn ja, wird ein Verweis auf die angeforderte Domäne an den Client gesendet.
    * Wenn nein, wird zum nächsten Schritt gewechselt.

2. Besteht eine transitive Vertrauensstellung zwischen der aktuellen Domäne und der nächsten Domäne im Vertrauenspfad?
    * Wenn ja, wird ein Verweis auf die nächste Domäne im Vertrauenspfad an den Client gesendet.
    * Wenn nein, wird eine Meldung zu verweigerter Anmeldung an den Client gesendet.

### <a name="ntlm-referral-processing"></a>NTLM-Weiterleitungsverarbeitung

Das NTLM-Authentifizierungsprotokoll ist für Clientauthentifizierungs- und -autorisierungsinformationen auf den Netzwerkanmeldungsdienst auf den Domänencontrollern angewiesen. Mit diesem Protokoll werden Clients authentifiziert, die nicht die Kerberos-Authentifizierung verwenden. Mit NTLM werden Vertrauensstellungen verwendet, um Authentifizierungsanforderungen zwischen Domänen zu übergeben.

Verwendet der Client NTLM für eine Authentifizierung, wird die auslösende Authentifizierungsanforderung direkt vom Client an den Ressourcenserver in der Zieldomäne gesendet. Dieser Server erstellt eine Abfrage, auf die der Client antwortet. Der Server sendet dann die Antwort des Benutzers an einen Domänencontroller in seiner Computerkontodomäne. Dieser Domänencontroller überprüft das Benutzerkonto anhand seiner Sicherheitskontendatenbank.

Ist das Konto nicht in der Datenbank vorhanden, ermittelt der Domänencontroller entsprechend der folgenden Logik, ob eine Passthrough-Authentifizierung ausgeführt oder ob die Anforderung weitergeleitet oder abgelehnt werden soll:

1. Hat die aktuelle Domäne eine direkte Vertrauensstellung mit der Domäne des Benutzers?
    * Wenn ja, sendet der Domänencontroller die Anmeldeinformationen des Clients an einen Domänencontroller in der Domäne des Benutzers zur Passthrough-Authentifizierung.
    * Wenn nein, wird zum nächsten Schritt gewechselt.

2. Hat die aktuelle Domäne eine transitive Vertrauensstellung mit der Domäne des Benutzers?
    * Wenn ja, wird die Authentifizierungsanforderung an die nächste Domäne im Vertrauenspfad weitergeleitet. Dieser Domänencontroller wiederholt den Vorgang, indem er die Anmeldeinformationen des Benutzers anhand seiner eigenen Sicherheitskontendatenbank überprüft.
    * Wenn nein, wird eine Meldung zu verweigerter Anmeldung an den Client gesendet.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Kerberos-basierte Verarbeitung von Authentifizierungsanforderungen über Gesamtstruktur-Vertrauensstellungen

Wenn zwei Gesamtstrukturen über eine Gesamtstruktur-Vertrauensstellung verbunden sind, können Authentifizierungsanforderungen, die über das Kerberos V5- oder NTLM-Protokoll erfolgen, zwischen Gesamtstrukturen weitergeleitet werden, um Zugriff auf Ressourcen in beiden Gesamtstrukturen zu ermöglichen.

Wenn eine Gesamtstruktur-Vertrauensstellung erstmals eingerichtet wird, sammelt jede Gesamtstruktur alle vertrauenswürdigen Namespaces in ihrer Partnergesamtstruktur und speichert die Informationen in einem [vertrauenswürdigen Domänenobjekt](#trusted-domain-object) (Trusted Domain Object, TDO). Vertrauenswürdige Namespaces umfassen Domänenstrukturnamen, UPN-Suffixe (User Principal Name, Benutzerprinzipalname), SPN-Suffixe (Service Principal Name, Dienstprinzipalname) und SID-Namespaces (Sicherheits-ID), die in der anderen Gesamtstruktur verwendet werden. Vertrauenswürdige Domänenobjekte werden in den globalen Katalog repliziert.

Damit Authentifizierungsprotokolle dem Gesamtstruktur-Vertrauenspfad folgen können, muss der Dienstprinzipalname (SPN) des Ressourcencomputers zu einem Speicherort in der anderen Gesamtstruktur aufgelöst werden. Ein SPN kann einer der folgenden Namen sein:

* Der DNS-Name eines Hosts
* Der DNS-Name einer Domäne
* Der kennzeichnende Name (Distinguished Name) eines Dienstverbindungspunkt-Objekts.

Wenn eine Arbeitsstation in einer Gesamtstruktur versucht, auf Daten auf einem Ressourcencomputer in einer anderen Gesamtstruktur zuzugreifen, versucht der Kerberos-Authentifizierungsprozess, vom Domänencontroller ein Dienstticket zum SPN des Ressourcencomputers zu erhalten. Sobald der Domänencontroller den globalen Katalog abgefragt und festgestellt hat, dass der SPN sich nicht in derselben Gesamtstruktur wie der Domänencontroller befindet, sendet der Domänencontroller einen Verweis auf seine übergeordnete Domäne an die Arbeitsstation. An diesem Punkt fragt die Arbeitsstation die übergeordnete Domäne auf das Dienstticket ab und folgt solange der Verweiskette (Weiterleitungskette), bis sie die Domäne erreicht hat, in der sich die Ressource befindet.

Die folgende Abbildung und die folgenden Schritte enthalten eine ausführliche Beschreibung des Kerberos-Authentifizierungsprozesses, der verwendet wird, wenn Computer unter Windows versuchen, auf Ressourcen auf einem Computer zuzugreifen, der sich in einer anderen Gesamtstruktur befindet.

![Abbildung des Kerberos-Prozesses über eine Gesamtstruktur-Vertrauensstellung](media/concepts-forest-trust/kerberos-over-forest-trust-process-diagram.png)

1. *User1* meldet sich bei *Workstation1* mit den Anmeldeinformationen aus der Domäne *europe.tailspintoys.com* an. Der Benutzer versucht dann, auf eine freigegebene Ressource auf *FileServer1* zuzugreifen, der sich in der Gesamtstruktur *usa.wingtiptoys.com* befindet.

2. *Workstation1* wendet sich an das Kerberos-KDC auf einem Domänencontroller in ihrer Domäne, *ChildDC1*, und fordert ein Dienstticket für den *FileServer1*-SPN an.

3. *ChildDC1* findet den SPN nicht in seiner Domänendatenbank und fragt den globalen Katalog ab, um festzustellen, ob irgendeine Domäne in der Gesamtstruktur *tailspintoys.com* diesen SPN enthält. Weil ein globaler Katalog auf seine eigene Gesamtstruktur beschränkt ist, wird der SPN nicht gefunden.

    Der globale Katalog überprüft dann seine Datenbank auf Informationen zu allen Gesamtstruktur-Vertrauensstellungen, die mit seiner Gesamtstruktur eingerichtet sind. Wurde eine solche Vertrauensstellung gefunden, werden die Namenssuffixe im vertrauenswürdigen Domänenobjekt der Gesamtstruktur-Vertrauensstellung mit dem Suffix des Ziel-SPNs verglichen, um eine Übereinstimmung zu finden. Sobald eine Übereinstimmung gefunden wurde, sendet der globale Katalog einen Routinghinweis an *ChildDC1*.

    Routinghinweise unterstützen dabei, Authentifizierungsanforderungen in Richtung der Zielgesamtstruktur zu senden. Hinweise werden nur verwendet, wenn alle herkömmlichen Authentifizierungskanäle, beispielsweise lokaler Domänencontroller und dann globaler Katalog, nicht dazu führen, dass ein SPN gefunden wird.

4. *ChildDC1* sendet einen Verweis auf seine übergeordnete Domäne an *Workstation1*.

5. *Workstation1* wendet sich an einen Domänencontroller in *ForestRootDC1* (ihre übergeordnete Domäne), um einen Verweis auf einen Domänencontroller (*ForestRootDC2*) in der Stammdomäne der Gesamtstruktur *wingtiptoys.com* zu erhalten.

6. *Workstation1* wendet sich an *ForestRootDC2* in der Gesamtstruktur *wingtiptoys.com*, um ein Dienstticket zu dem angeforderten Dienst zu erhalten.

7. *ForestRootDC2* wendet sich an seinen globalen Katalog, um den SPN zu finden. Im globalen Katalog wird eine Übereinstimmung für den SPN gefunden, und diese wird an *ForestRootDC2* gesendet.

8. *ForestRootDC2* sendet dann den Verweis auf *usa.wingtiptoys.com* an *Workstation1*.

9. *Workstation1* wendet sich an das KDC auf *ChildDC2* und verhandelt das Ticket für *User1*, um Zugriff auf *FileServer1* zu erhalten.

10. Sobald *Workstation1* ein Dienstticket hat, sendet sie das Ticket an *FileServer1*, der die Anmeldeinformationen von *User1* liest und entsprechend ein Zugriffstoken erstellt.

## <a name="trusted-domain-object"></a>Vertrauenswürdiges Domänenobjekt

Jede Domänen- oder Gesamtstruktur-Vertrauensstellung in einer Organisation wird durch ein vertrauenswürdiges Domänenobjekt (Trusted Domain Object, TDO) repräsentiert, das im *System*-Container in seiner Domäne gespeichert ist.

### <a name="tdo-contents"></a>TDO-Inhalte

Die in einem TDO enthaltenen Informationen variieren abhängig davon, ob ein TDO von einer Domänenvertrauensstellung oder einer Gesamtstruktur-Vertrauensstellung erstellt wurde.

Wenn eine Domänenvertrauensstellung erstellt wird, werden Attribute wie der DNS-Domänenname, die Domänen-SID, der Vertrauenstyp, die Transitivität der Vertrauensstellung und der gegenseitige Domänenname im TDO festgehalten. In einem TDO für eine Gesamtstruktur-Vertrauensstellung werden weitere Attribute gespeichert, um alle vertrauenswürdigen Namespaces aus der Partnergesamtstruktur zu kennzeichnen. Diese Attribute umfassen Domänenstrukturnamen, UPN-Suffixe (User Principal Name, Benutzerprinzipalname), SPN-Suffixe (Service Principal Name, Dienstprinzipalname) und SID-Namespaces (Sicherheits-ID).

Weil Vertrauensstellungen in Active Directory als TDOs gespeichert werden, sind allen Domänen in einer Gesamtstruktur die Vertrauensstellungen bekannt, die in der Gesamtstruktur eingerichtet sind. Gleiches gilt, wenn mehrere Gesamtstrukturen über Gesamtstruktur-Vertrauensstellungen miteinander verknüpft sind, denn der Gesamtstruktur-Stammdomäne in jeder Gesamtstruktur sind die Vertrauensstellungen bekannt, die in den Domänen in vertrauenswürdigen Gesamtstrukturen eingerichtet sind.

### <a name="tdo-password-changes"></a>TDO-Kennwortänderungen

Beide Domänen in einer Vertrauensstellung haben ein gemeinsames Kennwort, das im TDO-Objekt in Active Directory gespeichert ist. Im Rahmen des Kontowartungsprozesses ändert der vertrauenswürdige Domänencontroller alle 30 Tage das im TDO gespeicherte Kennwort. Da alle bidirektionalen Vertrauensstellungen tatsächlich zwei unidirektionale Vertrauensstellungen in jeweils entgegengesetzter Richtung sind, erfolgt der Prozess zweimal für bidirektionale Vertrauensstellungen.

Eine Vertrauensstellung hat eine vertrauende und eine vertrauenswürdige Seite. Auf der vertrauenswürdigen Seite kann jeder beschreibbare Domänencontroller für den Prozess verwendet werden. Auf der vertrauenden Seite führt der PDC-Emulator die Kennwortänderung aus.

Um ein Kennwort zu ändern, gehen die Domänencontroller wie folgt vor:

1. Der PDC-Emulator (primärer Domänencontroller) in der vertrauenden Domäne erstellt ein neues Kennwort. Ein Domänencontroller in der vertrauenswürdigen Domäne initiiert niemals eine Kennwortänderung. Sie wird immer durch den PDC-Emulator der vertrauenden Domäne initiiert.

2. Der PDC-Emulator in der vertrauenden Domäne legt das *OldPassword*-Feld des TD-Objekts auf das aktuelle *NewPassword*-Feld fest.

3. Der PDC-Emulator in der vertrauenden Domäne legt das *NewPassword*-Feld des TD-Objekts auf das neue Kennwort fest. Durch Aufbewahren einer Kopie des vorherigen Kennworts ist es möglich, zum alten Kennwort zurückzukehren, wenn der Domänencontroller in der vertrauenswürdigen Domäne die Änderung nicht empfängt, oder wenn die Änderung nicht repliziert wird, bevor eine Anforderung gesendet wird, die das neue Kennwort der Vertrauensstellung verwendet.

4. Der PDC-Emulator in der vertrauenden Domäne sendet einen Remoteaufruf an einen Domänencontroller in der vertrauenswürdigen Domäne, in dem dieser aufgefordert wird, das Kennwort für das Vertrauensstellungskonto auf das neue Kennwort festzulegen.

5. Der Domänencontroller in der vertrauenswürdigen Domäne ändert das Kennwort der Vertrauensstellung in das neue Kennwort.

6. Auf jeder Seite der Vertrauensstellung werden die Aktualisierungen an die anderen Domänencontroller in der Domäne repliziert. In der vertrauenden Domäne löst die Änderung eine dringende Replikation des vertrauenswürdigen Domänenobjekts aus.

Das Kennwort ist nun in beiden Domänencontrollern geändert. Mit normaler Replikation werden die TDO-Objekte an die anderen Domänencontroller in der Domäne verteilt. Es ist jedoch möglich, dass der Domänencontroller in der vertrauenden Domäne das Kennwort ändert, ohne dass ein Domänencontroller in der vertrauenswürdigen Domäne erfolgreich aktualisiert werden kann. Dieser Fall kann auftreten, wenn ein sicherer Kanal, der für die Verarbeitung der Kennwortänderung erforderlich ist, nicht eingerichtet werden konnte. Es ist auch möglich, dass der Domänencontroller in der vertrauenswürdigen Domäne zu einem bestimmten Zeitpunkt während des Prozesses nicht verfügbar ist und das aktualisierte Kennwort möglicherweise nicht erhält.

Um eine Situation zu bewältigen, in der die Kennwortänderung nicht erfolgreich übermittelt werden kann, ändert der Domänencontroller in der vertrauenden Domäne niemals das neue Kennwort, es sei denn, er hat mit dem neuen Kennwort erfolgreich eine Authentifizierung (Einrichten eines geschützten Kanals) vorgenommen. Dieses Verhalten ist der Grund, warum sowohl das alte als auch das neue Kennwort im TDO-Objekt der vertrauenden Domäne aufbewahrt werden.

Eine Kennwortänderung ist erst abgeschlossen, wenn die Authentifizierung mit dem Kennwort erfolgreich war. Das alte gespeicherte Kennwort kann über den geschützten Kanal verwendet werden, bis der Domänencontroller in der vertrauenswürdigen Domäne das neue Kennwort erhalten hat. Auf diese Weise wird ununterbrochener Dienst ermöglicht.

Wenn die Authentifizierung mit dem neuen Kennwort fehlschlägt, weil das Kennwort ungültig ist, versucht der vertrauende Domänencontroller, sich mit dem alten Kennwort zu authentifizieren. Kann er sich mit dem alten Kennwort erfolgreich authentifizieren, setzt er den Kennwortänderungsprozess innerhalb von 15 Minuten fort.

Aktualisierungen des Vertrauensstellungskennworts müssen innerhalb von 30 Tagen an die Domänencontroller auf beiden Seiten der Vertrauensstellung repliziert werden. Wird das Vertrauensstellungskennwort nach 30 Tagen geändert und hat ein Domänencontroller nur das N-2-Kennwort, kann er die Vertrauensstellung von der vertrauenden Seite nicht verwenden und kann keinen sicheren Kanal auf der vertrauenswürdigen Seite erstellen.

## <a name="network-ports-used-by-trusts"></a>Von Vertrauensstellungen verwendete Netzwerkports

Weil Vertrauensstellungen über verschiedene Netzwerkgrenzen hinweg bereitgestellt werden müssen, kann es sein, dass sie mindestens eine Firewall überbrücken müssen. Wenn dies der Fall ist, können Sie entweder den Vertrauensstellungsdatenverkehr durch eine Firewall tunneln oder bestimmte Ports in der Firewall öffnen, damit der Datenverkehr durchgelassen wird.

> [!IMPORTANT]
> Active Directory Domain Services unterstützt es nicht, Active Directory-RPC-Datenverkehr auf bestimmte Ports zu beschränken.

Weitere Informationen zu den für eine Gesamtstruktur-Vertrauensstellung benötigten Ports finden Sie im Abschnitt **Windows Server 2008 und höhere Versionen** im Microsoft-Support-Artikel [Konfigurieren einer Firewall für Active Directory-Domänen und -Vertrauensstellungen](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts).

## <a name="supporting-services-and-tools"></a>Unterstützende Dienste und Tools

Zur Unterstützung von Vertrauensstellungen und Authentifizierung werden einige zusätzliche Features und Verwaltungstools verwendet.

### <a name="net-logon"></a>Netzwerkanmeldung

Der Netzwerkanmeldungsdienst (Anmeldedienst) verwaltet einen geschützten Kanal von einem Windows-basierten Computer zu einem Domänencontroller. Der Dienst wird auch in den folgenden vertrauensstellungsbezogenen Prozessen verwendet:

* Einrichten und Verwalten von Vertrauensstellungen: Netzwerkanmeldung unterstützt bei der Verwaltung von Vertrauensstellungskennwörtern, sammelt Vertrauensstellungsinformationen und überprüft Vertrauensstellungen durch Interagieren mit dem LSA-Prozess und dem TDO.

    Für eine Gesamtstruktur-Vertrauensstellung enthalten die Vertrauensstellungsinformationen den *FTInfo*-Datensatz (Forest Trust Information, Gesamtstrukturvertrauensstellungs-Informationen). Dieser wiederum enthält den Satz der Namespaces, die eine vertrauenswürdige Gesamtstruktur zur Verwaltung beansprucht, kommentiert mit einem Feld, das angibt, ob jedem Anspruch durch die vertrauende Gesamtstruktur vertraut wird.

* Authentifizierung: Sendet Benutzeranmeldeinformationen über einen geschützten Kanal an einen Domänencontroller und gibt die Domänen-SIDs und Benutzerrechte für den Benutzer zurück.

* Domänencontrollersuche: Unterstützt beim Suchen nach Domänencontrollern in einer Domäne oder über Domänen hinweg.

* Pass-Through-Überprüfung: Anmeldeinformationen von Benutzern in anderen Domänen werden von Netzwerkanmeldung verarbeitet. Wenn eine vertrauende Domäne die Identität eines Benutzers überprüfen muss, übergibt sie die Anmeldeinformationen des Benutzers über Netzwerkanmeldung zur Überprüfung an die vertrauenswürdige Domäne.

* PAC-Überprüfung (Privilege Attribute Certificate): Wenn ein Server, der das Kerberos-Protokoll für die Authentifizierung verwendet, die PAC-Datei in einem Dienstticket überprüfen muss, sendet er die PAC-Datei über den sicheren Kanal an seinen Domänencontroller zur Überprüfung.

### <a name="local-security-authority"></a>Lokale Sicherheitsautorität

Die lokale Sicherheitsautorität (LSA) ist ein geschütztes Subsystem, das Informationen über alle Aspekte der lokalen Sicherheit für ein System verwaltet. Insgesamt als lokale Sicherheitsrichtlinie bezeichnet, stellt die LSA verschiedene Dienste für die Übersetzung zwischen Namen und Bezeichnern bereit.

Das LSA-Sicherheitssubsystem stellt Dienste sowohl im Kernelmodus als auch im Benutzermodus bereit, mit denen Zugriff auf Objekte überprüft, Benutzerberechtigungen geprüft und Überwachungsmeldungen erstellt werden können. LSA ist für das Überprüfen der Gültigkeit aller Sitzungstickets zuständig, die von Diensten in vertrauenswürdigen oder nicht vertrauenswürdigen Domänen gesendet werden.

### <a name="management-tools"></a>Verwaltungstools

Administratoren können *Active Directory-Domänen und -Vertrauensstellungen*, *Netdom* und *Nltest* verwenden, um Vertrauensstellungen verfügbar zu machen, zu erstellen, zu entfernen oder zu ändern.

* *Active Directory-Domänen und -Vertrauensstellungen* ist die Microsoft Management Console (MMC), mit der Domänenvertrauensstellungen, Domänen- und Gesamtstrukturfunktionsebenen sowie die Suffixe von Benutzerprinzipalnamen verwaltet werden.
* Die Befehlszeilentools *Netdom* und *Nltest* können verwendet werden, um Vertrauensstellungen zu suchen, anzuzeigen, zu erstellen und zu verwalten. Diese Tools kommunizieren direkt mit der lokalen Sicherheitsautorität auf einem Domänencontroller.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Ressourcengesamtstrukturen finden Sie unter [Funktionsweise von Vertrauensstellungen für Ressourcengesamtstrukturen in Azure Active Directory Domain Services][concepts-trust].

Informationen zu den ersten Schritten beim Erstellen einer verwalteten Domäne mit einer Ressourcengesamtstruktur finden Sie unter [Tutorial: Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne][tutorial-create-advanced]. Danach können Sie die Schritte für [Erstellen einer ausgehenden Gesamtstruktur-Vertrauensstellung zu einer lokalen Domäne in Azure Active Directory Domain Services][create-forest-trust] ausführen.

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
