---
title: 'Referenz: Vertrauensframeworks in Azure Active Directory B2C | Microsoft-Dokumentation'
description: Ein Thema zu benutzerdefinierten Azure Active Directory B2C-Richtlinien und zum Identity Experience Framework
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d3d29bd05f67d00047499dc256e5e1a82f98693a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95990982"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definieren von Vertrauens-Frameworks mit Azure AD B2C Identity Experience Framework

Benutzerdefinierte Azure Active Directory B2C-Richtlinien (Azure AD B2C), die das Identity Experience Framework nutzen, bieten ihrem Unternehmen einen zentralisierten Dienst. Dieser Dienst reduziert die Komplexität des Identitätsverbunds in einer großen Interessengemeinschaft. Die Komplexität wird auf eine einzelne Vertrauensstellung und einen einzelnen Metadatenaustausch reduziert.

Hierfür ist erforderlich, dass benutzerdefinierte Azure AD B2C-Richtlinien, die das Identity Experience Framework verwenden, Ihnen die Beantwortung der folgenden Fragen ermöglichen:

- Welche sind die Richtlinien, die sich auf rechtliche Fragen, die Sicherheit, den Datenschutz und den Schutz von Daten beziehen und eingehalten werden müssen?
- Wer sind die Kontakte, und wie werden Benutzer zu anerkannten Teilnehmern?
- Wer sind die anerkannten Anbieter von Identitätsinformationen (auch „Anspruchsanbieter“), und was bieten sie an?
- Wer sind die anerkannten vertrauenden Seiten (und was benötigen sie)? (optional)
- Was sind die technischen „On the Wire“-Interoperabilitätsanforderungen für Teilnehmer?
- Wie lauten die betriebsbezogenen Runtimeregeln, die zum Austausch von Informationen zur digitalen Identität durchgesetzt werden müssen?

Zur Beantwortung all dieser Fragen wird für die benutzerdefinierten Azure AD B2C-Richtlinien, für die das Identity Experience Framework eingesetzt wird, das Vertrauens-Framework-Konstrukt (Trust Framework, TF) genutzt. Wir sehen uns dieses Konstrukt und den damit erzielten Beitrag nun näher an.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Grundlegendes zum Vertrauens-Framework und zur zugrundeliegenden Verbundverwaltung

Das Vertrauens-Framework sollte als geschriebene Spezifikation der Richtlinien für Identität, Sicherheit, Datenschutz und Schutz von Daten verstanden werden, die von Teilnehmern einer Interessengemeinschaft eingehalten werden müssen.

Die Verbundidentität ist eine Basis zur Erzielung von Endbenutzer-Identitätszusicherung auf Internetniveau. Indem die Identitätsverwaltung an Drittanbieter delegiert wird, kann eine einzelne digitale Identität für einen Endbenutzer mit mehreren vertrauenden Seiten wiederverwendet werden.

Für die Sicherstellung der Identität ist es erforderlich, dass Identitätsanbieter (IdPs) und Attributanbieter (AtPs) bestimmte Richtlinien und Methoden in Bezug auf die Sicherheit, den Datenschutz und den Betrieb einhalten.  Da keine direkten Untersuchungen durchgeführt werden können, müssen vertrauende Seiten (Relying Parties, RPs) Vertrauensstellungen mit den IdPs und AtPs entwickeln, die jeweils ausgewählt werden.

Da die Zahl von Konsumenten und Anbietern von Informationen zur digitalen Identität ständig steigt, ist es nicht mehr haltbar, für diese Vertrauensstellungen die Verwaltung als Paare fortzusetzen. Dies gilt auch für den paarweisen Austausch der technischen Metadaten, die für die Netzwerkverbindung benötigt werden.  Mit Verbund-Hubs konnten diese Probleme nur in eingeschränktem Maße gelöst werden.

### <a name="what-a-trust-framework-specification-defines"></a>Was eine Framework Trust-Spezifikation definiert:
Vertrauensframeworks (TFs) sind der Dreh- und Angelpunkt des OIX-Vertrauensframework-Modells (Open Identity Exchange), bei dem jede Interessengemeinschaft mit einer bestimmten Vertrauensframework-Spezifikation gesteuert wird. Mit einer TF-Spezifikation dieser Art wird Folgendes definiert:

