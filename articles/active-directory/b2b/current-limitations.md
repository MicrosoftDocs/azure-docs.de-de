---
title: Einschränkungen der B2B-Zusammenarbeit – Azure Active Directory | Microsoft-Dokumentation
description: Aktuelle Einschränkungen der Azure Active Directory B2B-Zusammenarbeit
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1466614dfeb8fa4d3d095bae070d01c6503c5bb
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357237"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Einschränkungen der Azure AD B2B-Zusammenarbeit
Azure Active Directory B2B-Zusammenarbeit (Azure AD) unterliegt derzeit den in diesem Artikel beschriebenen Einschränkungen.

## <a name="possible-double-multi-factor-authentication"></a>Mögliche doppelte Multi-Factor Authentication
Mit Azure AD B2B können Sie die mehrstufige Authentifizierung bei der Ressourcenorganisation (die einladende Organisation) erzwingen. Die Gründe für diesen Ansatz werden in [Multi-Factor Authentication für Benutzer der Azure Active Directory B2B-Zusammenarbeit](conditional-access.md) erläutert. Wenn ein Partner bereits die mehrstufige Authentifizierung eingerichtet hat und sie erzwingt, müssen die Benutzer die Authentifizierung möglicherweise einmal in ihrer eigenen Organisation ausführen und dann erneut in Ihrer.

## <a name="instant-on"></a>Instant-On
Im Workflow der B2B-Zusammenarbeit werden Benutzer zum Verzeichnis hinzugefügt und während der Einlösung der Einladung, der App-Zuweisung usw. dynamisch aktualisiert. Die Aktualisierungs- und Schreibvorgänge erfolgen im Allgemeinen in einer Verzeichnisinstanz und müssen in allen Instanzen repliziert werden. Die Replikation wird durchgeführt, nachdem alle Instanzen aktualisiert wurden. Wenn das Objekt in einer Instanz geschrieben oder aktualisiert wurde und der Aufruf zum Abrufen dieses Objekts in einer anderen Instanz erfolgt ist, können bei der Replikation Wartezeiten auftreten. Wenn dies der Fall ist, führen Sie eine Aktualisierung aus, oder wiederholen Sie den Vorgang. Wenn Sie mit unserer API eine App schreiben, ist die Wiederholung des Vorgangs mit einem Backoff-Intervall ein sinnvolles Verfahren, um dieses Problem zu verringern.

## <a name="azure-ad-directories"></a>Azure AD-Verzeichnisse
Azure AD B2B unterliegt den Einschränkungen des Azure AD-Dienstverzeichnisses. Ausführliche Informationen zur Anzahl von Verzeichnissen, die ein Benutzer erstellen kann, und der Anzahl von Verzeichnissen, zu denen ein Benutzer oder Gastbenutzer gehören kann, finden Sie unter [Dienst- und andere Einschränkungen für Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Nationale Clouds
[Nationale Clouds](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) sind physisch isolierte Azure-Instanzen. Die B2B-Zusammenarbeit wird über die Grenzen nationaler Clouds hinweg nicht unterstützt. Beispiel: Wenn sich Ihr Azure-Mandant in der öffentlichen globalen Cloud befindet, können Sie keinen Benutzer einladen, dessen Konto sich in einer nationalen Cloud befindet. Damit Sie mit dem Benutzer zusammenarbeiten können, fragen Sie ihn nach einer anderen E-Mail-Adresse, oder erstellen Sie ein Mitgliedsbenutzerkonto in Ihrem Verzeichnis.

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Zusammenarbeit:

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [Delegieren von Einladungen zur B2B-Zusammenarbeit](delegate-invitations.md)

