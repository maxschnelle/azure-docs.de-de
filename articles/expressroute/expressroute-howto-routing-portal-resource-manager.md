---
title: 'Tutorial: Konfigurieren des Peerings für eine ExpressRoute-Leitung: Azure-Portal'
description: In diesem Tutorial erfahren Sie, wie Sie über das Azure-Portal privates ExpressRoute-Peering sowie ExpressRoute-Microsoft-Peering erstellen und bereitstellen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: cad098ed2dedc7abba57394ef1e26b9b7c87cd9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855470"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>Tutorial: Erstellen und Ändern des Peerings für eine ExpressRoute-Leitung mithilfe des Azure-Portals

In diesem Tutorial erfahren Sie, wie Sie über das Azure-Portal die Routingkonfiguration für eine ExpressRoute-Leitung in Azure Resource Manager erstellen und verwalten. Sie können außerdem den Status prüfen, ein Update durchführen oder Peerings für eine ExpressRoute-Verbindung löschen bzw. deren Bereitstellung aufheben. Wenn Sie eine andere Methode für die Arbeit mit Ihrer Verbindung verwenden möchten, wählen Sie einen Artikel aus der folgenden Liste aus:

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure-Befehlszeilenschnittstelle](howto-routing-cli.md)
> * [Öffentliches Peering](about-public-peering.md)
> * [Video – Privates Peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – Microsoft-Peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisch)](expressroute-howto-routing-classic.md)
> 

Sie können privates Peering und Microsoft-Peering für eine ExpressRoute-Leitung konfigurieren (öffentliches Azure-Peering ist für neue Leitungen veraltet). Sie können Peerings in beliebiger Reihenfolge konfigurieren. Sie müssen jedoch sicherstellen, dass Sie die Konfiguration jedes Peerings einzeln nacheinander durchführen. Weitere Informationen zu Routingdomänen und Peerings finden Sie unter [ExpressRoute-Routingdomänen](expressroute-circuit-peerings.md). Weitere Informationen zum öffentlichen Peering finden Sie unter [Öffentliches ExpressRoute-Peering](about-public-peering.md).

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> - Konfigurieren, Aktualisieren und Löschen des Microsoft-Peerings für eine Leitung
> - Konfigurieren, Aktualisieren und Löschen des privaten Azure-Peerings für eine Leitung

## <a name="prerequisites"></a>Voraussetzungen

* Lesen Sie unbedingt die folgenden Seiten, bevor Sie mit der Konfiguration beginnen:
    * [Voraussetzungen](expressroute-prerequisites.md) 
    * [Routinganforderungen](expressroute-routing.md)
    * [Workflows](expressroute-workflows.md)
* Sie benötigen eine aktive ExpressRoute-Verbindung. Führen Sie die Anweisungen unter [Erstellen und Ändern des Peerings für eine ExpressRoute-Verbindung](expressroute-howto-circuit-portal-resource-manager.md) aus, und lassen Sie die Leitung vom Konnektivitätsanbieter aktivieren, bevor Sie fortfahren. Zum Konfigurieren von Peerings muss die ExpressRoute-Leitung den Zustand „Bereitgestellt“ und „Aktiviert“ aufweisen. 
* Wenn Sie einen freigegebenen Schlüssel/MD5-Hash nutzen möchten, verwenden Sie den Schlüssel unbedingt auf beiden Seiten des Tunnels. Es gilt ein Grenzwert von maximal 25 alphanumerische Zeichen. Sonderzeichen werden nicht unterstützt. 

Diese Anweisungen gelten nur für Verbindungen, die über Service Provider erstellt wurden, von denen Layer 2-Konnektivitätsdienste angeboten werden. Wenn Sie einen Dienstanbieter nutzen, der verwaltete Layer 3-Dienste anbietet (meist ein IP-VPN wie MPLS), übernimmt Ihr Konnektivitätsanbieter die Konfiguration und Verwaltung des Routings für Sie. 

> [!IMPORTANT]
> Derzeit kündigen wir keine von Service Providern konfigurierten Peerings über das Service Management Portal an. Wir arbeiten daran, dass diese Funktion schnell bereitgestellt wird. Informieren Sie sich bei Ihrem Dienstanbieter, bevor Sie BGP-Peerings konfigurieren.
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-Peering

