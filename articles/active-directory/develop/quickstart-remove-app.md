---
title: 'Vorgehensweise: Entfernen einer registrierten App aus der Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Anleitung erfahren Sie, wie Sie eine Anwendung entfernen, die bei der Microsoft Identity Platform registriert ist.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: edfed8ca361d456e622096e23079d5887f96ab30
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452979"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Entfernen einer Anwendung, die bei der Microsoft Identity Platform registriert ist

Unternehmensentwickler und SaaS-Anbieter (Software-as-a-Service), die Anwendungen bei der Microsoft Identity Platform registriert haben, müssen die Registrierung einer Anwendung unter Umständen entfernen.

In diesen Abschnitten wird Folgendes vermittelt:

* Entfernen einer Anwendung, die von Ihnen oder Ihrer Organisation erstellt wurde
* Entfernen einer Anwendung, die von einer anderen Organisation erstellt wurde

## <a name="prerequisites"></a>Voraussetzungen

* Eine [Anwendung, die in Ihrem Azure AD-Mandanten registriert ist](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Entfernen einer Anwendung, die von Ihnen oder Ihrer Organisation erstellt wurde

Anwendungen, die von Ihnen oder Ihrer Organisation registriert wurden, werden in Ihrem Mandanten durch ein Anwendungsobjekt und ein Dienstprinzipalobjekt dargestellt. Weitere Informationen finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte](./app-objects-and-service-principals.md).

Zum Löschen einer Anwendung müssen Sie als Besitzer der Anwendung angegeben sein oder über Administratorberechtigungen verfügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Sie mit Ihrem Konto auf mehrere Mandanten zugreifen können, klicken Sie rechts oben auf Ihr Konto, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** und dann **App-Registrierungen** aus. Wählen Sie die Anwendung aus, die Sie konfigurieren möchten. Nachdem Sie die App ausgewählt haben, wird die Seite **Übersicht** angezeigt.
1. Wählen Sie auf der Seite **Übersicht** die Option **Löschen**.
1. Wählen Sie **Ja**, um zu bestätigen, dass Sie die App löschen möchten.

## <a name="remove-an-application-authored-by-another-organization"></a>Entfernen einer Anwendung, die von einer anderen Organisation erstellt wurde

Wenn Sie **App-Registrierungen** im Kontext eines Mandanten anzeigen, stammt ein Teil der Anwendungen, die auf der Registerkarte **Alle Apps** angegeben sind, von einem anderen Mandanten. Sie wurden während des Zustimmungsprozesses unter Ihrem Mandanten registriert. Genauer gesagt: Sie werden unter Ihrem Mandanten nur von einem Dienstprinzipalobjekt ohne entsprechendes Anwendungsobjekt repräsentiert. Weitere Informationen zu den Unterschieden zwischen Anwendungs- und Dienstprinzipalobjekten finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure AD](./app-objects-and-service-principals.md).

Der Administrator des Unternehmens muss seinen Dienstprinzipal entfernen, um für eine Anwendung den Zugriff auf Ihr Verzeichnis zu entfernen (nachdem die Zustimmung erteilt wurde). Der Administrator muss über globalen Administratorzugriff verfügen und kann die Anwendung über das Azure-Portal entfernen oder die [Azure AD-PowerShell-Cmdlets](/previous-versions/azure/jj151815(v=azure.100)) zum Entfernen des Zugriffs verwenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über [Anwendungs- und Dienstprinzipalobjekte](app-objects-and-service-principals.md) in der Microsoft Identity Platform
