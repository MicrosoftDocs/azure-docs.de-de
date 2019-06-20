---
title: 'Azure AD Connect: Beheben von Problemen mit dem Quellanker bei der Installation | Microsoft-Dokumentation'
description: Dieses Thema enthält Schritte zum Beheben von Problemen mit dem Quellanker bei der Installation.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62114153"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Beheben von Problemen mit dem Quellanker bei der Installation
In diesem Artikel werden die verschiedenen Probleme im Zusammenhang mit dem Quellanker beschrieben, die bei der Installation auftreten können, und Möglichkeiten zum Beheben dieser Probleme aufgezeigt.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Ungültiger Quellanker in Azure Active Directory

### <a name="custom-installation"></a>Benutzerdefinierte Installation

Bei der benutzerdefinierten Installation liest Azure AD Connect die Quellankerrichtlinie von Azure Active Directory. Wenn die Richtlinie in Azure Active Directory vorhanden ist, wird in Azure AD Connect dieselbe Richtlinie angewandt, sofern sie nicht vom Kunden überschrieben wird. Im Assistent wird angezeigt, welches Attribut gelesen wurde. Außerdem werden Sie im Assistent gewarnt, wenn Sie versuchen, die Quellankerrichtlinie zu überschreiben.

Bei diesem Lesevorgang ist es möglich, dass die Quellankerrichtlinie in Azure Active Directory nicht erwartet wurde. In diesem Fall wird der zu verwendende Quellanker in Azure AD Connect nicht erkannt und muss manuell überschrieben werden.</br>
![Nicht erwartet](media/tshoot-connect-source-anchor/source1.png)

Um dieses Problem zu beheben, können Sie den Quellanker durch Auswahl eines spezifischen Attributs manuell überschreiben. Fahren Sie mit dieser Option nur fort, wenn Sie sicher sind, welches Attribut Sie auswählen sollen. Wenn Sie nicht sicher sind, wenden Sie sich an den [Microsoft-Support](https://support.microsoft.com/contactus/). Wenn Sie die Quellankerrichtlinie ändern, kann die Zuordnung zwischen den lokalen Benutzern und den zugehörigen Azure-Ressourcen aufgehoben werden.</br>
![Nicht erwartet](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Express-Installation
Bei der Expressinstallation liest Azure AD Connect die Quellankerrichtlinie von Azure Active Directory. Wenn die Richtlinie in Azure Active Directory vorhanden ist, wird in Azure AD Connect dieselbe Richtlinie angewandt. Es gibt keine Möglichkeit der manuellen Überschreibung.

Bei diesem Lesevorgang ist es möglich, dass die Quellankerrichtlinie in Azure Active Directory nicht erwartet wurde. In diesem Fall wird der zu verwendende Quellanker in Azure AD Connect nicht erkannt.</br>
![Nicht erwartet](media/tshoot-connect-source-anchor/source3.png)

Um dieses Problem zu beheben, müssen Sie die Installation im benutzerdefinierten Modus erneut durchführen und den Quellanker durch Auswahl eines spezifischen Attributs überschreiben. Fahren Sie mit dieser Option nur fort, wenn Sie sicher sind, welches Attribut Sie auswählen sollen. Wenn Sie nicht sicher sind, wenden Sie sich an den [Microsoft-Support](https://support.microsoft.com/contactus/). Wenn Sie die Quellankerrichtlinie ändern, kann die Zuordnung zwischen den lokalen Benutzern und den zugehörigen Azure-Ressourcen aufgehoben werden.

### <a name="invalid-source-anchor-in-sync-engine"></a>Ungültiger Quellanker im Synchronisierungsmodul
Bei der Installation ist es möglich, dass Azure AD Connect versucht, das Synchronisierungsmodul mit einem ungültigen Quellanker zu konfigurieren. Dieser Vorgang ist in den meisten Fällen auf ein Produktproblem zurückzuführen, und bei der Installation von Azure AD Connect treten Fehler auf. Wenden Sie sich an den [Microsoft-Support](https://support.microsoft.com/contactus/), wenn dieses Problem auftritt.</br>
![Nicht erwartet](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).