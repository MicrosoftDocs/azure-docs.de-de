---
title: Hochverfügbarkeit für Azure MFA Server – Azure Active Directory
description: Stellen Sie in Konfigurationen für Hochverfügbarkeit mehrere Instanzen von Azure Multi-Factor Authentication-Server bereit.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b23cf7abeb0aaf8bc3939296a307edb07ce5fcf0
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742475"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Konfigurieren von Azure Multi-Factor Authentication-Server für Hochverfügbarkeit

Um hohe Verfügbarkeit bei Ihrer für Azure MFA Server-Bereitstellung zu erreichen, müssen Sie mehrere MFA-Server bereitstellen. Dieser Abschnitt enthält Informationen zu einem Entwurf mit Lastenausgleich, mit dem Sie Ihre Hochverfügbarkeitsziele für die Azure MFS Server-Bereitstellung erreichen können.

> [!IMPORTANT]
> Seit dem 1. Juli 2019 bietet Microsoft für neue Bereitstellungen keine MFA-Server mehr an. Neue Kunden, die bei Benutzeranmeldeereignissen eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) verlangen möchten, sollten cloudbasierte Azure AD Multi-Factor Authentication verwenden.
>
> Informationen zu den ersten Schritten mit der cloudbasierten MFA finden Sie im [Tutorial: Schützen von Benutzeranmeldeereignissen mit Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Bestandskunden, die ihren MFA-Server vor dem 1. Juli 2019 aktiviert haben, können weiterhin die neuesten Versionen und zukünftige Updates herunterladen sowie Anmeldedaten zur Aktivierung generieren.

## <a name="mfa-server-overview"></a>Übersicht über MFA Server

Die Dienstarchitektur von Azure MFA Server umfasst mehrere Komponenten, die im folgenden Diagramm dargestellt sind:

 ![Komponenten der Architektur von MFA Server](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Ein MFA-Server ist ein Windows-Server, auf dem die Azure Multi-Factor Authentication-Software installiert ist. Die MFA Server-Instanz muss vom MFA-Dienst in Azure aktiviert werden. Sie können lokal auch mehrere MFA Server-Instanzen installieren.

Der erste installierte MFA-Server wird nach der Aktivierung durch den Azure MFA-Dienst standardmäßig der MFA-Masterserver. Der MFA-Masterserver verfügt über eine beschreibbare Kopie der Datenbank „PhoneFactor.pfdata“. Alle danach installierten Instanzen von MFA Server werden als untergeordnete Server bezeichnet. Auf den untergeordneten MFA-Servern befindet sich eine replizierte schreibgeschützte Kopie der Datenbank „PhoneFactor.pfdata“. MFA-Server replizieren ihre Informationen mithilfe von Remoteprozeduraufrufen (RPC). Alle MFA-Server müssen gemeinsam einer Domäne angehören oder eigenständig sein, um Informationen zu replizieren.

Sowohl der MFA-Master als auch untergeordnete MFA-Server kommunizieren mit dem MFA-Dienst, wenn eine zweistufige Authentifizierung erforderlich ist. Wenn z.B. ein Benutzer versucht, Zugriff auf eine Anwendung zu erhalten, die eine zweistufige Authentifizierung erfordert, wird der Benutzer zunächst von einem Identitätsanbieter wie Active Directory (AD) authentifiziert.

Nach der erfolgreichen Authentifizierung in AD kommuniziert der MFA-Server mit dem MFA-Dienst. Der MFA-Server wartet auf eine Benachrichtigung vom MFA-Dienst, um den Benutzerzugriff auf die Anwendung zuzulassen oder zu verweigern.

Wenn der MFA-Masterserver offline geschaltet wird, kann die Authentifizierungen weiterhin verarbeitet werden, aber Vorgänge, die Änderungen an der MFA-Datenbank erfordern, können nicht verarbeitet werden. (Beispiele: Hinzufügen von Benutzern, Self-Service-PIN-Änderungen, Ändern von Benutzerinformationen oder Zugreifen auf das Benutzerportal)

## <a name="deployment"></a>Bereitstellung

Berücksichtigen Sie die folgenden wichtigen Punkte für den Lastenausgleich von Azure MFA Server und den zugehörigen Komponenten.

* **Verwenden des RADIUS-Standards für Hochverfügbarkeit:** Wenn Sie Server mit Azure MFA Server als RADIUS-Server verwenden, können Sie potenziell einen MFA-Server als primäres RADIUS-Authentifizierungsziel und andere Azure MFA Server-Instanzen als sekundäre Authentifizierungsziele konfigurieren. Allerdings ist diese Methode für Hochverfügbarkeit möglicherweise nicht sehr effizient, da Sie, wenn auf dem primären Authentifizierungsziel ein Authentifizierungsfehler auftritt, ein Timeout abwarten müssen, bevor Sie eine Authentifizierung mit den sekundären Authentifizierungszielen durchführen können. Effizienter ist ein Lastenausgleich für den RADIUS-Datenverkehr zwischen dem RADIUS-Client und den RADIUS-Servern (in diesem Fall die Azure MFA-Server, die als RADIUS-Server fungieren). In diesem Fall können Sie die RADIUS-Clients mit einer einzelnen URL konfigurieren, auf die sie verweisen können.
* **Manuelles Höherstufen untergeordneter MFA-Server:** Wenn der Azure MFA-Masterserver offline geschaltet wird, verarbeiten die sekundären Azure MFA Server-Instanzen weiterhin MFA-Anforderungen. Bis allerdings der MFA-Masterserver verfügbar ist, können Administratoren keine Benutzer hinzufügen oder MFA-Einstellungen ändern, und Benutzer können keine Änderungen über das Benutzerportal vornehmen. Das Höherstufen eines untergeordneten MFA-Servers als Masterserver ist immer ein manueller Vorgang.
* **Trennbarkeit von Komponenten:** Azure MFA Server besteht aus mehreren Komponenten, die auf derselben Windows Server-Instanz oder auf verschiedenen Instanzen installiert werden können. Zu diesen Komponenten gehören das Benutzerportal, der Webdienst für Mobile Apps und der AD FS-Adapter (Agent). Diese Trennbarkeit ermöglicht die Verwendung des Webanwendungsproxys, um das Benutzerportal und den Webserver für Mobile App aus dem Umkreisnetzwerk zu veröffentlichen. Eine solche Konfiguration erhöht die allgemeine Sicherheit Ihres Entwurfs. Dies wird im folgenden Diagramm dargestellt. Das MFA-Benutzerportal und der Webserver für Mobile Apps können auch in Hochverfügbarkeitskonfigurationen mit Lastenausgleich bereitgestellt werden.

   ![MFA-Server mit einem Umkreisnetzwerk](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Die Verwendung von Einmalkennwörtern per SMS (auch als Einmal-SMS bezeichnet) erfordert die Verwendung von persistenten Sitzungen, wenn für den Datenverkehr ein Lastenausgleich verwendet wird:** Einmal-SMS ist eine Authentifizierungsoption, bei der der MFA-Server den Benutzern eine Textnachricht (SMS) mit einem Einmalkennwort sendet. Der Benutzer gibt das Einmalkennwort in einem Eingabeaufforderungsfenster ein, um die MFA-Anforderung zu erfüllen. Wenn Sie einen Lastenausgleich für Azure MFA Server vornehmen, muss der gleiche Server, der die anfängliche Authentifizierunganforderung verarbeitet, auch der Server sein, der die Nachricht mit dem Einmalkennwort von den Benutzern erhält. Wenn ein anderer MFA-Server die Antwort auf das Einmalkennwort empfängt, führt die Authentifizierungsaufforderung zu einem Fehler. Weitere Informationen finden Sie unter [One Time Password over SMS Added to Azure MFA Server](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server) (Einmalkennwort per SMS jetzt in Azure MFA Server).
* **Bereitstellungen von Benutzerportal und Webdienst für Mobile Apps mit Lastenausgleich erfordern persistente Sitzungen:** Wenn Sie einen Lastenausgleich für das MFA-Benutzerportal und den Webdienst für Mobile Apps vornehmen, muss jede Sitzung auf demselben Server bleiben.

## <a name="high-availability-deployment"></a>Bereitstellung mit hoher Verfügbarkeit

Das folgende Diagramm zeigt eine vollständige Hochverfügbarkeitsimplementierung von Azure MFA und den zugehörigen Komponenten mit Lastenausgleich zusammen mit AD FS als Referenz.

 ![Hochverfügbarkeitsimplementierung von Azure MFA Server](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Beachten Sie die folgenden Elemente für den entsprechend nummerierten Bereich im Diagramm oben.

1. Für die beiden Azure MFA Server-Instanzen (MFA1 und MFA2) wird ein Lastenausgleich (mfaapp.contoso.com) vorgenommen. Außerdem ist für die Replikation der Datenbank „PhoneFactor.pfdata“ die Verwendung eines statischen Ports konfiguriert (4443). Das Webdienst-SDK ist auf jedem der MFA-Server installiert, um die Kommunikation über TCP-Port 443 mit den AD FS-Servern zu ermöglichen. Die MFA-Server werden in einer zustandslosen Konfiguration mit Lastenausgleich bereitgestellt. Wenn Sie allerdings Einmalkennwörter per SMS verwenden möchten, müssen Sie einen zustandsbehafteten Lastenausgleich verwenden.
   ![Azure MFA Server – Hochverfügbarkeits-Anwendungsserver](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Da für RPC dynamische Ports verwendet werden, sollten Firewalls nicht bis in den Bereich der dynamischen Ports geöffnet werden, den RPC potenziell verwenden kann. Wenn Sie über eine Firewall **zwischen** Ihren MFA-Anwendungsservern verfügen, sollten Sie die MFA-Server für die Kommunikation des Replikationsdatenverkehrs zwischen untergeordneten Servern und Masterserver über einen statischen Port konfigurieren und diesen Port in Ihrer Firewall öffnen. Sie können den statischen Port erzwingen, indem Sie einen DWORD-Registrierungswert unter ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` erstellen, der den Namen ```Pfsvc_ncan_ip_tcp_port``` hat, und den Wert auf einen verfügbaren statischen Port festlegen. Verbindungen werden immer von den untergeordneten MFA-Servern an den Masterserver initiiert. Der statische Port ist also nur auf dem Masterserver erforderlich. Da Sie aber jederzeit einen untergeordneten Server zum Masterserver hochstufen können, sollten Sie den statischen Port auf allen MFA-Servern festlegen.

2. Für die beiden Server für Benutzerportal und MFA Mobile App (MFA-UP-MAS1 und MFA-UP-MAS2) wird in einer **zustandsbehafteten** Konfiguration (mfa.contoso.com) ein Lastenausgleich vorgenommen. Beachten Sie, dass für einen Lastenausgleich von MFA-Benutzerportal und Mobile App-Dienst persistente Sitzungen eine Voraussetzung sind.
   ![Azure MFA-Server – Benutzerportal und Mobile App-Dienst mit Hochverfügbarkeit](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. Für die AD FS-Serverfarm wird ein Lastenausgleich vorgenommen, und sie wird über AD FS-Proxys mit Lastenausgleich im Umkreisnetzwerk im Internet veröffentlicht. Jeder AD FS-Server verwendet den AD FS-Agent für die Kommunikation mit Azure MFA-Servern über eine einzige Lastenausgleichs-URL (mfaapp.contoso.com) über TCP-Port 443.

## <a name="next-steps"></a>Nächste Schritte

* [Installieren und Konfigurieren von Azure MFA Server](howto-mfaserver-deploy.md)
