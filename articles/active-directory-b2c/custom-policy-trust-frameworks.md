---
title: Übersicht über benutzerdefinierte Richtlinien in Azure AD B2C | Microsoft-Dokumentation
description: Ein Thema zu benutzerdefinierten Azure Active Directory B2C-Richtlinien und zum Identity Experience Framework
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb33e11af26d5f5a2676f5b236ac142179bdb550
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592839"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Übersicht über Benutzerdefinierte Azure AD B2C-Richtlinien

Benutzerdefinierte Richtlinien sind Konfigurationsdateien, die das Verhalten Ihres Azure Active Directory B2C-Mandanten (Azure AD B2C-Mandanten) definieren. Während [Benutzerflows](user-flow-overview.md) im Azure AD B2C-Portal für die gängigsten Identitätsaufgaben vordefiniert sind, können benutzerdefinierte Richtlinien von einem Identitätsentwickler vollständig bearbeitet werden, um die Ausführung vieler verschiedener Aufgaben zu ermöglichen.

Eine benutzerdefinierte Richtlinie ist vollständig konfigurierbar und richtliniengesteuert. Eine benutzerdefinierte Richtlinie koordiniert die Vertrauensstellungen zwischen Entitäten in Standardprotokollformaten wie OpenID Connect, OAuth, SAML sowie einigen nicht standardmäßigen Formaten. Ein Beispiel hierfür ist der REST-API-basierte Austausch von Systemansprüchen. Das Framework erstellt benutzerfreundliche Umgebungen mit Positivlisten.

Eine benutzerdefinierte Richtlinie wird als eine oder mehrere XML-formatierte Dateien dargestellt, die aufeinander in einer hierarchischen Kette verweisen. Die XML-Elemente definieren die Bausteine, die Interaktion mit dem Benutzer und anderen Parteien sowie die Geschäftslogik. 

## <a name="custom-policy-starter-pack"></a>Starter Pack für benutzerdefinierte Richtlinien