- **Die Sicherheits- und Datenschutzmetriken für die Interessengemeinschaft mit der folgenden Definition**:
    - Die von Teilnehmern angebotenen bzw. benötigten Zusicherungsebenen (Levels of Assurance, LOA), also eine sortierte Gruppe mit Vertrauensbewertungen für die Authentizität der Informationen zur digitalen Identität.
    - Die von Teilnehmern angebotenen bzw. benötigten Schutzebenen (Levels of Protection, LOP), also eine sortierte Gruppe mit Vertrauensbewertungen für den Schutz der Informationen zur digitalen Identität, die von Teilnehmern der Interessengemeinschaft verwaltet wird.

- **Die Beschreibung der Informationen zur digitalen Identität, die von Teilnehmern angeboten bzw. benötigt werden**.

- **Die technischen Richtlinien für die Erzeugung und Nutzung von Informationen zur digitalen Identität und somit zum Messen von LOA und LOP. Diese schriftlichen Richtlinien umfassen normalerweise die folgenden Kategorien von Richtlinien:**
    - Richtlinien zur Identitätsüberprüfung, z.B.: *Wie streng werden die Identitätsinformationen einer Person überprüft?*
    - Sicherheitsrichtlinien, z.B.: *Wie gut ist die Integrität und Vertraulichkeit der Informationen geschützt?*
    - Datenschutzrichtlinien, z.B.: *Welches Maß an Kontrolle hat ein Benutzer über seine personenbezogenen Informationen (Personally Identifiable Information, PII)* ?
    - Richtlinien zur „Überlebensfähigkeit“ (Survivability), z.B.: *Kontinuität und Schutz von personenbezogenen Informationen, wenn ein Anbieter den Betrieb einstellt.*

- **Die technischen Profile für die Erzeugung und Nutzung von Informationen zur digitalen Identität. Die Profile enthalten:**
    - Festlegen des Umfangs von Schnittstellen, für die Informationen zur digitalen Identität auf der angegebenen Zusicherungsebene (LOA) verfügbar sind.
    - Beschreiben der technischen Anforderungen für die „On the Wire“-Interoperabilität.

- **Die Beschreibung der verschiedenen Rollen, die Teilnehmer in der Gemeinschaft ausüben können, zusammen mit den Qualifikationen, die zum Ausüben dieser Rollen erforderlich sind.**

Daher wird mit einer TF-Spezifikation gesteuert, wie Identitätsinformationen zwischen den Teilnehmern der Interessengemeinschaft ausgetauscht werden: vertrauende Seiten, Identitäts- und Attributanbieter und Attributverifizierer.

Eine TF-Spezifikation umfasst ein oder mehrere Dokumente, die als Referenz für die Steuerung der Interessengemeinschaft dienen und die Durchsetzung und Nutzung von Informationen zur digitalen Identität innerhalb der Gemeinschaft regeln. Es handelt sich also um eine Reihe von Richtlinien und Verfahren, die für das Herstellen von Vertrauen in die digitalen Identitäten für Onlinetransaktionen zwischen Mitgliedern einer Interessengemeinschaft ausgelegt sind.

Mit anderen Worten werden in einer TF-Spezifikation die Regeln zum Erstellen eines wettbewerbsfähigen Verbundökosystems für die Identität einer Gemeinschaft definiert.

Derzeit herrscht ein breiter Konsens über die Vorteile eines derartigen Ansatz. Es besteht kein Zweifel, dass Vertrauens-Framework-Spezifikationen die Entwicklung von digitalen Identitätsökosystemen mit Merkmalen der überprüfbaren Sicherheit, der Sicherheit und des Datenschutzes begünstigen, was bedeutet, dass sie von verschiedenen Interessensgemeinschaften wiederverwendet werden können.

Aus diesem Grund nutzen benutzerdefinierte Azure AD B2C-Richtlinien, die das Identity Experience Framework nutzen, die Spezifikation als Basis für ihre Datendarstellung für ein TF, um die Interoperabilität zu vereinfachen.

