---
title: Platzhalteranwendungen im Azure Active Directory-Anwendungsproxy
description: Erfahren Sie, wie Platzhalteranwendungen im Azure Active Directory-Anwendungsproxy verwendet werden.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b2563b238bae310d662220d2c244e863249c9c4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997510"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Platzhalteranwendungen im Azure Active Directory-Anwendungsproxy

In Azure Active Directory (Azure AD) kann die Konfiguration einer großen Anzahl von lokalen Anwendungen schnell unüberschaubar werden. Die Folge sind unnötige Risiken aufgrund von Konfigurationsfehlern, wenn viele der Anwendungen die gleichen Einstellungen erfordern. Mit [Azure AD-Anwendungsproxy](application-proxy.md) können Sie dieses Problem mithilfe einer Platzhalteranwendung umgehen, mit der Sie viele Anwendungen gleichzeitig veröffentlichen und verwalten können. Diese Lösung bietet Ihnen folgende Möglichkeiten:

- Vereinfachen des Verwaltungsaufwands
- Verringern der Anzahl potenzieller Konfigurationsfehler
- Ermöglichen eines sicheren Benutzerzugriffs auf weitere Ressourcen

In diesem Artikel erhalten Sie alle erforderlichen Informationen über die Konfiguration für die Veröffentlichung von Platzhalteranwendungen in Ihrer Umgebung.

## <a name="create-a-wildcard-application"></a>Erstellen einer Platzhalteranwendung

Sie können eine Platzhalteranwendung (*) erstellen, wenn Sie über eine Gruppe von Anwendungen mit der gleichen Konfiguration verfügen. Potenzielle Kandidaten für eine Platzhalteranwendung sind Anwendungen, die die folgenden Einstellungen gemeinsam haben:

- Die Gruppe von Benutzern, die darauf Zugriff haben
- Die SSO-Methode
- Das Zugriffsprotokoll (HTTP, HTTPS)

Sie können Anwendungen mit Platzhaltern veröffentlichen, wenn sowohl interne als auch externe URLs im folgenden Format vorliegen:

> http(s)://*.\<domain\>

Beispiel: `http(s)://*.adventure-works.com`.

Obwohl die internen und externen URLs unterschiedliche Domänen verwenden können, hat es sich bewährt, wenn diese identisch sind. Beim Veröffentlichen der Anwendung wird ein Fehler angezeigt, wenn eine der URLs keinen Platzhalter aufweist.

Die Erstellung einer Platzhalteranwendung basiert auf demselben [Ablauf für die Anwendungsveröffentlichung](application-proxy-add-on-premises-application.md), der auch für alle anderen Anwendungen gilt. Der einzige Unterschied besteht darin, dass Sie einen Platzhalter in den URLs und ggf. in der SSO-Konfiguration verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass Sie diese Anforderungen erfüllen.

### <a name="custom-domains"></a>Benutzerdefinierte Domänen

Während [benutzerdefinierte Domänen](application-proxy-configure-custom-domain.md) für alle anderen Anwendungen optional sind, stellen Sie eine Voraussetzung für Platzhalteranwendungen dar. Zum Erstellen benutzerdefinierter Domänen müssen Sie die folgenden Schritte ausführen:

1. Erstellen Sie eine überprüfte Domäne in Azure.
1. Laden Sie ein TLS/SSL-Zertifikat im PFX-Format in Ihren Anwendungsproxy hoch.

Erwägen Sie die Verwendung eines Platzhalterzertifikats für die Anwendung, die Sie erstellen möchten. 

Aus Sicherheitsgründen ist dies eine zwingende Anforderung, und es werden keine Platzhalter für Anwendungen unterstützt, die keine benutzerdefinierte Domäne für die externe URL verwenden.

### <a name="dns-updates"></a>DNS-Updates

Bei der Verwendung benutzerdefinierter Domänen müssen Sie einen DNS-Eintrag mit einem CNAME-Eintrag für die externe URL (z.B. `*.adventure-works.com`) erstellen, der auf die externe URL des Anwendungsproxy-Endpunkts verweist. Bei Platzhalteranwendungen muss der CNAME-Eintrag auf die entsprechenden externen URLs verweisen:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Um sicherzustellen, dass Sie den CNAME-Eintrag ordnungsgemäß konfiguriert haben, können Sie [nslookup](/windows-server/administration/windows-commands/nslookup) für einen der Zielendpunkte, z.B. `expenses.adventure-works.com`, ausführen.  Die Antwort sollte den bereits erwähnten Alias (`<yourAADTenantId>.tenant.runtime.msappproxy.net`) enthalten.

## <a name="considerations"></a>Überlegungen

Die folgenden Überlegungen sollten Sie im Hinblick auf Platzhalteranwendungen berücksichtigen.

### <a name="accepted-formats"></a>Zulässige Formate