Das [Starter Pack](custom-policy-get-started.md#get-the-starter-pack) mit benutzerdefinierten Richtlinien für Azure AD B2C enthält mehrere vordefinierte Richtlinien, damit Sie schnell loslegen können. Jedes dieser Starter Packs enthält die kleinste Anzahl von technischen Profilen sowie die zum Erreichen der beschriebenen Szenarien benötigten User Journeys:

- **LocalAccounts** ermöglicht die ausschließliche Nutzung von lokalen Konten.
- **SocialAccounts** ermöglicht die ausschließliche Nutzung von Konten sozialer Netzwerke (oder Verbundkonten).
- **SocialAndLocalAccounts** ermöglicht sowohl die Verwendung von lokalen Konten als auch von Konten für soziale Netzwerke. Die meisten unserer Beispiele beziehen sich auf diese Richtlinie.
- **SocialAndLocalAccountsWithMFA** ermöglicht lokale Optionen und Optionen für soziale Netzwerke sowie zur Multi-Factor Authentication.

## <a name="understanding-the-basics"></a>Verstehen der Grundlagen 

### <a name="claims"></a>Ansprüche

Ein Anspruch bietet eine temporäre Speicherung von Daten während der Ausführung einer Azure AD B2C-Richtlinie. Er kann Informationen zum Benutzer, wie z. B. Vorname oder Nachname, oder andere Ansprüche speichern, die vom Benutzer oder anderen Systemen (Anspruchsaustausch) erhalten wurden. Im [Anspruchsschema](claimsschema.md) deklarieren Sie Ihre Ansprüche. 

Beim Ausführen der Richtlinie sendet Azure AD B2C Ansprüche an interne und externe Parteien und empfängt Ansprüche von diesen. Anschließend wird eine Teilmenge dieser Ansprüche als Teil des Tokens an die Anwendung der vertrauenden Seite gesendet. Ansprüche werden auf folgende Weise verwendet: 

- Ein Anspruch wird für das Verzeichnisbenutzerobjekt gespeichert, gelesen oder aktualisiert.
- Ein Anspruch wird von einem externen Identitätsanbieter empfangen.
- Ansprüche werden über einen benutzerdefinierten REST-API-Dienst gesendet oder empfangen.
- Daten werden während der Flows zur Registrierung oder Bearbeitung des Profils als Ansprüche vom Benutzer gesammelt.

### <a name="manipulating-your-claims"></a>Bearbeiten von Ansprüchen

[Anspruchstransformationen](claimstransformations.md) sind vordefinierte Funktionen, mit denen Sie einen bestimmten Anspruch in einen anderen konvertieren, einen Anspruch evaluieren oder einen Anspruchswert festlegen können. Beispiele sind das Hinzufügen eines Elements zu einer Zeichenfolgensammlung, das Ändern der Groß-/Kleinschreibung einer Zeichenfolge oder das Auswerten eines Anspruchs mit Datums- und Uhrzeitangaben. Die Anspruchstransformation gibt dabei die Transformationsmethode an. 

### <a name="customize-and-localize-your-ui"></a>Anpassen und Lokalisieren Ihrer Benutzeroberfläche

Wenn Sie Informationen von Ihren Benutzern sammeln möchten, indem Sie eine Seite in ihrem Webbrowser anzeigen, verwenden Sie das [selbstbestätigte technische Profil](self-asserted-technical-profile.md). Sie können Ihr selbstbestätigtes technisches Profil bearbeiten, indem Sie [Ansprüche hinzufügen und Benutzereingaben anpassen](./configure-user-input.md).

Zum [Anpassen der Benutzeroberfläche](customize-ui-with-html.md) für Ihr selbstbestätigtes technisches Profil geben Sie im [ContentDefinition](contentdefinitions.md)-Element eine URL mit benutzerdefiniertem HTML-Inhalt an. Verweisen Sie im selbstbestätigten technischen Profil auf die ID dieser Inhaltsdefinition.

Wenn Sie sprachspezifische Zeichenfolgen anpassen möchten, verwenden Sie das [Localization](localization.md)-Element. Eine Inhaltsdefinition kann einen Verweis auf ein [Localization](localization.md)-Element mit einer Liste der lokalisierten Ressourcen enthalten, die geladen werden sollen. Azure AD B2C führt die Benutzeroberflächenelemente mit dem HTML-Inhalt, der über Ihre URL geladen wird, zusammen und zeigt anschließend die Seite für den Benutzer an. 

## <a name="relying-party-policy-overview"></a>Übersicht über die Richtlinie für die vertrauende Seite

Eine Anwendung der vertrauenden Seite, die im SAML-Protokoll als Dienstanbieter bezeichnet wird, ruft die [Richtlinie der vertrauenden Seite](relyingparty.md) auf, um eine bestimmte User Journey auszuführen. Die Richtlinie der vertrauenden Seite gibt die auszuführende User Journey sowie eine Liste der Ansprüche an, die das Token enthält. 

![Diagramm mit dem Flow der Richtlinienausführung](./media/custom-policy-trust-frameworks/custom-policy-execution.png)

Alle Anwendungen der vertrauenden Seite, die dieselbe Richtlinie verwenden, erhalten dieselben Tokenansprüche, und der Benutzer durchläuft die gleiche User Journey.

### <a name="user-journeys"></a>User Journeys

Mithilfe von [User Journeys](userjourneys.md) können Sie die Geschäftslogik durch den Pfad definieren, den Benutzer befolgen müssen, um Zugriff auf Ihre Anwendung zu erhalten. Die Benutzer werden durch diese User Journey geleitet, um die Ansprüche abzurufen, die an Ihre Anwendung übermittelt werden sollen. Eine User Journey besteht aus einer Sequenz von [Orchestrierungsschritten](userjourneys.md#orchestrationsteps). Benutzer müssen den letzten Schritt erreichen, um ein Token zu erhalten. 

In den folgenden Anweisungen wird beschrieben, wie Sie der Richtlinie im [Starter Pack für Konten sozialer Netzwerke und lokale Konten](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) Orchestrierungsschritte hinzufügen können. Nachfolgend finden Sie ein Beispiel für einen REST-API-Aufruf, der hinzugefügt wurde.

![Angepasste User Journey](media/custom-policy-trust-frameworks/user-journey-flow.png)


### <a name="orchestration-steps"></a>Orchestrierungsschritte

Der Orchestrierungsschritt verweist auf eine Methode, die den beabsichtigten Zweck oder die gewünschte Funktionalität implementiert. Diese Methode wird als ein [technisches Profil](technicalprofiles.md) bezeichnet. Wenn die User Journey Verzweigungen erfordert, damit die Geschäftslogik besser dargestellt werden kann, verweist der Orchestrierungsschritt auf die [untergeordnete Journey](subjourneys.md). Eine untergeordnete Journey enthält eigene Orchestrierungsschritte.

Benutzer müssen den letzten Orchestrierungsschritt der User Journey erreichen, um ein Token zu erhalten. Möglicherweise müssen die Benutzer dabei allerdings nicht alle Orchestrierungsschritte durchlaufen. Orchestrierungsschritte können anhand von [Voraussetzungen](userjourneys.md#preconditions), die im Orchestrierungsschritt definiert werden, bedingungsabhängig ausgeführt werden. 

Nachdem ein Orchestrierungsschritt abgeschlossen wurde, speichert Azure AD B2C die ausgegebenen Ansprüche im **Anspruchsbehälter**. Die Ansprüche im Anspruchsbehälter können von allen nachfolgenden Orchestrierungsschritten der User Journey verwendet werden.

Das folgende Diagramm zeigt, wie die Orchestrierungsschritte der User Journey auf den Anspruchsbehälter zugreifen können.

![Azure AD B2C-User Journey](media/custom-policy-trust-frameworks/user-journey-diagram.png)

### <a name="technical-profile"></a>Technisches Profil

Ein technisches Profil bietet eine Schnittstelle für die Kommunikation mit verschiedenen Typen von Parteien. Eine User Journey kombiniert Aufrufe von technischen Profilen mithilfe von Orchestrierungsschritten, um so Ihre Geschäftslogik zu definieren.

Allen Typen von technischen Profilen liegt das gleiche Konzept zugrunde. Sie senden Eingabeansprüche, führen Anspruchstransformation aus und kommunizieren mit der konfigurierten Partei. Nach Abschluss des Prozesses gibt das technische Profil die Ausgabeansprüche an den Anspruchsbehälter zurück. Weitere Informationen finden Sie unter [Übersicht über technische Profile](technicalprofiles.md).

### <a name="validation-technical-profile"></a>Technisches Validierungsprofil

Wenn Benutzer mit der Benutzeroberfläche interagieren, können Sie die dabei gesammelten Daten überprüfen. Für die Interaktion mit Benutzern muss ein [selbstbestätigtes technisches Profil](self-asserted-technical-profile.md) verwendet werden.

Zum Überprüfen der Benutzereingabe wird ein [technisches Validierungsprofil](validation-technical-profile.md) vom selbstbestätigten technischen Profil aufgerufen. Ein technisches Validierungsprofil stellt eine Methode dar, beliebige nicht interaktive technische Profile aufzurufen. In diesem Fall kann das technische Profil Ausgabeansprüche oder eine Fehlermeldung zurückgeben. Die Fehlermeldung wird für Benutzer auf dem Bildschirm gerendert, sodass sie den Vorgang wiederholen können.

Im folgenden Diagramm wird veranschaulicht, wie Azure AD B2C die Benutzeranmeldeinformationen mit einem technischen Validierungsprofil überprüft.

![Diagramm mit technischem Validierungsprofil](media/custom-policy-trust-frameworks/validation-technical-profile.png)

## <a name="inheritance-model"></a>Vererbungsmodell

Jedes Starter Pack enthält die folgenden Dateien:

- Eine **Basisdatei** enthält die meisten Definitionen. Um die Problembehandlung und langfristige Verwaltung Ihrer Richtlinien zu vereinfachen, sollten Sie versuchen, möglichst wenige Änderungen an dieser Datei vorzunehmen.
- Eine **Erweiterungendatei** enthält die eindeutigen Konfigurationsänderungen für Ihren Mandanten. Diese Richtliniendatei wird von der Basisdatei abgeleitet. Verwenden Sie diese Datei, um neue Funktionalität hinzufügen oder vorhandene Funktionen zu überschreiben. Sie verwenden diese Datei z.B. für einen Verbund mit neuen Identitätsanbietern.
- Eine **Datei der vertrauenden Seite** stellt die einzige aufgabenorientierte Datei dar, die direkt von der Anwendung der vertrauenden Seite aufgerufen wird, z.B. Ihren Web-, mobilen oder Desktopanwendungen. Jede eindeutige Aufgabe wie das Registrieren oder Anmelden, das Zurücksetzen des Kennworts oder die Profilbearbeitung erfordert eine eigene Richtliniendatei der vertrauenden Seite. Diese Richtliniendatei wird von der Erweiterungsdatei abgeleitet.

Das Vererbungsmodell lautet wie folgt:

- Die untergeordnete Richtlinie kann auf jeder Ebene von der übergeordneten Richtlinie erben und diese durch Hinzufügen neuer Elemente erweitern.
- In komplexeren Szenarien können Sie weitere Vererbungsebenen (bis zu insgesamt 10 Ebenen) hinzufügen.
- Sie können auch zusätzliche Richtlinien der vertrauenden Seite hinzufügen. Dies können z. B. Richtlinien für das Löschen des eigenen Kontos, das Ändern einer Telefonnummer, eine SAML-Richtlinie der vertrauenden Seite usw. sein.

Das folgende Diagramm zeigt die Beziehung zwischen den Richtliniendateien und den Anwendungen der vertrauenden Seite.

![Diagramm mit dem Richtlinien-Vererbungsmodell des Vertrauensframeworks](media/custom-policy-trust-frameworks/policies.png)


## <a name="guidance-and-best-practices"></a>Leitfaden und bewährte Methoden

### <a name="best-practices"></a>Bewährte Methoden

Sie können Ihre eigene Geschäftslogik in eine benutzerdefinierte Azure AD B2C-Richtlinie integrieren, um die gewünschten Benutzerumgebungen zu erstellen und die Funktionalität des Diensts zu erweitern. Dazu stehen Ihnen verschiedene bewährte Methoden und Empfehlungen für den Einstieg zur Verfügung.

- Erstellen Sie Ihre Logik in der **Erweiterungsrichtlinie** oder in der **Richtlinie der vertrauenden Seite**. Sie können neue Elemente hinzufügen, die die Basisrichtlinie überschreiben, indem Sie auf dieselbe ID verweisen. Auf diese Weise können Sie Ihr Projekt aufskalieren und gleichzeitig spätere Upgrades der Basisrichtlinie vereinfachen, wenn Microsoft neue Starter Packs veröffentlicht.
- Es wird dringend davon abgeraten, Änderungen an der **Basisrichtlinie** vorzunehmen. Falls doch Änderungen vorgenommen werden müssen, fügen Sie an den entsprechenden Stellen Kommentare ein.
- Falls Sie ein Element überschreiben (z. B. die Metadaten eines technischen Profils), vermeiden Sie das Kopieren des gesamten technischen Profils aus der Basisrichtlinie. Kopieren Sie stattdessen nur den benötigten Abschnitt des Elements. Ein Beispiel für diese Änderung finden Sie unter [Deaktivieren der E-Mail-Überprüfung während der Kundenregistrierung in Azure Active Directory B2C](./disable-email-verification.md).
- Um doppelte technische Profile mit identischer Kernfunktionalität zu minimieren, nutzen Sie die [Inklusion technischer Profile](technicalprofiles.md#include-technical-profile).
- Vermeiden Sie es, während der Anmeldung in das Azure AD-Verzeichnis zu schreiben, da dies zu Drosselungsproblemen führen kann.
- Wenn Ihre Richtlinie externe Abhängigkeiten aufweist, wie z. B. REST-APIs, stellen Sie sicher, dass sie hochverfügbar sind.
- Für mehr Benutzerfreundlichkeit stellen Sie durch [Online-Content Delivery](../cdn/index.yml) sicher, dass Ihre benutzerdefinierten HTML-Vorlagen global bereitgestellt werden. Mit Azure Content Delivery Network (CDN) können Sie Ladezeiten reduzieren, Bandbreite sparen und die Reaktionsgeschwindigkeit erhöhen.
- Wenn Sie an der User Journey eine Änderung vornehmen möchten, kopieren Sie die gesamte User Journey aus der Basisrichtlinie in die Erweiterungsrichtlinie. Weisen Sie der kopierten User Journey eine eindeutige User Journey-ID zu. Ändern Sie dann in der [Richtlinien der vertrauenden Seite](relyingparty.md) das [DefaultUserJourney](relyingparty.md#defaultuserjourney)-Element so, dass es auf die neue User Journey verweist.

## <a name="troubleshooting"></a>Problembehandlung

Beim Entwickeln mit Azure AD B2C-Richtlinien können beim Ausführen Ihrer User Journey Fehler oder Ausnahmen auftreten. Diese können mithilfe von Application Insights untersucht werden.

- Integrieren Sie Application Insights mit Azure AD B2C, um [Ausnahmen zu diagnostizieren](troubleshoot-with-application-insights.md).
- Die [Azure AD B2C-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) vereinfacht das Zugreifen auf und das [Visualisieren von Protokollen](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md) basierend auf einem Richtliniennamen und der Uhrzeit.
- Der häufigste Fehler beim Einrichten von benutzerdefinierten Richtlinien ist falsch formatierter XML-Code. Mit der [XML-Schemavalidierung](troubleshoot-custom-policies.md) können Sie Fehler identifizieren, bevor Sie Ihre XML-Datei hochladen.

## <a name="continuous-integration"></a>Continuous Integration

Mithilfe einer CI/CD-Pipeline (Continuous Integration/Continuous Delivery), die Sie in Azure Pipelines einrichten, können Sie [benutzerdefinierte Azure AD B2C-Richtlinien in Ihre Softwarebereitstellung](deploy-custom-policies-devops.md) und die Codesteuerungsautomatisierung einbeziehen. Bei der Bereitstellung in unterschiedlichen Azure AD B2C-Umgebungen (z. B. Entwicklung, Test und Produktion) empfiehlt es sich, manuelle Prozesse zu entfernen und automatisierte Tests mithilfe von Azure Pipelines auszuführen.

## <a name="prepare-your-environment"></a>Vorbereiten der Umgebung

Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C:

1. [Erstellen eines Azure AD B2C-Mandanten](tutorial-create-tenant.md)
1. [Registrieren Sie im Azure-Portal eine Webanwendung](tutorial-register-applications.md), damit Sie Ihre Richtlinie testen können.
1. Fügen Sie die erforderlichen [Richtlinienschlüssel](custom-policy-get-started.md#add-signing-and-encryption-keys) hinzu, und [registrieren Sie die Identity Experience Framework-Anwendungen](custom-policy-get-started.md#register-identity-experience-framework-applications).
1. [Laden Sie das Starter Pack für Azure AD B2C-Richtlinien herunter](custom-policy-get-started.md#get-the-starter-pack) und in Ihren Mandanten hoch. 
1. Nachdem Sie das Starter Pack hochgeladen haben, [testen Sie Ihre Registrierungs- oder Anmelderichtlinie](custom-policy-get-started.md#test-the-custom-policy).
1. Es wird empfohlen, [Visual Studio Code](https://code.visualstudio.com/) (VS Code) herunterzuladen und zu installieren. Visual Studio Code ist ein einfacher, aber dennoch leistungsfähiger Quellcode-Editor, der auf Ihrem Desktop ausgeführt wird und für Windows, macOS und Linux verfügbar ist. Mit VS Code können Sie schnell durch Ihre benutzerdefinierten Azure AD B2C-Richtliniendateien (XML-Dateien) navigieren und diese bearbeiten, indem Sie die [Azure AD B2C-Erweiterung für VS Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) installieren.
 
## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Azure AD B2C-Richtlinie eingerichtet und getestet haben, können Sie mit der Anpassung Ihrer Richtlinie beginnen. Die Vorgehensweise erfahren Sie in den folgenden Artikeln:

- [Hinzufügen von Ansprüchen und Anpassen von Benutzereingaben mit benutzerdefinierten Richtlinien](./configure-user-input.md). Erfahren Sie, wie Sie einen Anspruch definieren und der Benutzeroberfläche einen Anspruch hinzufügen, indem Sie einige der technischen Profile des Starter Packs anpassen.
- [Anpassen der Benutzeroberfläche Ihrer Anwendung mit einer benutzerdefinierten Richtlinie](customize-ui-with-html.md). Erfahren Sie, wie Sie eigene HTML-Inhalte erstellen und die Inhaltsdefinition anpassen.
- [Lokalisieren der Benutzeroberfläche Ihrer Anwendung mit einer benutzerdefinierten Richtlinie](./language-customization.md). Erfahren Sie, wie Sie eine Liste mit unterstützten Sprachen einrichten und sprachspezifische Bezeichnungen bereitstellen, indem Sie das Element mit lokalisierten Ressourcen hinzufügen.
- Während Sie Ihre Richtlinie entwickeln und testen, können Sie die [E-Mail-Überprüfung deaktivieren](./disable-email-verification.md). Erfahren Sie, wie Sie die Metadaten eines technischen Profils überschreiben.
- [Einrichten der Anmeldung mit einem Google-Konto mithilfe benutzerdefinierter Richtlinien](./identity-provider-google.md). Erfahren Sie, wie Sie einen neuen Anspruchsanbieter mit einem technischen OAuth2-Profil erstellen. Passen Sie dann die User Journey so an, dass sie eine Option zur Anmeldung mit Google bietet.
- Um Probleme mit Ihren benutzerdefinierten Richtlinien zu diagnostizieren, können Sie [Azure Active Directory B2C-Protokolle mit Application Insights sammeln](troubleshoot-with-application-insights.md). Erfahren Sie, wie Sie neue technische Profile hinzufügen und die Richtlinie für die vertrauende Seite konfigurieren.
