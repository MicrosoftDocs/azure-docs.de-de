---
title: Planen von Richtlinien für bedingten Zugriff in Azure Active Directory | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie Richtlinien für bedingten Zugriff für Azure Active Directory planen.
services: active-directory
author: MicrosoftGuyJFlo
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e277f31dcf2627959b88d58f325fb4dad024a00
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001193"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Anleitung: Planen der Bereitstellung von bedingtem Zugriff in Azure Active Directory

Die Planung der Bereitstellung von bedingtem Zugriff ist wichtig, um sicherzustellen, dass Sie für Apps und Ressourcen in Ihrer Organisation eine geeignete Zugriffsstrategie implementieren. Der Großteil der Bereitstellungsplanung sollte auf die Ausarbeitung der verschiedenen Richtlinien entfallen, die nötig sind, um den Zugriff Ihrer Benutzer auf der Grundlage bestimmter Bedingungen zuzulassen oder zu blockieren. In diesem Dokument werden die Schritte erläutert, die zur Implementierung sicherer und effektiver Richtlinien für bedingten Zugriff erforderlich sind. Bevor Sie mit diesen Schritten beginnen, sollten Sie sich zunächst mit der Funktionsweise des [bedingten Zugriffs](overview.md) sowie mit dessen Verwendung vertraut machen.


## <a name="what-you-should-know"></a>Wichtige Informationen