Bei Platzhalteranwendungen muss die **interne URL** das Format `http(s)://*.<domain>` aufweisen.

![Verwenden Sie das Format „http(s)://*.\<Domäne>“ für interne URLs.](./media/application-proxy-wildcard/22.png)

Beim Konfigurieren einer **externen URL** müssen Sie folgendes Format verwenden: `https://*.<custom domain>`

![Verwenden Sie das Format „https://*.\<benutzerdefinierte Domäne>“ für externe URLs.](./media/application-proxy-wildcard/21.png)

Andere Positionen des Platzhalters, mehrere Platzhalter oder andere RegEx-Zeichenfolgen werden nicht unterstützt und verursachen Fehler.

### <a name="excluding-applications-from-the-wildcard"></a>Ausschließen von Anwendungen aus dem Platzhalter

Sie können eine Anwendung aus der Platzhalteranwendung wie folgt ausschließen:

- Veröffentlichen der Ausnahmeanwendung als normale Anwendung
- Aktivieren das Platzhalters nur für bestimmte Anwendungen über die DNS-Einstellungen

Das Veröffentlichen einer Anwendung als normale Anwendung ist die bevorzugte Methode, um eine Anwendung aus einem Platzhalter auszuschließen. Veröffentlichen Sie die ausgeschlossenen Anwendungen vor den Platzhalteranwendungen, um sicherzustellen, dass Ihre Ausnahmen von Anfang an erzwungen werden. Die spezifischste Anwendung hat immer Vorrang – eine als `budgets.finance.adventure-works.com` veröffentlichte Anwendung hat Vorrang vor der Anwendung `*.finance.adventure-works.com`, die wiederum Vorrang vor der Anwendung `*.adventure-works.com` hat.

Außerdem können Sie den Platzhalter über Ihre DNS-Verwaltung so einschränken, dass dieser nur für bestimmte Anwendungen funktioniert. Als bewährte Methode sollten Sie einen CNAME-Eintrag erstellen, der einen Platzhalter enthält und dem Format der von Ihnen festgelegten externen URL entspricht. Stattdessen können Sie auch bestimmte Anwendungs-URLs auf die Platzhalter verweisen lassen. Anstelle von `*.adventure-works.com` lassen Sie beispielsweise `hr.adventure-works.com`, `expenses.adventure-works.com` und `travel.adventure-works.com individually` auf `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` verweisen.

Wenn Sie diese Option verwenden, benötigen Sie ggf. einen weiteren CNAME-Eintrag für den Wert `AppId.domain`, z.B. `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, der auch auf das gleiche Ziel verweist. Sie finden die **App-ID** auf der Seite mit den Anwendungseigenschaften der Platzhalteranwendung:

![Anwendungs-ID auf der Eigenschaftenseite der App suchen](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Festlegen der Homepage-URL für das MyApps-Panel

Die Platzhalteranwendung wird im [MyApps-Panel](https://myapps.microsoft.com) mit nur einer Kachel dargestellt. Diese Kachel ist standardmäßig ausgeblendet. So blenden Sie die Kachel ein und weisen Benutzern eine bestimmte Zielseite zu:

1. Befolgen Sie die Richtlinien zum [Festlegen einer Homepage-URL](application-proxy-configure-custom-home-page.md).
1. Setzen Sie **Anwendung anzeigen** auf der Eigenschaftenseite für die Anwendung auf **true**.

### <a name="kerberos-constrained-delegation"></a>Eingeschränkte Kerberos-Delegierung

Für Anwendungen mit [eingeschränkter Kerberos-Delegierung (KCD) wie der SSO-Methode](application-proxy-configure-single-sign-on-with-kcd.md) wird für den Dienstprinzipalnamen (SPN), der für die SSO-Methode aufgeführt ist, möglicherweise auch ein Platzhalter benötigt. Der SPN kann z.B. wie folgt aussehen: `HTTP/*.adventure-works.com`. Zudem müssen die einzelnen SPNs auf Ihren Back-End-Servern konfiguriert sein (z.B. `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com`).

## <a name="scenario-1-general-wildcard-application"></a>Szenario 1: Allgemeine Platzhalteranwendung

In diesem Szenario gibt es drei verschiedene Anwendungen, die Sie veröffentlichen möchten:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Für alle drei Anwendungen gilt Folgendes:

- Sie werden von allen Benutzern verwendet.
- Sie verwenden die *integrierte Windows-Authentifizierung*.
- Sie haben die gleichen Eigenschaften.

Die zum Veröffentlichen der Platzhalteranwendung erforderlichen Schritte finden Sie unter [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](application-proxy-add-on-premises-application.md). Annahmen für dieses Szenario:

- Ein Mandant mit der folgenden ID: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Eine überprüfte Domäne namens `adventure-works.com` wurde konfiguriert.
- Ein **CNAME**-Eintrag, der `*.adventure-works.com` auf `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` verweist, wurde erstellt.

Erstellen Sie entsprechend der [dokumentierten Schritte](application-proxy-add-on-premises-application.md) eine neue Anwendungsproxyanwendung in Ihrem Mandanten. In diesem Beispiel ist der Platzhalter in den folgenden Feldern enthalten:

- Interne URL:

    ![Beispiel: Platzhalter in interner URL](./media/application-proxy-wildcard/42.png)

- Externe URL:

    ![Beispiel: Platzhalter in externer URL](./media/application-proxy-wildcard/43.png)

- Interner Anwendungs-SPN:

    ![Beispiel: Platzhalter in der SPN-Konfiguration](./media/application-proxy-wildcard/44.png)

Durch die Veröffentlichung der Platzhalteranwendung können Sie jetzt auf Ihre drei Anwendungen zugreifen, indem Sie zu den gewohnten URLs navigieren (z.B. `travel.adventure-works.com`).

Bei der Konfiguration wird die folgende Struktur implementiert:

![Zeigt die Struktur, die von der Beispielkonfiguration implementiert wird](./media/application-proxy-wildcard/05.png)

| Color | BESCHREIBUNG |
| ---   | ---         |
| Blau  | Anwendungen, die im Azure-Portal explizit veröffentlicht und angezeigt werden |
| Grau  | Anwendungen, auf die über die übergeordnete Anwendung zugegriffen werden kann. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Szenario 2: Allgemeine Platzhalteranwendung mit Ausnahme

In diesem Szenario steht Ihnen neben den drei allgemeinen Anwendungen eine weitere Anwendung, `finance.adventure-works.com`, zur Verfügung, die nur für die Finanzabteilung zugänglich sein soll. Mit der aktuellen Anwendungsstruktur ist der Zugriff auf die Finanzanwendung über die Platzhalteranwendung und durch alle Mitarbeiter möglich. Um dies zu ändern, schließen Sie Ihre Anwendung aus Ihrem Platzhalter aus, indem Sie „finance“ als separate Anwendung mit einschränkenderen Berechtigungen konfigurieren.

Stellen Sie sicher, dass ein CNAME-Eintrag vorhanden ist, der `finance.adventure-works.com` auf den anwendungsspezifischen Endpunkt verweist, welcher auf der Seite „Anwendungsproxy“ für die Anwendung festgelegt wurde. In diesem Szenario verweist `finance.adventure-works.com` auf `https://finance-awcycles.msappproxy.net/`.

Entsprechend der [dokumentierten Schritte](application-proxy-add-on-premises-application.md) sind für dieses Szenario die folgenden Einstellungen erforderlich:

- Legen Sie im Feld **Interne URL** die Finanzabteilung **finance** anstelle eines Platzhalters fest.

    ![Beispiel: Im Feld „Interne URL“ „finance“ anstelle eines Platzhalters festlegen](./media/application-proxy-wildcard/52.png)

- Legen Sie im Feld **Externe URL** die Finanzabteilung **finance** anstelle eines Platzhalters fest.

    ![Beispiel: Im Feld „Externe URL“ „finance“ anstelle eines Platzhalters festlegen](./media/application-proxy-wildcard/53.png)

- Legen Sie für den internen Anwendungs-SPN die Finanzabteilung **finance** anstelle eines Platzhalters fest.

    ![Beispiel: Für die SPN-Konfiguration „finance“ anstelle eines Platzhalters festlegen](./media/application-proxy-wildcard/54.png)

Bei dieser Konfiguration wird das folgende Szenario implementiert:

![Zeigt die vom Beispielszenario implementierte Konfiguration](./media/application-proxy-wildcard/09.png)

Da `finance.adventure-works.com` eine spezifischere URL als `*.adventure-works.com` ist, hat diese Vorrang. Benutzern, die zu `finance.adventure-works.com` navigieren, werden die in der Finanzressourcenanwendung festgelegten Optionen angezeigt. In diesem Fall können ausschließlich Mitarbeiter der Finanzabteilung auf `finance.adventure-works.com` zugreifen.

Wenn Sie für die Finanzabteilung mehrere Anwendungen veröffentlicht haben und `finance.adventure-works.com` als überprüfte Domäne vorhanden ist, könnten Sie eine weitere Platzhalteranwendung `*.finance.adventure-works.com` veröffentlichen. Da diese spezifischer als die allgemeine `*.adventure-works.com` ist, hat sie Vorrang, wenn ein Benutzer auf eine Anwendung in der „finance“-Domäne zugreift.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu **benutzerdefinierten Domänen** finden Sie unter [Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md).
- Weitere Informationen zum **Veröffentlichen von Anwendungen** finden Sie unter [Veröffentlichen von Anwendungen mit dem Azure AD-Anwendungsproxy](application-proxy-add-on-premises-application.md).