Azure AD B2C benutzerdefinierte Richtlinien, welche das Identity Experience Framework nutzen, stellen eine TF-Spezifikation als eine Mischung von Menschen und maschinenlesbaren Daten dar. Einige Abschnitte dieses Modells (üblicherweise Abschnitte, die mehr auf Governance ausgerichtet sind) werden als Verweise auf die veröffentlichten Dokumentationen zu Sicherheit und Datenschutz sowie die damit verbundenen Prozeduren (sofern vorhanden) dargestellt. Andere Abschnitte beschreiben ausführlich die Regeln bezüglich Konfiguration, Metadaten und Laufzeit, die betriebliche Automatisierung erleichtern.

## <a name="understand-trust-framework-policies"></a>Grundlegendes zu Vertrauens-Framework-Richtlinien

Im Hinblick auf die Implementierung besteht die TF-Spezifikation aus einer Reihe von Richtlinien, welche die vollständige Kontrolle über das Verhalten von Identitäten und Erfahrungen ermöglichen.  Mit benutzerdefinierten Azure AD B2C-Richtlinien in Verbindung mit dem Identity Experience Framework können Sie Ihr eigenes Vertrauens-Framework erstellen, indem Sie deklarative Richtlinien nutzen, mit denen Folgendes definiert und konfiguriert werden kann:

- Die Dokumentverweise, mit denen das Verbundökosystem für die Identität einer Gemeinschaft für das Vertrauens-Framework definiert wird. Dies sind Links zur Dokumentation zu Vertrauensframeworks. Die (vordefinierten) betriebsbezogenen Runtimeregeln oder die User Journeys, mit denen der Austausch und die Nutzung von Ansprüchen automatisiert bzw. gesteuert wird. Diese User Journeys sind einer Zusicherungsebene (LOA) und einer Schutzebene (LOP) zugeordnet. Eine Richtlinie kann daher über User Journeys mit unterschiedlichen LOAs (und LOPs) verfügen.

- Die Identitäts- und Attributanbieter oder die Anspruchsanbieter in der Interessengemeinschaft und die unterstützten technischen Profile zusammen mit der LOA/LOP-Akkreditierung („Out of Band“), die sich darauf bezieht.

- Die Integration mit Attributverifizierern oder Anspruchsanbietern.

- Die vertrauenden Seiten in der Gemeinschaft (nach Rückschluss).

- Die Metadaten zum Einrichten der Netzwerkkommunikation zwischen Teilnehmern. Diese Metadaten werden zusammen mit den technischen Profilen im Verlauf einer Transaktion verwendet, um die „On the Wire“-Interoperabilität zwischen der vertrauenden Seite und anderen Teilnehmern der Gemeinschaft herzustellen.

- Die Protokollkonvertierung, falls vorhanden (z.B. SAML 2.0, OAuth2, WS-Verbund und OpenID Connect).

- Die Authentifizierungsanforderungen.

- Die Multi-Factor-Orchestrierung, falls vorhanden.

- Ein gemeinsames Schema für alle verfügbaren Ansprüche und Zuordnungen zu Teilnehmern einer Interessengemeinschaft.

- Alle Anspruchstransformationen, zusammen mit der möglichen Datenminimierung in diesem Kontext, zur Bewältigung des Austauschs und der Nutzung von Ansprüchen.

- Die Bindung und die Verschlüsselung.

- Der Anspruchsspeicher.

### <a name="understand-claims"></a>Verstehen von Ansprüchen

> [!NOTE]
> Wir bezeichnen alle möglichen Typen von Identitätsinformationen, die ausgetauscht werden können, als Ansprüche: Ansprüche in Bezug auf die Anmeldeinformationen eines Endbenutzers für die Authentifizierung, Identitätsüberprüfung, Kommunikationsgerät, physischer Standort, personenbezogene Attribute usw.
>
> Wir verwenden den Begriff „Ansprüche“ statt „Attribute“, da es sich in Onlinetransaktionen bei diesen Datenartefakten nicht um Fakten handelt, die direkt durch die vertrauende Seite überprüft werden können. Stattdessen handelt es sich um Assertionen oder Ansprüche zu Fakten für die die vertrauende Seite ein ausreichendes Maß an Vertrauen haben muss, um die angeforderte Transaktion für den Endbenutzer zu gewähren.
>
> Ein weiterer Grund ist die Tatsache, dass benutzerdefinierte Azure AD B2C-Richtlinien, die das Identity Experience Framework nutzen, für die Vereinfachung des Austauschs aller Arten von Informationen zur digitalen Identität auf einheitliche Weise ausgelegt sind. Dies gilt unabhängig davon, ob das zugrunde liegende Protokoll für die Benutzerauthentifizierung oder den Attributabruf definiert ist.  Ebenso verwenden wir den Ausdruck „Anspruchsanbieter“ zur allgemeinen Bezeichnung von Identitätsanbietern, Attributanbietern und Attributverifizierern, wenn nicht zwischen den jeweiligen spezifischen Funktionen unterschieden werden soll.