Dieser Abschnitt unterstützt Sie beim Erstellen, Abrufen, Aktualisieren und Löschen der Microsoft-Peeringkonfiguration für eine ExpressRoute-Verbindung.

> [!IMPORTANT]
> Beim Microsoft-Peering von ExpressRoute-Verbindungen, die vor dem 1. August 2017 konfiguriert wurden, werden alle Dienstpräfixe über das Microsoft-Peering angekündigt, auch wenn keine Routenfilter definiert sind. Beim Microsoft-Peering von ExpressRoute-Verbindungen, die am oder nach dem 1. August 2017 konfiguriert wurden, werden Präfixe erst angekündigt, wenn der Verbindung ein Routenfilter hinzugefügt wurde. Weitere Informationen finden Sie unter [Konfigurieren eines Routenfilters für das Microsoft-Peering](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>So erstellen Sie Microsoft-Peering

1. Konfigurieren Sie die ExpressRoute-Verbindung. Überprüfen Sie vor dem Fortfahren den **Anbieterstatus**, um sicherzustellen, dass die Verbindung vom Konnektivitätsanbieter vollständig bereitgestellt wird.

   Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste im Angebot hat, können Sie bei ihm die Aktivierung des Microsoft-Peerings anfordern. In diesem Fall müssen Sie die Anweisungen in den nächsten Abschnitten nicht befolgen. Falls Ihr Konnektivitätsanbieter das Routing jedoch nicht für Sie verwaltet, müssen Sie nach dem Erstellen der Leitung diese Schritte ausführen.

   **Verbindung – Anbieterstatus: Nicht bereitgestellt**

   [![Screenshot: Übersichtsseite für die ExpressRoute-Demoleitung mit einem roten Feld, auf der der auf „Nicht bereitgestellt“ festgelegte Anbieterstatus hervorgehoben ist](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)


   **Verbindung – Anbieterstatus: Bereitgestellt**

   [![Screenshot: Übersichtsseite für die ExpressRoute-Demoleitung mit einem roten Feld, auf der der auf „Bereitgestellt“ festgelegte Anbieterstatus hervorgehoben ist](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)

2. Konfigurieren Sie das Microsoft-Peering für die Verbindung. Stellen Sie vorab sicher, dass Ihnen die folgenden Informationen vorliegen:

   * Ein /30-Subnetz für die primäre Verknüpfung. Der Adressblock muss ein gültiges öffentliches IPv4-Präfix sein, das sich in Ihrem Besitz befindet und über eine RIR/IRR-Registrierung verfügt. Über dieses Subnetz weisen Sie die erste verwendbare IP-Adresse für Ihren Router zu, da die zweite verwendbare IP-Adresse von Microsoft für den eigenen Router genutzt wird.
   * Ein /30-Subnetz für die sekundäre Verknüpfung. Der Adressblock muss ein gültiges öffentliches IPv4-Präfix sein, das sich in Ihrem Besitz befindet und über eine RIR/IRR-Registrierung verfügt. Über dieses Subnetz weisen Sie die erste verwendbare IP-Adresse für Ihren Router zu, da die zweite verwendbare IP-Adresse von Microsoft für den eigenen Router genutzt wird.
   * Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet. Sie müssen sowohl für primäre als auch für sekundäre Verknüpfungen die gleiche VLAN-ID verwenden.
   * AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden.
   * Angekündigte Präfixe: Sie geben eine Liste mit allen Präfixen an, die Sie über die BGP-Sitzung ankündigen möchten. Nur öffentliche IP-Adresspräfixe werden akzeptiert. Wenn Sie planen, einen Satz mit Präfixen zu senden, können Sie eine durch Komma getrennte Liste senden. Diese Präfixe müssen über eine RIR/IRR-Registrierung für Sie verfügen.
   * **Optional:** Kunden-ASN: Wenn Sie Präfixe ankündigen, die nicht für die Peering-AS-Nummer registriert sind, können Sie die AS-Nummer angeben, unter der sie registriert sind.
   * Routing-Registrierungsname: Sie können den RIR/IRR-Wert angeben, unter dem die AS-Nummer und die Präfixe registriert sind.
   * **Optional** – Einen MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden.
3. Sie können das Peering auswählen, das Sie konfigurieren möchten, wie im folgenden Beispiel zu sehen. Wählen Sie die Zeile für das Microsoft-Peering aus.

   [![Auswählen der Zeile für das Microsoft-Peering](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Auswählen der Zeile für das Microsoft-Peering")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Konfigurieren Sie das Microsoft-Peering. **Speichern** Sie die Konfiguration, nachdem Sie alle Parameter angegeben haben. Die folgende Abbildung enthält eine Beispielkonfiguration:

   ![Konfigurieren des Microsoft-Peerings](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Microsoft überprüft, ob Ihnen die angegebenen „Angekündigten öffentlichen Präfixe“ und „Peer-ASN" (oder „Kunden-ASN“) in der Internet Routing Registry zugewiesen werden. Wenn Sie die öffentlichen Präfixe aus einer anderen Entität erhalten und die Zuweisung nicht mit der Routing Registry aufgezeichnet wird, wird die automatische Überprüfung nicht durchgeführt, sodass eine manuelle Überprüfung erforderlich ist. Wenn bei der automatischen Überprüfung ein Fehler auftritt, wird die Meldung „Überprüfung erforderlich“ angezeigt. 
>
> Wenn die Meldung „Überprüfung erforderlich“ angezeigt wird, sammeln Sie die Dokumente mit den öffentlichen Präfixen, die Ihrer Organisation von der Entität zugewiesen wurden, die in der Routingregistrierung als Besitzer der Präfixe aufgeführt ist. Öffnen Sie ein Supportticket, um diese Dokumente zur manuellen Überprüfung zu senden. 
>

   Wenn Ihre Verbindung wie oben dargestellt in den Zustand „Überprüfung erforderlich“ versetzt wird, müssen Sie ein Supportticket öffnen, um gegenüber unserem Supportteam nachzuweisen, dass sich die Präfixe in Ihrem Besitz befinden. Sie können ein Supportticket direkt über das Portal erstellen, wie im folgenden Beispiel zu sehen:

   ![Überprüfung erforderlich – Supportticket](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Nachdem die Konfiguration akzeptiert wurde, sieht die Anzeige in etwa wie in der folgenden Abbildung aus:

   ![Peeringstatus: Konfiguriert](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Peeringstatus: Konfiguriert")

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>So zeigen Sie die Details zum Microsoft-Peering an:

Sie können die Eigenschaften des Microsoft-Peerings anzeigen, indem Sie die Zeile für das Peering auswählen.

[![Anzeigen der Eigenschaften des Microsoft-Peerings](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Eigenschaften anzeigen")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>So aktualisieren Sie die Konfiguration des Microsoft-Peerings:

Sie können die Zeile für das Peering auswählen, das Sie ändern möchten, und anschließend die Peeringeigenschaften ändern und die Änderungen speichern.

![Auswählen der Zeile für das Peering](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Privates Azure-Peering:

Dieser Abschnitt unterstützt Sie beim Erstellen, Abrufen, Aktualisieren und Löschen der privaten Azure-Peeringkonfiguration für eine ExpressRoute-Verbindung.

### <a name="to-create-azure-private-peering"></a>So erstellen Sie ein privates Azure-Peering

1. Konfigurieren Sie die ExpressRoute-Verbindung. Stellen Sie vor dem Fortfahren sicher, dass die Verbindung vom Konnektivitätsanbieter vollständig bereitgestellt wird. 

   Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste im Angebot hat, können Sie bei ihm die Aktivierung des privaten Azure-Peerings anfordern. In diesem Fall müssen Sie die Anweisungen in den nächsten Abschnitten nicht befolgen. Falls Ihr Konnektivitätsanbieter das Routing jedoch nicht für Sie verwaltet, müssen Sie nach dem Erstellen der Leitung die nächsten Schritte ausführen.

   **Verbindung – Anbieterstatus: Nicht bereitgestellt**

   [![Screenshot: Übersichtsseite für die ExpressRoute Demo-Leitung mit einem roten Feld, das den Anbieterstatus hervorhebt, der auf „Nicht bereitgestellt“ festgelegt ist](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Verbindung – Anbieterstatus: Bereitgestellt**

   [![Screenshot: Übersichtsseite für die ExpressRoute Demo-Leitung mit einem roten Feld, das den Anbieterstatus hervorhebt, der auf „Bereitgestellt“ festgelegt ist](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Konfigurieren Sie das private Azure-Peering für die Verbindung. Bevor Sie mit den nächsten Schritten fortfahren, stellen Sie sicher, dass Sie über Folgendes verfügen:

   * Ein /30-Subnetz für die primäre Verknüpfung. Dieses Subnetz darf nicht Teil eines Adressraums sein, der für virtuelle Netzwerke reserviert ist. Über dieses Subnetz weisen Sie die erste verwendbare IP-Adresse für Ihren Router zu, da die zweite verwendbare IP-Adresse von Microsoft für den eigenen Router genutzt wird.
   * Ein /30-Subnetz für die sekundäre Verknüpfung. Dieses Subnetz darf nicht Teil eines Adressraums sein, der für virtuelle Netzwerke reserviert ist. Über dieses Subnetz weisen Sie die erste verwendbare IP-Adresse für Ihren Router zu, da die zweite verwendbare IP-Adresse von Microsoft für den eigenen Router genutzt wird.
   * Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet. Sie müssen sowohl für primäre als auch für sekundäre Verknüpfungen die gleiche VLAN-ID verwenden.
   * AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden. Sie können eine private AS-Nummer für dieses Peering mit Ausnahme der Nummern von 65515 bis 65520 einschließlich verwenden.
   * Wenn Sie das private Peering konfigurieren, müssen Sie Azure die Routen von Ihrem lokalen Edge-Router über BGP ankündigen.
   * **Optional** – Einen MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden.
3. Wählen Sie die Zeile für das private Azure-Peering aus, wie im folgenden Beispiel gezeigt:

   [![Auswählen der Zeile für das private Peering](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Auswählen der Zeile für das private Peering")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Konfigurieren Sie das private Azure-Peering. **Speichern** Sie die Konfiguration, nachdem Sie alle Parameter angegeben haben.

   ![Konfigurieren des privaten Peerings](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Nachdem die Konfiguration akzeptiert wurde, sieht die Anzeige in etwa wie im folgenden Beispiel aus:

   ![Speichern des privaten Peerings](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>So zeigen Sie Details zum privaten Azure-Peering an:

Sie können die Eigenschaften des privaten Azure-Peerings anzeigen, indem Sie das Peering auswählen.

[![Anzeigen der Eigenschaften für das private Peering](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Anzeigen der Eigenschaften für das private Peering")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>So aktualisieren Sie die Konfiguration für privates Azure-Peering:

Sie können die Zeile für das Peering auswählen und die Peeringeigenschaften ändern. Speichern Sie Ihre Änderungen nach dem Aktualisieren.

![Aktualisieren des privaten Peerings](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>So löschen Sie das Microsoft-Peering:

Die Peeringkonfiguration kann durch Klicken auf das Löschsymbol entfernt werden, wie in der folgenden Abbildung gezeigt:

![Löschen des Peerings](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>So löschen Sie ein privates Azure-Peering:

Die Peeringkonfiguration kann durch Auswählen des Löschsymbols entfernt werden, wie in der folgenden Abbildung gezeigt:

> [!WARNING]
> Sie müssen sicherstellen, dass alle virtuellen Netzwerke und ExpressRoute Global Reach-Verbindungen entfernt wurden, bevor Sie dieses Beispiel ausführen. 
> 
> 

![Löschen des privaten Peerings](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie das private Azure-Peering konfiguriert haben, können Sie ein ExpressRoute-Gateway erstellen, um ein virtuelles Netzwerk mit der Leitung zu verknüpfen. 

> [!div class="nextstepaction"]
> [Konfigurieren eines Gateways für ein virtuelles Netzwerk für ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
