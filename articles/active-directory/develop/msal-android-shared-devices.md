---
title: Modus für gemeinsam genutzte Geräte für Android-Geräte
titleSuffix: Microsoft identity platform | Azure
description: Erfahren Sie, wie Sie den Modus für gemeinsam genutzte Geräte aktivieren, über den Mitarbeiter in Service und Produktion ein Android-Gerät gemeinsam nutzen können
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: marsma
ms.reviewer: hahamil
ms.custom: aaddev, identitypla | Azuretformtop40
ms.openlocfilehash: fc32b4f583aea2fa9a34ab8b235f3f99fe4def9d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562167"
---
# <a name="shared-device-mode-for-android-devices"></a>Modus für gemeinsam genutzte Geräte für Android-Geräte

>[!IMPORTANT]
> Dieses Feature [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Mitarbeiter in Service und Produktion, z. B. Mitarbeiter im Einzelhandel, Mitglieder der Flugzeugbesatzung und Außendienstmitarbeiter, verwenden für ihre Arbeit häufig ein gemeinsam genutztes mobiles Gerät. Dies wird problematisch, wenn sie Kennwörter oder PIN-Nummern gemeinsam verwenden, um auf dem gemeinsam genutzten Gerät auf Kunden- und Geschäftsdaten zuzugreifen.

Im Modus für gemeinsam genutzte Geräte können Sie ein Android-Gerät so konfigurieren, dass es problemlos von mehreren Mitarbeitern gemeinsam genutzt werden kann. Mitarbeiter können sich anmelden und schnell auf Kundeninformationen zugreifen. Nach ihrer Schicht oder nach Abschluss der Aufgabe können sie sich auf dem Gerät abmelden, und dieses ist dann sofort für den nächsten Mitarbeiter einsatzbereit.

Der Modus für gemeinsam genutzte Geräte ermöglicht auch eine auf Microsoft-Identitäten basierende Verwaltung des Geräts.

Bei der Erstellung einer App für den Modus für gemeinsam genutzte Geräte arbeiten Entwickler und Cloudgeräteadministratoren zusammen:

- Entwickler schreiben eine App für ein einzelnes Konto (Apps für mehrere Konten werden im Modus für gemeinsam genutzte Geräte nicht unterstützt), fügen `"shared_device_mode_supported": true` in der Konfiguration der App hinzu und schreiben Code zum Ausführen von Vorgängen wie z. B. das Abmelden bei dem gemeinsam genutzten Gerät.
- Geräteadministratoren bereiten das gemeinsam zu nutzende Gerät vor, indem sie die Authenticator-App installieren und das Gerät mithilfe der Authenticator-App auf den Modus für gemeinsam genutzte Geräte festlegen. Nur Benutzer mit der Rolle [Cloudgeräteadministrator](../roles/permissions-reference.md#cloud-device-administrator-permissions) können mithilfe der [Authenticator-App](../user-help/user-help-auth-app-overview.md) für ein Gerät den Modus für gemeinsam genutzte Geräte festlegen. Sie können die Mitgliedschaft der Organisationsrollen im Azure-Portal über **Azure Active Directory** > **Rollen und Administratoren** > **Cloudgeräteadministrator** konfigurieren.

 Dieser Artikel konzentriert sich hauptsächlich darauf, was Entwickler berücksichtigen sollten.

## <a name="single-vs-multiple-account-applications"></a>Anwendungen für einzelne Konten und Anwendungen für mehrere Konten

In Anwendungen, die mit dem Microsoft Authentication Library SDK (MSAL) geschrieben wurden, können ein einzelnes Konto oder mehrere Konten verwaltet werden. Weitere Informationen finden Sie unter [Modus für einzelne und mehrere Konten](single-multi-account.md). Die in Ihrer App verfügbaren Microsoft Identity Platform-Funktionen hängen davon ab, ob die Anwendung im Modus für einzelne Konten oder im Modus für mehrere Konten ausgeführt wird.

**Apps mit dem Modus für gemeinsam genutzte Geräte können nur im Modus für einzelne Konten verwendet werden**.

> [!IMPORTANT]
> Anwendungen, die nur den Modus für mehrere Konten unterstützen, können auf einem gemeinsam genutzten Gerät nicht ausgeführt werden. Wenn ein Mitarbeiter eine App lädt, die den Modus für einzelne Konten nicht unterstützt, wird die App auf dem gemeinsam genutzten Gerät nicht ausgeführt.
>
> Apps, die vor der Veröffentlichung des MSAL SDK geschrieben wurden, werden im Modus für mehrere Konten ausgeführt und müssen für die Unterstützung des Modus für einzelne Konten aktualisiert werden, damit sie auf einem Gerät im Modus für gemeinsam genutzte Geräte ausgeführt werden können.

**Unterstützung einzelner Konten sowie mehrerer Konten**

Die App kann so erstellt werden, dass sie auf persönlichen sowie auf gemeinsam genutzten Geräten ausgeführt werden kann. Wenn Ihre App derzeit mehrere Konten unterstützt und der Modus für gemeinsam genutzte Geräte unterstützt werden soll, fügen Sie die Unterstützung für den Modus für einzelne Konten hinzu.

Sie können auch festlegen, dass sich das Verhalten der App je nach dem Gerätetyp ändert, auf dem sie ausgeführt wird. Verwenden Sie `ISingleAccountPublicClientApplication.isSharedDevice()`, um festzulegen, wann der Modus für einzelne Konten ausgeführt werden soll.

Es gibt zwei verschiedene Schnittstellen, die den Typ des Geräts darstellen, auf dem die Anwendung ausgeführt wird. Wenn Sie eine Anwendungsinstanz aus der MSAL-Anwendungsfactory anfordern, wird automatisch das richtige Anwendungsobjekt bereitgestellt.

Im folgenden Objektmodell sind der Objekttyp, den Sie möglicherweise erhalten, und seine Bedeutung im Kontext gemeinsam genutzter Geräte dargestellt:

![Vererbungsmodell einer öffentlichen Clientanwendung](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Sie müssen eine Typüberprüfung und eine Umwandlung in die entsprechende Schnittstelle durchführen, wenn Sie das `PublicClientApplication`-Objekt erhalten. Mit dem folgenden Code wird eine Überprüfung auf den Modus für mehrere Konten oder den Modus für einzelne Konten durchgeführt und das Anwendungsobjekt entsprechend umgewandelt:

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

Folgende Unterschiede gelten in Abhängigkeit davon, ob die App auf einem gemeinsam genutzten oder einem persönlichen Gerät ausgeführt wird:

|  | Gerät im Modus für gemeinsam genutzte Geräte  | Persönliches Gerät |
|---------|---------|---------|
| **Konten**     | Einzelnes Konto | Mehrere Konten |
| **Anmeldung** | Global | Global |
| **Abmeldung** | Global | In jeder Anwendung kann gesteuert werden, ob die Abmeldung lokal für die Anwendung oder für die Anwendungsfamilie erfolgt. |
| **Unterstützte Kontotypen** | Nur Geschäftskonten | Persönliche Konten und Geschäftskonten werden unterstützt.  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Gründe für die alleinige Unterstützung des Modus für einzelne Konten

Wenn Sie eine Anwendung schreiben, die nur von Mitarbeitern in Service und Produktion auf einem gemeinsam genutzten Gerät verwendet wird, sollten Sie für die Anwendung nur die Unterstützung des Modus für einzelne Konten vorsehen. Dies gilt für die meisten Anwendungen, bei denen es um die Abarbeitung von Aufgaben geht, z. B. in Apps für Gesundheitsdaten oder Abrechnungsvorgänge und den meisten branchenspezifischen Apps. Die alleinige Unterstützung des Modus für einzelne Konten vereinfacht die Entwicklung, da die zusätzlichen Funktionen für Apps für mehrere Konten nicht implementiert werden müssen.

## <a name="what-happens-when-the-device-mode-changes"></a>Was geschieht, wenn sich der Gerätemodus ändert?

Wenn die Anwendung im Modus für mehrere Konten ausgeführt wird und ein Administrator für das Gerät den Modus für gemeinsam genutzte Geräte festlegt, werden alle Konten auf dem Gerät aus der Anwendung gelöscht, und die Anwendung wechselt in den Modus für einzelne Konten.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Abmeldung bei gemeinsam genutzten Geräten und der allgemeine App-Lebenszyklus

Im Hinblick auf die Abmeldung von Benutzern müssen Maßnahmen zum Schutz der Privatsphäre und der Daten der Benutzer getroffen werden. Wenn Sie z. B. eine App für Patientendaten erstellen, müssen Sie sicherstellen, dass beim Abmelden des Benutzers die zuvor angezeigten Patientendaten entfernt werden. Die Anwendung muss dafür vorbereitet werden und dies jedes Mal überprüfen, wenn sie im Vordergrund ausgeführt wird.

Bei Verwendung der MSAL in der App zum Abmelden eines Benutzers von einer App, die auf einem Gerät mit dem Modus für gemeinsam genutzte Geräte ausgeführt wird, werden das angemeldete Konto und die zwischengespeicherten Token sowohl in der App als auch auf dem Gerät entfernt.

Im folgenden Diagramm sind der gesamte App-Lebenszyklus und allgemeine Ereignisse dargestellt, die beim Ausführen der App eintreten können. Das Diagramm umfasst den Zeitpunkt des Starts einer Aktivität, die An- und Abmeldung bei einem Konto sowie die Art und Weise, wie sich Ereignisse wie z. B. das Unterbrechen, Fortsetzen und Beenden der Aktivität einfügen.

![App-Lebenszyklus eines gemeinsam genutzten Geräts](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Nächste Schritte

Im Tutorial [Verwenden des Modus für gemeinsam genutzte Geräte in Ihrer Android-Anwendung](tutorial-v2-shared-device-mode.md) wird beschrieben, wie eine App für Mitarbeiter in Service und Produktion auf einem Android-Gerät im Modus für gemeinsam genutzte Geräte ausgeführt wird.
