---
title: Wie und warum Anwendungen Azure Active Directory hinzugefügt werden
description: Was bedeutet es für eine Anwendung, Azure AD hinzugefügt zu werden, und wie gelangen Anwendungen in das Verzeichnis?
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: elisol, lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cc9d0a951ac6f7ed18ad6558ae9edb2d1f9c8f4
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544647"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Wie und warum werden Anwendungen zu Azure AD hinzugefügt?

Es gibt zwei Darstellungen von Anwendungen in Azure AD: 
* [Anwendungsobjekte](app-objects-and-service-principals.md#application-object) können, obwohl es [Ausnahmen](#notes-and-exceptions) gibt, als die Definition einer Anwendung betrachtet werden.
* [Dienstprinzipale](app-objects-and-service-principals.md#service-principal-object) können als eine Instanz einer Anwendung betrachtet werden. Dienstprinzipale verweisen in der Regel auf ein Anwendungsobjekt, und ein Anwendungsobjekt kann verzeichnisübergreifend von mehreren Dienstprinzipalen referenziert werden.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Was sind Anwendungsobjekte und woher stammen sie?
[Anwendungsobjekte](app-objects-and-service-principals.md#application-object) können im Azure-Portal über die Oberfläche [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade) verwaltet werden. Anwendungsobjekte stellen für Azure AD eine Beschreibung der Anwendung bereit und können als die Definition der Anwendung betrachtet werden. Sie teilen dem Dienst basierend auf den Einstellungen der Anwendung mit, wie Token für die Anwendung ausgestellt werden müssen. Das Anwendungsobjekt ist nur in seinem Stammverzeichnis vorhanden. Dies gilt auch dann, wenn es sich um eine mehrinstanzenfähige Anwendung handelt, die Dienstprinzipale in anderen Verzeichnissen unterstützt. Das Anwendungsobjekt kann Folgendes enthalten (sowie zusätzliche Informationen, die hier nicht erwähnt werden):
* Name, Logo und Herausgeber
* Antwort-URLs
* Geheimnisse (symmetrische und/oder asymmetrische Schlüssel für die Authentifizierung der Anwendung)
* API-Abhängigkeiten (OAuth)
* Veröffentlichte APIs/Ressourcen/Bereiche (OAuth)
* App-Rollen (RBAC)
* Metadaten und Konfiguration für einmaliges Anmelden (Single Sign-On, SSO)
* Metadaten und Konfiguration für die Benutzerbereitstellung
* Metadaten und Konfiguration für den Proxy

Anwendungsobjekte können auf verschiedene Arten erstellt werden, darunter:
* Anwendungsregistrierungen im Azure-Portal
* Erstellen einer neuen Anwendung mit Visual Studio und Konfigurieren der Anwendung zur Verwendung der Azure AD-Authentifizierung
* Hinzufügen einer Anwendung aus dem App-Katalog durch einen Administrator (dadurch wird ebenfalls ein Dienstprinzipal erstellt)
* Erstellen einer neuen Anwendung mithilfe der Microsoft Graph-API, der Azure AD Graph-API oder mit PowerShell
* Verschiedene weitere Methoden wie entwicklerspezifische Methoden in Azure oder API-Explorer-Methoden in einem Developer Center

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Was sind Dienstprinzipale und woher stammen sie?
[Dienstprinzipale](app-objects-and-service-principals.md#service-principal-object) können im Azure-Portal über die Oberfläche [Unternehmensanwendungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) verwaltet werden. Dienstprinzipale steuern eine Anwendung, die eine Verbindung mit Azure AD herstellt, und können als die Instanz der Anwendung in Ihrem Verzeichnis betrachtet werden. Für jede Anwendung können maximal ein Anwendungsobjekt (das in einem „Basisverzeichnis“ registriert wird) und ein oder mehrere Dienstprinzipalobjekte vorhanden sein, die Instanzen der Anwendung in jedem Verzeichnis darstellen, in dem diese agiert. 

Der Dienstprinzipal kann Folgendes enthalten:

* Einen Rückverweis auf ein Anwendungsobjekt über die Anwendungs-ID-Eigenschaft
* Datensätze mit lokalen Anwendungsrollenzuweisungen für Benutzer und Gruppen
* Datensätze mit lokalen Benutzer- und Administratorberechtigungen, die der Anwendung gewährt wurden
  * Beispiel: die Berechtigung für die Anwendung, auf die E-Mail eines bestimmten Benutzers zuzugreifen
* Datensätze mit lokalen Richtlinien, einschließlich der Richtlinie für bedingten Zugriff
* Datensätze mit alternativen lokalen Einstellungen für eine Anwendung
  * Transformationsregeln für Ansprüche
  * Attributzuordnungen (Benutzerbereitstellung)
  * Verzeichnisspezifische App-Rollen (wenn die Anwendung benutzerdefinierte Rollen unterstützt)
  * Den verzeichnisspezifischen Namen oder das verzeichnisspezifische Logo

Ähnlich wie Anwendungsobjekte können Dienstprinzipale auf verschiedene Arten erstellt werden, darunter:

* Anmeldung von Benutzern bei einer in Azure AD integrierten Drittanbieteranwendung
  * Bei der Anmeldung werden die Benutzer aufgefordert, der Anwendung neben weiteren Berechtigungen auch die Berechtigung für den Zugriff auf deren Profil zu gewähren. Der erste Benutzer, der hierzu seine Zustimmung erteilt, bewirkt, dass dem Verzeichnis ein Dienstprinzipal hinzugefügt wird, der die Anwendung darstellt.
* Anmeldung von Benutzern bei Microsoft-Onlinediensten wie [Office 365](https://products.office.com/)
  * Wenn Sie Office 365 abonnieren oder ein Testabonnement starten, werden im Verzeichnis ein oder mehrere Dienstprinzipale erstellt, die die verschiedenen Dienste darstellen, mit denen die Funktionen von Office 365 bereitgestellt werden.
  * Manche Office 365-Dienste wie beispielsweise SharePoint erstellen fortlaufend neue Dienstprinzipale, um eine sichere Kommunikation zwischen den Komponenten (einschließlich Workflows) zu ermöglichen.
* Hinzufügen einer Anwendung aus dem App-Katalog durch einen Administrator (dadurch wird ebenfalls ein zugrunde liegendes Anwendungsobjekt erstellt)
* Hinzufügen einer Anwendung zur Verwendung des [Azure AD-Anwendungsproxys](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Verbinden einer Anwendung für einmaliges Anmelden über SAML oder einmaliges Anmelden (SSO) per Kennwort
* Programmgesteuert über die Azure AD Graph-API oder über PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Wie werden Anwendungsobjekte und Dienstprinzipale miteinander verknüpft?
Eine Anwendung besitzt ein Anwendungsobjekt in ihrem Basisverzeichnis, auf das von einem oder mehreren Dienstprinzipalen in jedem der Verzeichnisse verwiesen wird, in denen die Anwendung arbeitet (einschließlich des Basisverzeichnisses der Anwendung).
![Diagramm, das zeigt, wie Anwendungsobjekte und Dienstprinzipale miteinander und mit Azure AD-Instanzen interagieren.][apps_service_principals_directory]

Im obigen Diagramm verwaltet Microsoft intern zwei Verzeichnisse (dargestellt auf der linken Seite) für die Veröffentlichung von Anwendungen:

* Eines für Microsoft-Anwendungen (Verzeichnis für Microsoft-Dienste)
* Eines für bereits integrierte Anwendungen von Drittanbietern (Verzeichnis für den App-Katalog)

Herausgeber oder Anbieter, die ihre Anwendungen in Azure AD integrieren, müssen über ein Veröffentlichungsverzeichnis verfügen (dargestellt auf der rechten Seite als SaaS-Verzeichnis).

Zu Anwendungen, die Sie selbst hinzufügen (dargestellt als **Ihre App** im Diagramm), zählen:

* Selbst entwickelte Anwendungen (in Azure AD integriert)
* Anwendungen, mit denen Sie über SSO verbunden sind
* Anwendungen, die Sie mithilfe des Azure AD-Anwendungsproxys veröffentlicht haben

### <a name="notes-and-exceptions"></a>Hinweise und Ausnahmen
* Nicht alle Dienstprinzipale verweisen zurück auf ein Anwendungsobjekt. In den Anfängen von Azure AD wurden den Anwendungen eingeschränktere Dienste bereitgestellt, und der Dienstprinzipal reichte zur Einrichtung einer Anwendungsidentität aus. Der ursprüngliche Dienstprinzipal sah dem Active Directory-Dienstkonto von Windows Server noch wesentlich ähnlicher. Aus diesem Grund ist es nach wie vor möglich, Dienstprinzipale mit Azure AD PowerShell auf verschiedene Arten zu erstellen, ohne zuvor ein Anwendungsobjekt zu erstellen. Die Azure AD Graph-API setzt hingegen für die Erstellung eines Dienstprinzipals ein Anwendungsobjekt voraus.
* Nicht alle oben beschriebenen Informationen werden derzeit programmgesteuert bereitgestellt. Die folgenden Informationen sind nur in der Benutzeroberfläche verfügbar:
  * Transformationsregeln für Ansprüche
  * Attributzuordnungen (Benutzerbereitstellung)
* Ausführlichere Informationen zu Dienstprinzipalen und Anwendungsobjekten finden Sie in der Referenzdokumentation zur Azure AD Graph-REST-API:
  * [Anwendung](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Dienstprinzipal](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Weshalb werden Anwendung in Azure AD integriert?
Anwendungen werden Azure AD hinzugefügt, damit sie die von Azure AD bereitgestellten Dienste nutzen können, beispielsweise:

* Anwendungsauthentifizierung und -autorisierung
* Benutzerauthentifizierung und -autorisierung
* Einmaliges Anmelden (SSO) durch Verbund oder Kennwort
* Benutzerbereitstellung und -synchronisierung
* Rollenbasierte Zugriffssteuerung – Definition von Anwendungsrollen für rollenbasierte Autorisierungsprüfungen in einer Anwendung mithilfe des Verzeichnisses
* OAuth-Autorisierungsdienste – werden von Office 365 und anderen Microsoft-Anwendungen zum Autorisieren des Zugriffs auf APIs und Ressourcen verwendet
* Anwendungsveröffentlichung und Proxy – Veröffentlichung einer Anwendung aus einem privaten Netzwerk im Internet

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Wer hat die Berechtigung zum Hinzufügen von Anwendungen zu meiner Azure AD-Instanz?
Obwohl einige Aufgaben nur von globalen Administratoren ausgeführt werden können (beispielsweise das Hinzufügen von Anwendungen aus dem App-Katalog und Konfigurieren einer Anwendung zur Verwendung des Anwendungsproxys), besitzen standardmäßig alle Benutzer in Ihrem Verzeichnis Berechtigungen zum Registrieren von Anwendungsobjekten, die sie selbst entwickeln. Zudem können alle Benutzer standardmäßig selbst entscheiden, welche Anwendungen sie freigeben bzw. welchen Anwendungen sie mittels Zustimmung Zugriff auf ihre Unternehmensdaten gewähren. Wenn eine Person der erste Benutzer in Ihrem Verzeichnis ist, der sich bei einer Anwendung anmeldet und seine Zustimmung erteilt, wird in Ihrem Mandanten ein Dienstprinzipal erstellt. Andernfalls werden die Informationen zur Zustimmungserteilung im vorhandenen Dienstprinzipal gespeichert.

Benutzern die Registrierung von Anwendungen und die Zustimmung zu deren Verwendung zu ermöglichen, mag anfangs zwar bedenklich klingen, Sie sollten aber Folgendes berücksichtigen:


* Viele Jahre lang konnten Anwendungen Windows Server Active Directory für die Benutzerauthentifizierung nutzen, ohne dass die Anwendung im Verzeichnis registriert sein musste oder auf andere Weise im Verzeichnis aufgezeichnet wurde. Heute haben Unternehmen mehr Einblick in die Anwendungen, die ihr Verzeichnis nutzen, und können genau feststellen, wofür sie es verwenden.
* Durch das Delegieren dieser Aufgaben an Benutzer entfällt die Notwendigkeit eines vom Administrator gesteuerten Anwendungsregistrierungs- und Veröffentlichungsprozesses. Bei Active Directory-Verbunddienste (AD FS) musste ein Administrator häufig als vertrauende Seite eine Anwendung im Namen der Entwickler hinzufügen. Dies können Entwickler nun im eigenen Namen erledigen.
* Die Benutzeranmeldung bei Anwendungen über das Unternehmenskonto ist im Rahmen ihrer Geschäftsabläufe ein großer Vorteil. Sobald ein Benutzer aber das Unternehmen verlässt, verliert er automatisch in der bislang verwendeten Anwendung den Zugriff auf sein Konto.
* Auch eine Aufzeichnung darüber, welche Daten für welche Anwendungen freigegeben wurden, ist von Vorteil. Daten lassen sich heute leichter austauschen und übertragen als jemals zuvor, und eine klare Aufzeichnung darüber, wer welche Daten für welche Anwendungen freigegeben hat, ist äußerst wichtig.
* API-Besitzer, die Azure AD für OAuth nutzen, legen genau fest, welche Berechtigungen die Benutzer Anwendungen erteilen können und bei welchen Berechtigungen die Zustimmung eines Administrators erforderlich ist. Nur Administratoren können ihre Zustimmung für größere Bereiche und wichtigere Berechtigungen erteilen, während die Zustimmung des Benutzers auf die Daten und Funktionen des Benutzers begrenzt ist.
* Wenn ein Benutzer eine Anwendung hinzufügt oder ihr den Zugriff auf seine Daten erlaubt, kann das Ereignis überwacht werden. Sie können daher in den Überwachungsberichten des Azure-Portals überprüfen, wie eine Anwendung zum Verzeichnis hinzugefügt wurde.

Falls Sie dennoch verhindern möchten, dass Benutzer in Ihrem Verzeichnis ohne die Genehmigung des Administrators Anwendungen registrieren und sich bei Anwendungen anmelden, stehen Ihnen dazu zwei Funktionen zur Verfügung:

* So verhindern Sie, dass Benutzer selbst ihre Zustimmung für Anwendungen erteilen:
  1. Navigieren Sie im Azure-Portal unter „Unternehmensanwendungen“ zum Abschnitt [Benutzereinstellungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/).
  2. Ändern Sie die Einstellung von **Benutzer können Apps den Zugriff auf Unternehmensdaten in ihrem Namen gestatten** in **Nein**. 
     
     > [!NOTE]
     > Wenn Sie diese Option deaktivieren, ist bei jeder neuen Anwendung, die ein Benutzer benötigt, die Zustimmung eines Administrators erforderlich.    
* So verhindern Sie, dass Benutzer eigene Anwendungen registrieren:
  1. Navigieren Sie im Azure-Portal unter „Azure Active Directory“ zum Abschnitt [Benutzereinstellungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings).
  2. Ändern Sie die Einstellung von **Benutzer können Anwendungen registrieren** in **Nein**.

> [!NOTE]
> Microsoft selbst verwendet die Standardkonfiguration, in der Benutzer Anwendungen registrieren und selbst ihre Zustimmung für Anwendungen erteilen können.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

