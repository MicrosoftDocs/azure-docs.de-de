---
title: Behandeln von Problemen bei der Installation von Azure AD Connect | Microsoft-Dokumentation
description: Dieses Thema enthält Schritte zum Behandeln von Problemen bei der Installation von Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e077127681f8bd7b650ab22f2d036efd7f9733ee
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60454790"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Problembehandlung: Azure AD Connect-Installationsprobleme

## <a name="recommended-steps"></a>**Empfohlene Schritte**
Überprüfen Sie, welcher [Azure AD Connect-Installationstyp](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) für Sie geeignet ist. Werden die Kriterien für die Express-Installation erfüllt, wird dringend empfohlen, die Express-Installation zu verwenden. Bei der Express-Installation müssen Sie nur geringfügige Optionen angeben, um die Installation abzuschließen. Dadurch reduziert sich die Fehlerwahrscheinlichkeit. 

Wenn die Kriterien für eine Express-Installation jedoch nicht erfüllt werden und Sie die benutzerdefinierte Installation ausführen müssen, finden Sie hier einige bewährte Methoden, denen Sie folgen können, um häufig auftretende Probleme zu vermeiden. Der Einfachheit halber sind hier nur selektive Optionen aufgeführt:

* Stellen Sie sicher, dass Sie für den Computer, auf dem Sie AAD Connect installieren, Administratorrechte haben. Melden Sie sich am Computer mit den gleichen Administratoranmeldeinformationen an.

* Übernehmen Sie auf der folgenden Seite für alle Optionen die Standardeinstellungen. Eine Ausnahme bildet dabei die Option „Vorhandenen SQL Server-Computer verwenden“, wenn Sie vorhandene SQL Server-Computer verwenden möchten. Hier finden Sie [weitere Details](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) zur Verwendung der Optionen bei einer benutzerdefinierten Installation. 

    ![Vorhandenen SQL Server-Computer verwenden](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Wählen Sie auf der folgenden Seite die Option „Neues AD-Konto erstellen“ aus, um mögliche Probleme mit den Berechtigungen eines vorhandenen Kontos zu vermeiden.

    ![AD-Gesamtstrukturkonto](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Allgemeine Probleme**

* [Konnektivitätsprobleme mit lokaler Active Directory-Instanz](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools)

* [Konnektivitätsprobleme mit Azure Active Directory-Onlineinstanz](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity)

* [Berechtigungsprobleme mit lokaler Active Directory-Instanz](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account)

## <a name="recommended-documents"></a>**Empfohlene Dokumente**
* [Voraussetzungen für Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Auswählen des Installationstyps für Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Erste Schritte mit Azure AD Connect mit Expresseinstellungen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Benutzerdefinierte Installation von Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: Aktualisieren von einer früheren Version auf die aktuelle Version](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version).
* [Azure AD Connect: Was ist ein Stagingserver?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Was ist das PowerShell-Modul „ADConnectivityTool“?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Nächste Schritte
- [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md)
- [Was ist eine Hybrididentität?](whatis-hybrid-identity.md)





