---
title: Microsoft Identity Platform – Authentifizierungsprotokolle
description: Übersicht über die von Microsoft Identity Platform unterstützten Authentifizierungsprotokolle
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 41ea41b4d7c181dad9246653a68c329387ac5381
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "80884680"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Microsoft Identity Platform – Authentifizierungsprotokolle

Microsoft Identity Platform unterstützt mehrere der am häufigsten verwendeten Authentifizierungs- und Autorisierungsprotokolle. Die Themen in diesem Abschnitt beschreiben die unterstützten Protokolle und deren Implementierung in Microsoft Identity Platform. Die Themen umfassen eine Beschreibung der unterstützten Anspruchstypen, eine Einführung in die Verwendung von Verbundmetadaten, eine ausführliche OAuth 2.0.- und SAML 2.0-Protokoll-Referenzdokumentation und einen Abschnitt zur Problembehandlung.

## <a name="authentication-protocols-articles-and-reference"></a>Artikel und Referenz zu Authentifizierungsprotokollen

* [Wichtige Informationen zum Rollover von Signaturschlüsseln in Microsoft Identity Platform](active-directory-signing-key-rollover.md): Erfahren Sie mehr über die Rolloverkadenz von Signaturschlüsseln in Microsoft Identity Platform und über die Änderungen, die Sie für das automatische Update des Schlüssels vornehmen können. Außerdem wird die Aktualisierung der häufigsten Anwendungsszenarien erläutert.
* [Unterstützte Token- und Anspruchstypen](id-tokens.md): Erfahren Sie mehr über die Ansprüche in den Token, die Microsoft Identity Platform ausgibt.
* [OAuth 2.0 in Microsoft Identity Platform](v2-oauth2-auth-code-flow.md): Erfahren Sie mehr über die Implementierung von OAuth 2.0 in Microsoft Identity Platform.
* [OpenID Connect 1.0](v2-protocols-oidc.md) – Erfahren Sie mehr über die Verwendung des Authorisierungsprotokolls OAuth 2.0 für die Authentifizierung.
* [Dienst-zu-Dienst-Aufrufe mit Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md): Erfahren Sie mehr über den Ablauf der Gewährung von OAuth 2.0-Clientanmeldeinformationen für Dienst-zu-Dienst-Aufrufe.
* [Dienst-zu-Dienst-Aufrufe mit dem Im-Auftrag-von-Fluss](v2-oauth2-on-behalf-of-flow.md): Erfahren Sie mehr über das Verwenden das Im-Auftrag-von-Flusses von OAuth 2.0 für Dienst-zu-Dienst-Aufrufe.
* [SAML-Protokollreferenz](active-directory-saml-protocol-reference.md): Lernen Sie die SAML-Profile zum einmaligen Anmelden und einmaligem Abmelden von Microsoft Identity Platform kennen.

## <a name="see-also"></a>Weitere Informationen

* [Übersicht über die Microsoft Identity Platform](v2-overview.md)
* [Active Directory-Codebeispiele](sample-v2-code.md)