Bedingter Zugriff ist weniger ein eigenständiges Feature, sondern vielmehr ein Framework, mit dem Sie den Zugriff auf die Apps und Ressourcen Ihrer Organisation steuern können. Aus diesem Grund müssen für einige Einstellungen des bedingten Zugriffs zusätzliche Features konfiguriert werden. Ein Beispiel: Sie können eine Richtlinie konfigurieren, die auf eine bestimmte [Risikostufe für die Anmeldung](../identity-protection/howto-sign-in-risk-policy.md#what-is-the-sign-in-risk-policy) reagiert. Für eine Richtlinie, die auf der Risikostufe für die Anmeldung basiert, muss jedoch [Azure Active Directory Identity Protection](../identity-protection/overview.md) aktiviert werden.

Sollten zusätzliche Features erforderlich sein, benötigen Sie ggf. auch entsprechende Lizenzen. Bedingter Zugriff ist zwar beispielsweise ein Azure AD Premium P1-Feature, für Identity Protection wird jedoch eine Azure AD Premium P2-Lizenz benötigt.

Bei Richtlinien für bedingten Zugriff wird zwischen Basis- und Standardrichtlinien unterschieden. Eine [Basisrichtlinie](baseline-protection.md) ist eine vordefinierte Richtlinie für bedingten Zugriff. Mit diesen Richtlinien soll ein Mindestmaß an Sicherheit gewährleistet werden. Basisrichtlinien stehen in allen Editionen von Azure AD zur Verfügung und sind nur bedingt anpassbar. Sollten Sie mehr Flexibilität benötigen, deaktivieren Sie die Basisrichtlinie, und implementieren Sie eine benutzerdefinierte Standardrichtlinie, die Ihre Anforderungen erfüllt.

In einer Standardrichtlinie für bedingten Zugriff können Sie sämtliche Einstellungen anpassen, um die Richtlinie auf Ihre geschäftlichen Anforderungen abzustimmen. Für Standardrichtlinien wird eine Azure AD Premium P1-Lizenz benötigt.




## <a name="draft-policies"></a>Entwerfen von Richtlinien

Mit dem bedingten Zugriff von Azure Active Directory können Sie Ihre Cloud-Apps noch besser schützen. Der Zugriff auf eine Cloud-App basiert dabei nicht mehr auf einer statischen Zugriffskonfiguration, sondern auf einer dynamischen Richtlinienauswertung. Bei einer Richtlinie für bedingten Zugriff definieren Sie eine Reaktion (**Then do this** (auszuführende Aktion)) auf eine Zugriffsbedingung (**When this happens** (Ereignis)).

![Ursache und Reaktion](./media/plan-conditional-access/10.png)

Orientieren Sie sich bei jeder Richtlinie für bedingten Zugriff, die Sie implementieren möchten, an diesem Planungsmodell. Für die Planungsübung gilt Folgendes:

- Sie hilft Ihnen bei der Gliederung der Reaktionen und Bedingungen für die jeweilige Richtlinie.
- Sie liefert einen gut dokumentierten Katalog mit Richtlinien für bedingten Zugriff für Ihre Organisation. 

Anhand des Katalogs können Sie beurteilen, ob Ihre Richtlinienimplementierung die geschäftlichen Anforderungen Ihrer Organisation erfüllt. 

Verwenden Sie die folgende Beispielvorlage, um Richtlinien für bedingten Zugriff für Ihre Organisation zu erstellen:

|*Ereignis*:|*Auszuführende* Aktion:|
|-|-|
|Zugriffsversuch:<br>- Auf eine Cloud-App *<br>- Durch Benutzer und Gruppen*<br>Unter Verwendung von:<br>- Bedingung 1 (beispielsweise außerhalb des Unternehmensnetzwerks)<br>- Bedingung 2 (beispielsweise Geräteplattformen)|Zugriff auf die Anwendung blockieren|
|Zugriffsversuch:<br>- Auf eine Cloud-App *<br>- Durch Benutzer und Gruppen*<br>Unter Verwendung von:<br>- Bedingung 1 (beispielsweise außerhalb des Unternehmensnetzwerks)<br>- Bedingung 2 (beispielsweise Geräteplattformen)|Zugriff gewähren mit (UND):<br>- Anforderung 1 (beispielsweise MFA)<br>- Anforderung 2 (beispielsweise Gerätekonformität)|
|Zugriffsversuch:<br>- Auf eine Cloud-App *<br>- Durch Benutzer und Gruppen*<br>Unter Verwendung von:<br>- Bedingung 1 (beispielsweise außerhalb des Unternehmensnetzwerks)<br>- Bedingung 2 (beispielsweise Geräteplattformen)|Zugriff gewähren mit (ODER):<br>- Anforderung 1 (beispielsweise MFA)<br>- Anforderung 2 (beispielsweise Gerätekonformität)|

**Ereignis** definiert mindestens den Prinzipal (**wer**), der versucht, auf eine Cloud-App (**was**) zuzugreifen. Bei Bedarf können Sie auch mit einschließen, **wie** ein Zugriffsversuch erfolgt. Im Zusammenhang mit bedingtem Zugriff werden die Elemente, die das „Wer?“, „Was?“ und „Wie?“ definieren, als Bedingungen bezeichnet. Weitere Informationen finden Sie unter [Was sind Bedingungen beim bedingten Zugriff in Azure Active Directory?](conditions.md). 

Mit dem **auszuführenden Ereignis** definieren Sie die Reaktion Ihrer Richtlinie auf eine Zugriffsbedingung. In der Reaktion können Sie den Zugriff blockieren oder mit zusätzlichen Auflagen (beispielsweise mit mehrstufiger Authentifizierung (Multi-Factor Authentication, MFA)) gewähren. Eine vollständige Übersicht finden Sie unter [Was sind die Zugriffssteuerungen beim bedingten Zugriff mit Azure Active Directory?](controls.md).  
 

Die Kombination aus Bedingungen und Ihren Zugriffssteuerungen ergibt eine Richtlinie für bedingten Zugriff.

![Ursache und Reaktion](./media/plan-conditional-access/51.png)


Weitere Informationen finden Sie unter [Was ist erforderlich, damit eine Richtlinie funktioniert?](best-practices.md#whats-required-to-make-a-policy-work).

Nun ist eine gute Gelegenheit, um sich für einen Benennungsstandard für Ihre Richtlinien zu entscheiden. Der Benennungsstandard erleichtert die Suche nach Richtlinien und gibt Aufschluss über ihren Zweck, ohne sie im Azure-Verwaltungsportal öffnen zu müssen. Der Name Ihrer Richtlinie sollte Folgendes enthalten:

- Eine laufende Nummer
- Die Cloud-App, für die sie gilt
- Die Antwort
- Für wen sie gilt
- Wann sie gilt (falls zutreffend)
 
![Benennungsstandard](./media/plan-conditional-access/11.png)

Auch wenn ein aussagekräftiger Name hilft, die Übersicht über Ihre Implementierung für den bedingten Zugriff zu behalten, ist die Sequenznummer besonders dann hilfreich, wenn Sie in einer Konversation auf eine Richtlinie verweisen möchten. Wenn Sie beispielsweise mit einem anderen Administrator telefonieren, können Sie ihn auffordern, die Richtlinie EM063 zu öffnen, um ein Problem zu lösen.



Dem folgenden Namen kann beispielsweise entnommen werden, dass die Richtlinie die MFA für Marketingbenutzer in externen Netzwerken vorschreibt, die die Dynamics-CRP-App verwenden:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`


Neben Ihren aktiven Richtlinien sollten Sie auch deaktivierte Richtlinien implementieren, die als sekundäre [robuste Zugriffssteuerung für Ausfall-/Notfallszenarien](../authentication/concept-resilient-controls.md) fungieren. Ihr Benennungsstandard für die Notfallplan-Richtlinien sollte einige weitere Elemente enthalten: 

- `ENABLE IN EMERGENCY` am Anfang, damit sich der Name von denen anderer Richtlinien unterscheidet.

- Der Name der Störungen, auf die sie angewandt werden soll.

- Mithilfe einer Sequenznummer für die Sortierung wissen Administratoren sofort, in welcher Reihenfolge die Richtlinien aktiviert werden sollen. 


Der folgende Name gibt beispielsweise an, dass diese Richtlinie die erste von vier Richtlinien ist, die Sie im Fall von MFA-Unterbrechungen aktivieren sollten:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`







## <a name="plan-policies"></a>Planen von Richtlinien

Überlegen Sie sich bei der Planung Ihrer Richtlinienlösung für bedingten Zugriff, ob Sie Richtlinien erstellen müssen, um die folgenden Ergebnisse zu erzielen: 


### <a name="block-access"></a>Zugriff blockieren

Die Option zum Blockieren des Zugriffs zeichnet sich durch Folgendes aus:

- Sie hat Vorrang vor allen anderen Zuweisungen für einen Benutzer.

- Sie kann die Anmeldung bei Ihrem Mandanten für die gesamte Organisation blockieren.
 
Wenn Sie den Zugriff für alle Benutzer blockieren möchten, sollten Sie mindestens einen Benutzer (in der Regel Konten für den Notfallzugriff) von der Richtlinie ausnehmen. Weitere Informationen finden Sie unter [Auswählen von Benutzern und Cloud-Apps](block-legacy-authentication.md#select-users-and-cloud-apps).  
    

### <a name="require-mfa"></a>Anfordern von MFA

Um den Anmeldevorgang für Ihre Benutzer einfacher zu gestalten, möchten Sie ihnen vielleicht die Anmeldung bei Cloud-Apps über Benutzername und Kennwort gestatten. Meist gibt es jedoch einige Szenarien, in denen eine sicherere Form der Kontoüberprüfung angebracht ist. Mit einer Richtlinie für bedingten Zugriff können Sie die MFA-Anforderung auf bestimmte Szenarien beschränken. 

Gängige Anwendungsfälle für die MFA-Anforderung sind:

- [Zugriff durch Administratoren](howto-baseline-protect-administrators.md)
- [Zugriff auf bestimmte Apps](app-based-mfa.md) 
- [Zugriff über nicht vertrauenswürdige Netzwerkadressen](untrusted-networks.md)


### <a name="respond-to-potentially-compromised-accounts"></a>Reagieren auf potenziell gefährdete Konten

Mit Richtlinien für bedingten Zugriff können Sie automatisierte Reaktionen auf Anmeldungen mit potenziell gefährdeten Identitäten implementieren. Die Wahrscheinlichkeit, dass ein Konto gefährdet ist, wird in Form von Risikostufen ausgedrückt. Von Identity Protection werden zwei Risikostufen berechnet: Anmelderisiko und Benutzerrisiko. Bei der Implementierung einer Reaktion auf ein Anmelderisiko stehen zwei Optionen zur Verfügung:

- [Die Bedingung „Anmelderisiko“](conditions.md#sign-in-risk) in der Richtlinie für bedingten Zugriff
- [Die Richtlinie zum Anmelderisiko](../identity-protection/howto-sign-in-risk-policy.md) in Identity Protection 

Die Behandlung des Anmelderisikos als Bedingung ist die bevorzugte Methode, da Ihnen in diesem Fall mehr Anpassungsoptionen zur Verfügung stehen.

Die Benutzerrisikostufe ist nur als [Richtlinie zum Benutzerrisiko](../identity-protection/howto-user-risk-policy.md) in Identity Protection verfügbar. 

Weitere Informationen finden Sie unter [Was ist Azure Active Directory Identity Protection?](../identity-protection/overview.md). 


### <a name="require-managed-devices"></a>Vorschreiben der Verwendung verwalteter Geräte

Die zunehmende Verbreitung unterstützter Geräte für den Zugriff auf Ihre Cloudressourcen kommt der Produktivität Ihrer Benutzer zugute. Auf der anderen Seite gibt es wahrscheinlich Ressourcen in Ihrer Umgebung, die nicht für Geräte mit unbekannter Schutzebene zugänglich sein sollen. Für die betroffenen Ressourcen sollten Sie daher sicherstellen, dass Benutzer nur unter Verwendung eines verwalteten Geräts auf sie zugreifen können. Weitere Informationen finden Sie unter [Vorschreiben der Verwendung verwalteter Geräte für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Vorschreiben der Verwendung genehmigter Client-Apps

Bei BYOD-Szenarien (Bring Your Own Device) müssen Sie frühzeitig entscheiden, ob Sie das gesamte Gerät oder nur die darauf befindlichen Daten verwalten müssen. Ihre Mitarbeiter verwenden mobile Geräte sowohl für private als auch für berufliche Zwecke. Während Sie einerseits die Produktivität der Mitarbeiter sicherstellen möchten, sollten Sie andererseits Datenverluste vermeiden. Mit dem bedingten Zugriff mit Azure Active Directory (Azure AD) können Sie den Zugriff auf Ihre Cloud-Apps auf genehmigte Client-Apps beschränken, die Ihre Unternehmensdaten schützen können. Weitere Informationen finden Sie unter [Vorschreiben genehmigter Client-Apps für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs](app-based-conditional-access.md).


### <a name="block-legacy-authentication"></a>Blockieren älterer Authentifizierungsmethoden

Azure AD unterstützt mehrere der am häufigsten verwendeten Protokolle zur Authentifizierung und Autorisierung, einschließlich der Legacyauthentifizierung. Wie können Sie verhindern, dass Apps mit Legacyauthentifizierung auf Ressourcen Ihres Mandanten zugreifen? Es wird empfohlen, diese Apps einfach mit einer Richtlinie für bedingten Zugriff zu blockieren. Gegebenenfalls können Sie nur bestimmten Benutzern und bestimmten Netzwerkadressen die Verwendung von Apps erlauben, die auf der Legacyauthentifizierung basieren. Weitere Informationen finden Sie unter [Blockieren der Legacyauthentifizierung bei Azure AD mit bedingtem Zugriff](block-legacy-authentication.md).


## <a name="test-your-policy"></a>Testen Ihrer Richtlinie

Eine Richtlinie sollte vor dem Rollout in die Produktionsumgebung getestet werden, um sicherzustellen, dass sie wie erwartet funktioniert.

1. Erstellen Sie Testbenutzer.

2. Erstellen Sie einen Testplan.

3. Konfigurieren Sie die Richtlinie.

4. Auswerten einer simulierten Anmeldung

5. Testen Ihrer Richtlinie

6. Cleanup



### <a name="create-test-users"></a>Erstellen von Testbenutzern

Erstellen Sie zum Testen einer Richtlinie eine Gruppe von Benutzern, die den Benutzern in Ihrer Umgebung ähneln. Mithilfe der erstellten Testbenutzer können Sie sich vergewissern, dass Ihre Richtlinien erwartungsgemäß funktionieren, bevor Sie sie für echte Benutzer aktivieren und dadurch möglicherweise deren Zugriff auf Apps und Ressourcen unterbrechen. 


Einige Organisationen nutzen hierzu Testmandanten. Es kann sich jedoch als schwierig erweisen, sämtliche Bedingungen und Apps in einem Testmandanten nachzustellen, um das Ergebnis einer Richtlinie umfassend testen zu können. 

### <a name="create-a-test-plan"></a>Erstellen eines Testplans

Der Testplan ist wichtig, um die erwarteten Ergebnisse mit den tatsächlichen Ergebnissen vergleichen zu können. Sie sollten immer eine Erwartung haben, bevor Sie etwas testen. Die folgende Tabelle enthält einige Beispiele für Testfälle. Passen Sie die Szenarien und die erwarteten Ergebnisse auf der Grundlage der Konfiguration Ihrer Richtlinien für bedingten Zugriff an.

|Richtlinie |Szenario |Erwartetes Ergebnis | Ergebnis |
|---|---|---|---|
|[Anfordern der MFA, wenn nicht bei der Arbeit](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Ein autorisierter Benutzer meldet sich bei der *App* an, während er sich an einem vertrauenswürdigen Ort/bei der Arbeit befindet.|Der Benutzer wird nicht zur Verwendung der MFA aufgefordert.| |
|[Anfordern der MFA, wenn nicht bei der Arbeit](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Ein autorisierter Benutzer meldet sich bei der *App* an, während er sich nicht an einem vertrauenswürdigen Ort/bei der Arbeit befindet.|Der Benutzer wird zur Verwendung der MFA aufgefordert und kann sich erfolgreich anmelden.| |
|[Anfordern der MFA für Administratoren](https://docs.microsoft.com/azure/active-directory/conditional-access/baseline-protection#require-mfa-for-admins)|Ein globaler Administrator meldet sich bei der *App* an.|Der Administrator wird zur Verwendung der MFA aufgefordert.| |
|[Riskante Anmeldungen](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Ein Benutzer meldet sich bei der *App* über einen [Tor-Browser](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-playbook) an.|Der Administrator wird zur Verwendung der MFA aufgefordert.| |
|[Geräteverwaltung](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Ein autorisierter Benutzer versucht, sich über ein autorisiertes Gerät anzumelden.|Der Zugriff wird gewährt.| |
|[Geräteverwaltung](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Ein autorisierter Benutzer versucht, sich über ein nicht autorisiertes Gerät anzumelden.|Der Zugriff wird blockiert.| |
|[Kennwortänderung für riskante Benutzer](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)|Ein autorisierter Benutzer versucht, sich mit gefährdeten Anmeldeinformationen anzumelden (Anmeldung mit hohem Risiko).|Der Benutzer wird aufgefordert, das Kennwort zu ändern, oder der Zugriff wird blockiert (auf der Grundlage Ihrer Richtlinie).| |


### <a name="configure-the-policy"></a>Konfigurieren der Richtlinie

Richtlinien für bedingten Zugriff müssen manuell verwaltet werden. Im Azure-Portal können Sie Ihre Richtlinien für bedingten Zugriff an einem zentralen Ort verwalten: auf der Seite „Bedingter Zugriff“. Ein Einstiegspunkt für die Seite „Bedingter Zugriff“ ist der Abschnitt **Sicherheit** im Navigationsbereich **Active Directory**. 

![Bedingter Zugriff](media/plan-conditional-access/03.png)


Weitere Informationen zum Erstellen von Richtlinien für bedingten Zugriff finden Sie unter [Schnellstart: Anfordern der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) für bestimmte Apps über den bedingten Zugriff von Azure Active Directory](app-based-mfa.md). Diese Schnellstartanleitung enthält Folgendes:

- Informationen zur Benutzeroberfläche
- Einführung in die Funktionsweise des bedingten Zugriffs 


### <a name="evaluate-a-simulated-sign-in"></a>Auswerten einer simulierten Anmeldung

Nachdem Sie nun Ihre Richtlinie für bedingten Zugriff konfiguriert haben, möchten Sie wahrscheinlich wissen, ob sie erwartungsgemäß funktioniert. Verwenden Sie im ersten Schritt das [What-If-Richtlinientool](what-if-tool.md) des bedingten Zugriffs, um eine Anmeldung für Ihren Testbenutzer zu simulieren. Die Simulation schätzt die Auswirkungen dieser Anmeldungen auf Ihre Richtlinien ab und generiert einen Simulationsbericht.

>[!NOTE]
> Eine simulierte Ausführung vermittelt zwar einen Eindruck von den Auswirkungen einer Richtlinie für bedingten Zugriff, ist aber kein Ersatz für einen echten Testlauf.


### <a name="test-your-policy"></a>Testen Ihrer Richtlinie

Führen Sie Testfälle gemäß Ihrem Testplan aus. In diesem Schritt wird jede Richtlinie für Ihre Testbenutzer umfassend getestet, um sicherzustellen, dass sie ordnungsgemäß funktioniert. Verwenden Sie bei den einzelnen Tests jeweils die oben erstellten Szenarien.

Wichtig: Testen Sie unbedingt die Ausschlusskriterien einer Richtlinie. Sie können beispielsweise einen Benutzer oder eine Gruppe aus einer Richtlinie ausschließen, die eine mehrstufige Authentifizierung (MFA) erfordert. Daher empfiehlt es sich, zu testen, ob ausgeschlossene Benutzer zur Verwendung der MFA aufgefordert werden, weil die Kombination anderer Richtlinien möglicherweise die Verwendung der MFA für diese Benutzer vorschreibt.


### <a name="cleanup"></a>Cleanup

Die Bereinigung umfasst folgende Schritte:

1. Deaktivieren der Richtlinie

2. Entfernen der zugewiesenen Benutzer und Gruppen

3. Löschen der Testbenutzer  




## <a name="move-to-production"></a>Überführen in die Produktion

Wenn neue Richtlinien für Ihre Umgebung bereit sind, stellen Sie sie in Phasen bereit:

- Informieren Sie die Endbenutzer intern über die Änderung.

- Beginnen Sie mit einer kleinen Gruppe von Benutzern, und vergewissern Sie sich, dass das Verhalten der Richtlinie den Erwartungen entspricht.

- Wenn Sie eine Richtlinie auf einen größeren Benutzerkreis erweitern, schließen Sie dabei weiterhin alle Administratoren aus. Durch den Ausschluss der Administratoren ist gewährleistet, dass weiterhin jemand auf die Richtlinie zugreifen kann, sollte eine Änderung erforderlich sein.

- Wenden Sie eine Richtlinie nur dann auf alle Benutzer an, wenn dies erforderlich ist.

Es empfiehlt sich, mindestens ein Benutzerkonto zu erstellen, für das Folgendes gilt:

- Nur zur Richtlinienverwaltung gedacht

- Von allen Richtlinien ausgeschlossen

 



## <a name="rollback-steps"></a>Rollbackschritte

Sollte für Ihre neu implementierten Richtlinien ein Rollback erforderlich sein, verwenden Sie eine oder mehrere der folgenden Rollbackoptionen:

1. **Deaktivieren der Richtlinie:** Deaktivierte Richtlinien werden bei Anmeldeversuchen von Benutzern nicht angewendet. Die Richtlinie kann bei Bedarf jederzeit wieder aktiviert werden.

    ![Deaktivieren der Richtlinie](media/plan-conditional-access/07.png)

2. **Ausschließen eines Benutzers/einer Gruppe aus einer Richtlinie:** Falls ein Benutzer nicht auf die App zugreifen kann, können Sie ihn aus der Richtlinie ausschließen.

    ![Ausschließen von Benutzern](media/plan-conditional-access/08.png)

    >[!NOTE]
    > Diese Option sollte nur für vertrauenswürdige Benutzer verwendet werden. Der Benutzer sollte der Richtlinie oder Gruppe baldmöglichst wieder hinzugefügt werden.

3. **Löschen der Richtlinie:** Löschen Sie die Richtlinie, wenn Sie sie nicht mehr benötigen.

## <a name="next-steps"></a>Nächste Schritte

Verschaffen Sie sich in der [Dokumentation zum bedingten Zugriff mit Azure AD](index.yml) einen Überblick über die verfügbaren Informationen.