Mit diesen Anbietern wird gesteuert, wie Identitätsinformationen zwischen einer vertrauenden Seite, Identitäts- und Attributanbietern und Attributverifizierern ausgetauscht werden. Sie steuern, welche Identitäts- und Attributanbieter für die Authentifizierung einer vertrauenden Seite erforderlich sind. Dies kann als domänenspezifische Sprache (DSL) angesehen werden. Hierbei handelt es sich um eine Computersprache, die für eine bestimmte Anwendungsdomäne mit Vererbung, *if*-Anweisungen und Polymorphie spezialisiert ist.

Diese Richtlinien stellen den maschinenlesbaren Teil des TF-Konstrukts von benutzerdefinierten Richtlinien in Azure AD B2C dar, für die das Identity Experience Framework genutzt wird. Sie enthalten alle betriebsbezogenen Details, einschließlich Anspruchsanbietern Metadaten und technischen Profilen, Anspruchsschemadefinitionen, Anspruchstransformationsfunktionen und User Journeys, die zur Vereinfachung der Orchestrierung und der Automatisierung eingefügt werden.

Sie werden als *lebende Dokumente* betrachtet, da sich der Inhalt mit hoher Wahrscheinlichkeit im Laufe der Zeit, in Bezug auf die aktiven Teilnehmer, ändert, die in den Richtlinien deklariert werden. Zudem besteht die Möglichkeit, dass eine Änderung der allgemeinen Geschäftsbedingungen für Teilnehmer eintritt.

Das Einrichten und Verwalten des Verbunds wird erheblich vereinfacht, indem vertrauende Seiten gegenüber laufenden Neukonfigurationen von Vertrauensstellungen und der Konnektivität abgeschirmt werden, wenn unterschiedliche Anspruchsanbieter/-verifizierer dem Richtliniensatz (der die Gemeinschaft repräsentiert) beitreten oder diesen verlassen.

Interoperabilität ist eine andere wesentliche Herausforderung. Zusätzliche Anspruchsanbieter/Prüfer müssen einbezogen werden, da es unwahrscheinlich ist, dass vertrauende Seiten alle erforderlichen Protokolle unterstützen. Mit benutzerdefinierten Azure AD B2C-Richtlinien wird dieses Problem gelöst, indem Branchenstandardprotokolle unterstützt und bestimmte User Journeys angewendet werden. Sie werden zum Umstellen von Anforderungen verwendet, wenn vertrauende Seiten und Attributanbieter nicht das gleiche Protokoll unterstützen.

User Journeys enthalten Protokollprofile und Metadaten, um die „On the Wire“-Interoperabilität zwischen der vertrauenden Seite und anderen Teilnehmern zu installieren. Außerdem sind Regeln für die betriebsbezogene Runtime vorhanden, die auf Anforderung/Antwort-Nachrichten für den Austausch von Identitätsinformationen angewendet werden, um die Konformität mit veröffentlichten Richtlinien im Rahmen der TF-Spezifikation durchzusetzen. Das Konzept der Benutzer Journeys ist von entscheidender Bedeutung für die Anpassung der Kundenerfahrung. Es verdeutlicht auch, wie das System auf Protokollebene funktioniert.

Auf dieser Basis können Anwendungen und Portale der vertrauenden Seite je nach Kontext benutzerdefinierte Azure AD B2C Richtlinien mit Nutzung des Identity Experience Framework aufrufen, indem der Name einer bestimmten Richtlinie übergeben wird. So können genau das gewünschte Verhalten und der gewünschte Informationsaustausch erzielt werden, ohne dass hoher Aufwand anfällt oder ein Risiko besteht.
