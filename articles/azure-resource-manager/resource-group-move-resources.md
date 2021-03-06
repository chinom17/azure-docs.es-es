---
title: "Traslado de recursos de Azure a una nueva suscripción o grupo de recursos | Microsoft Docs"
description: "Use Azure Resource Manager para trasladar recursos a un nuevo grupo de recursos o a una nueva suscripción."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 3f8b5e8b8af4be85e830bde8eb0587c632a9dd1f
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción

En este artículo se explica cómo trasladar recursos a una nueva suscripción o un grupo de recursos en la misma suscripción. Puede usar el portal, PowerShell, la CLI de Azure o la API de REST para trasladar recursos. Las operaciones de movimiento de este artículo están disponibles sin ayuda del soporte técnico de Azure.

Al mover los recursos, el grupo de origen y el grupo de destino se bloquean durante la operación. Las operaciones de escritura y eliminación están bloqueadas en los grupos de recursos hasta que se completa el movimiento. Este bloqueo significa que no puede agregar, actualizar ni eliminar recursos de los grupos de recursos, pero no que los recursos queden bloqueados. Por ejemplo, si mueve un servidor SQL Server y su base de datos a un nuevo grupo de recursos, una aplicación que utiliza la base de datos no experimenta ningún tiempo de inactividad. Todavía puede leer y escribir en la base de datos.

No puede cambiar la ubicación del recurso. Si se mueve un recurso, solo se mueve a un nuevo grupo de recursos. El nuevo grupo de recursos puede tener una ubicación diferente, pero no cambia la ubicación del recurso.

> [!NOTE]
> En este artículo se describe cómo mover los recursos de una oferta de cuenta de Azure. Si realmente desea cambiar la oferta de cuenta de Azure (por ejemplo, actualizar de pago por uso a prepago) sin dejar de trabajar con los recursos existentes, consulte [Cambio a otra oferta de Azure](../billing/billing-how-to-switch-azure-offer.md).
>
>

## <a name="checklist-before-moving-resources"></a>Lista de comprobación antes de mover recursos

Hay algunos pasos importantes que deben realizarse antes de mover un recurso. Puede evitar errores mediante la comprobación de estas condiciones.

1. Las suscripciones de origen y destino deben existir en el mismo [inquilino de Azure Active Directory](../active-directory/active-directory-howto-tenant.md). Para comprobar que ambas suscripciones tienen el mismo identificador de inquilino, utilice Azure PowerShell o la CLI de Azure.

  Para Azure PowerShell, use:

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzureRmSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  Para la CLI de Azure, utilice:

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  Si los identificadores de inquilino de las suscripciones de origen y destino no son los mismos, use los siguientes métodos para conciliarlos: 

  * [Transferencia de la propiedad de una suscripción de Azure a otra cuenta](../billing/billing-subscription-transfer.md)
  * [Asociación o adición de una suscripción de Azure a Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md)

2. El servicio debe permitir la capacidad de traslado de recursos. En este artículo se enumeran los servicios que permiten mover recursos y los servicios que no permiten el traslado de recursos.
3. La suscripción de destino correspondiente al proveedor de recursos del recurso que se traslada debe estar registrada. Si no es así, recibirá un error en el que se indicará que la **suscripción no está registrada para un tipo de recurso**. Podría encontrar este problema al mover un recurso a una nueva suscripción que nunca se ha utilizado el suscripción con ese tipo de recurso.

  En PowerShell, use los siguientes comandos para obtener el estado de registro:

  ```powershell
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  Para registrar un proveedor de recursos, use:

  ```powershell
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  En la CLI de Azure, use los siguientes comandos para obtener el estado de registro:

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  Para registrar un proveedor de recursos, use:

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

## <a name="when-to-call-support"></a>Al llamar al soporte técnico

Puede trasladar la mayoría de los recursos a través de las operaciones de autoservicio que se muestran en este artículo. Utilice las operaciones de autoservicio para:

* Trasladar recursos de Resource Manager.
* Trasladar recursos clásicos conforme a las [limitaciones de implementación clásica](#classic-deployment-limitations).

Póngase en contacto con [soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) cuando necesite:

* Traslade los recursos a una nueva cuenta de Azure (e inquilino de Azure Active Directory) y si necesita ayuda consulte las instrucciones de la sección anterior.
* Trasladar recursos clásicos, pero que tienen problemas con las limitaciones.

## <a name="services-that-enable-move"></a>Servicios que permiten el traslado

Los servicios que permiten el traslado a un nuevo grupo de recursos y a una nueva suscripción son:

* API Management
* App Service apps (Web Apps) - consulte las [limitaciones de App Service](#app-service-limitations)
* Application Insights
* Automation
* Azure Cosmos DB
* Batch
* Mapas de Bing
* CDN
* Cloud Services (consulte las [limitaciones de la implementación clásica](#classic-deployment-limitations)
* Cognitive Services
* Content Moderator
* Data Catalog
* Data Factory
* Data Lake Analytics
* Almacén de Data Lake
* DNS
* Event Hubs
* Clústeres de HDInsight: consulte [Limitaciones de HDInsight](#hdinsight-limitations).
* IoT Hubs
* Key Vault
* Equilibradores de carga
* Logic Apps
* Machine Learning
* Media Services
* Mobile Engagement
* Notification Hubs
* Operational Insights
* Operations Management
* Power BI
* Caché en Redis
* Scheduler
* Search
* Servidor de administración
* Azure Service Bus
* Service Fabric
* Storage
* Storage (clásico); consulte las [limitaciones de la implementación clásica](#classic-deployment-limitations)
* Stream Analytics: los trabajos de Stream Analytics no se pueden mover si se encuentran en estado de ejecución.
* Servidor de SQL Database: la base de datos y el servidor deben residir en el mismo grupo de recursos. Cuando se mueve un servidor SQL Server, se mueven también todas sus bases de datos.
* Traffic Manager
* Virtual Machines: no se pueden mover las máquinas virtuales con Managed Disks. Vea [Limitaciones de Virtual Machines](#virtual-machines-limitations).
* Virtual Machines (clásico); consulte las [limitaciones de la implementación clásica](#classic-deployment-limitations)
* Conjuntos de escalado de máquinas virtuales; vea [Limitaciones de Virtual Machines](#virtual-machines-limitations).
* Redes virtuales; vea [Limitaciones de las redes virtuales](#virtual-networks-limitations).
* VPN Gateway

## <a name="services-that-do-not-enable-move"></a>Servicios que no permiten el traslado

Los servicios que actualmente no permiten trasladar un recurso son:

* Servicios de dominio de AD
* Servicio de mantenimiento híbrido de AD
* Application Gateway
* BizTalk Services
* Container Service
* ExpressRoute
* DevTest Labs: el traslado al nuevo grupo de recursos en la misma suscripción está habilitado pero no el traslado de suscripción cruzado.
* Dynamics LCS
* Aplicaciones administradas
* Managed Disks; vea [Limitaciones de Virtual Machines](#virtual-machines-limitations).
* Almacén de Recovery Services: no mueva tampoco los recursos de Compute, Network y Storage asociados con el almacén de Recovery Services, vea [Limitaciones de Recovery Services](#recovery-services-limitations).
* Seguridad
* Administrador de dispositivos de StorSimple
* Virtual Networks (clásico); consulte las [limitaciones de la implementación clásica](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>Limitaciones de Virtual Machines

Managed Disks no admite el traslado. Esta restricción indica que tampoco se pueden mover varios recursos relacionados. No se pueden mover los siguientes recursos:

* Discos administrados
* Virtual Machines con Managed Disks
* Imágenes creadas a partir de Managed Disks
* Instantáneas creadas a partir de Managed Disks
* Conjuntos de disponibilidad con Virtual Machines con Managed Disks

Las instancias de Virtual Machines creadas a partir de recursos de Marketplace no se pueden mover entre suscripciones. Desaprovisione el recurso en la suscripción activa y vuelva a implementarlo en la nueva suscripción.

Los recursos de Virtual Machines con certificado almacenados en Key Vault se pueden trasladar al nuevo grupo de recursos en la misma suscripción, pero no entre suscripciones.

## <a name="virtual-networks-limitations"></a>Limitaciones de las redes virtuales

Para mover una red virtual emparejada, primero debe deshabilitar el emparejamiento de red virtual. Una vez deshabilitado, puede mover la red virtual. Después de moverla, vuelva a habilitar el emparejamiento de red virtual.

No puede mover una red virtual a otra suscripción distinta si la red virtual contiene una subred con vínculos de navegación de recursos. Por ejemplo, si un recurso de Redis Cache está implementado en una subred, esta contiene un vínculo de navegación de recursos.

## <a name="app-service-limitations"></a>limitaciones de App Service

Las limitaciones para mover recursos de App Service son diferentes en función de si los recursos se mueven dentro de una suscripción o a una nueva.

### <a name="moving-within-the-same-subscription"></a>Moverlos dentro de la misma suscripción

Al mover una instancia de Web App _dentro de la misma suscripción_, no se pueden mover los certificados SSL cargados. Sin embargo, puede mover una instancia de Web App al nuevo grupo de recursos sin mover su certificado SSL cargado y la funcionalidad SSL de la aplicación seguirá funcionando. 

Si desea mover el certificado SSL junto con la instancia de Web App, siga estos pasos:

1.  Elimine el certificado cargado desde la instancia de Web App.
2.  Traslade la instancia.
3.  Cargue el certificado en la instancia de Web App trasladada.

### <a name="moving-across-subscriptions"></a>Moverlos entre suscripciones

Al mover una instancia de Web App _entre suscripciones_, se aplican las limitaciones siguientes:

- El grupo de recursos de destino no debe tener ningún recurso de App Service. Entre los recursos de App Service se incluyen:
    - Web Apps
    - Planes de App Service
    - Certificados SSL cargados o importados
    - Entornos de App Service
- Todos los recursos de App Service del grupo de recursos se deben mover conjuntamente.
- Los recursos de App Service solo se pueden mover del grupo de recursos en el que se crearon originalmente. Si un recurso de App Service ya no se encuentra en su grupo de recursos original, deberá devolverse a este en primer lugar y, a continuación, se podrá mover entre suscripciones. 

## <a name="classic-deployment-limitations"></a>limitaciones de la implementación clásica

Las opciones para mover recursos implementados mediante el modelo clásico varían en función de si traslada los recursos dentro de una misma suscripción o a una nueva suscripción.

### <a name="same-subscription"></a>Misma suscripción

Al mover recursos de un grupo de recursos a otro dentro de la misma suscripción, se aplican las restricciones siguientes:

* No se pueden mover redes virtuales (clásico).
* Las máquinas virtuales (clásico) se deben mover con el servicio en la nube.
* El servicio en la nube solo se puede mover cuando el traslado incluye todas sus máquinas virtuales.
* Solo se puede mover un servicio en la nube cada vez.
* Solo se puede mover una cuenta de almacenamiento (clásico) cada vez.
* No se puede mover una cuenta de almacenamiento (clásico) con una máquina virtual o un servicio en la nube en la misma operación de traslado.

Para trasladar recursos clásicos a un grupo de recursos nuevo dentro de la misma suscripción, utilice las operaciones de traslado estándar a través del [portal](#use-portal), [Azure PowerShell](#use-powershell), la [CLI de Azure](#use-azure-cli) o la [API de REST](#use-rest-api). Utilice las mismas operaciones que utiliza para trasladar recursos de Resource Manager.

### <a name="new-subscription"></a>Suscripción nueva

Al trasladar recursos a una nueva suscripción, se aplican las restricciones siguientes:

* Todos los recursos clásicos de la suscripción se deben mover en la misma operación.
* La suscripción de destino no debe contener otros recursos clásicos.
* El traslado solo puede solicitarse a través de una API de REST independiente para el traslado de recursos clásicos. Los comandos de movimiento estándar de Resource Manager no funcionan para mover recursos clásicos a una nueva suscripción.

Para trasladar recursos clásicos a una nueva suscripción, use operaciones REST específicas para recursos clásicos. Para usar REST, siga estos pasos:

1. Compruebe si la suscripción de origen puede participar en un movimiento entre suscripciones. Utilice la siguiente operación:

  ```HTTP
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     En el cuerpo de la solicitud, incluya:

  ```json
  {
    "role": "source"
  }
  ```

     La respuesta para la operación de validación está en el formato siguiente:

  ```json
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. Compruebe si la suscripción de destino puede participar en un movimiento entre suscripciones. Utilice la siguiente operación:

  ```HTTP
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     En el cuerpo de la solicitud, incluya:

  ```json
  {
    "role": "target"
  }
  ```

     La respuesta está en el mismo formato que la validación de la suscripción de origen.
3. Si ambas suscripciones superan la validación, traslade todos los recursos clásicos de una suscripción a otra con la siguiente operación:

  ```HTTP
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    En el cuerpo de la solicitud, incluya:

  ```json
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

Es posible que esta operación tarde varios minutos.

## <a name="recovery-services-limitations"></a>Limitaciones de Recovery Services

No se admite el traslado para recursos de Storage, Network o Compute que se usan para configurar la recuperación ante desastres de Azure Site Recovery.

Por ejemplo, suponga que ha configurado la replicación de las máquinas locales en una cuenta de almacenamiento (Storage1) y desea que la máquina protegida aparezca después de la conmutación por error en Azure como una máquina virtual (VM1) conectada a una red virtual (Network1). No puede mover ninguno de estos recursos de Azure, Storage1, VM1 y Network1, en grupos de recursos dentro de la misma suscripción o entre suscripciones.

Procedimiento para mover una máquina virtual inscrita en **Azure Backup** entre grupos de recursos:
 1. Detenga temporalmente la copia de seguridad y conserve los datos de esta
 2. Traslade la máquina virtual al grupo de recursos de destino
 3. Vuelva a protegerla en el mismo almacén o en otro nuevo. Los usuarios pueden restaurar desde los puntos de restauración disponibles creados antes de la operación de traslado.
Aunque el usuario traslade la máquina virtual de la que se ha realizado la copia de seguridad entre suscripciones, los pasos 1 y 2 serán los mismos. En el paso 3, el usuario necesita proteger la máquina virtual en un nuevo almacén presente o en uno creado en la suscripción de destino. El almacén de Recovery Services no admite copias de seguridad entre suscripciones.

## <a name="hdinsight-limitations"></a>Limitaciones de HDInsight

Puede mover clústeres de HDInsight a una nueva suscripción o un nuevo grupo de recursos. Sin embargo, no puede mover entre suscripciones los recursos de red vinculados al clúster de HDInsight (por ejemplo, la red virtual, una NIC o un equilibrador de carga). Además, tampoco se puede mover a un nuevo grupo de recursos una NIC que está conectada a una máquina virtual del clúster.

Al mover un clúster de HDInsight a una nueva suscripción, mueva primero otros recursos (por ejemplo, la cuenta de almacenamiento). Después, mover el clúster de HDInsight.

## <a name="search-limitations"></a>Limitaciones de Search

No puede mover varios recursos de Search ubicados en regiones diferentes a la vez.
Si se da el caso, deberá moverlos por separado.

## <a name="use-portal"></a>Mediante el portal

Para trasladar recursos, seleccione el grupo de recursos que contiene esos recursos y, después, el botón **Mover**.

![Mover recursos](./media/resource-group-move-resources/select-move.png)

Seleccione si va a mover los recursos a un nuevo grupo de recursos o a una nueva suscripción.

Seleccione los recursos que trasladar y el grupo de recursos de destino. Confirme que tiene que actualizar los scripts para estos recursos y seleccione **Aceptar**. Si ha seleccionado el icono de edición de la suscripción en el paso anterior, también debe seleccionar la suscripción de destino.

![seleccionar destino](./media/resource-group-move-resources/select-destination.png)

En **Notificaciones**, podrá ver que la operación de traslado está en curso.

![mostrar el estado del traslado](./media/resource-group-move-resources/show-status.png)

Cuando haya finalizado, se le notificará del resultado.

![mostrar el resultado del traslado](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Uso de PowerShell

Para mover recursos existentes a otro grupo de recursos o a otra suscripción, use el comando [Move-AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) . El siguiente ejemplo muestra cómo trasladar varios recursos a un nuevo grupo de recursos.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Para moverlos a una nueva suscripción, especifique un valor para el parámetro `DestinationSubscriptionId`.

## <a name="use-azure-cli"></a>Uso de CLI de Azure

Para trasladar recursos existentes a otro grupo de recursos o a otra suscripción, use el comando [az resource move](/cli/azure/resource?view=azure-cli-latest#az_resource_move). Proporcione los identificadores de recursos de los recursos que se van a mover. El siguiente ejemplo muestra cómo trasladar varios recursos a un nuevo grupo de recursos. En el parámetro `--ids`, ofrezca una lista separada por espacios de los identificadores de recurso que se van a trasladar.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Para mover a una nueva suscripción, proporcione el parámetro `--destination-subscription-id`.

## <a name="use-rest-api"></a>Use la API de REST

Para trasladar recursos existentes a otro grupo de recursos o a una suscripción, ejecute:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

En el cuerpo de la solicitud, especifique el grupo de recursos de destino y los recursos a mover. Para obtener más información acerca de la operación REST de movimiento, consulte [Mover recursos](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>pasos siguientes

* Para obtener información sobre los cmdlets de PowerShell que permiten administrar su suscripción, vea [Uso de Azure PowerShell con Azure Resource Manager](powershell-azure-resource-manager.md).
* Para obtener información sobre los comandos de la CLI de Azure para administrar su suscripción, vea [Uso de la CLI de Azure para Mac, Linux y Windows con Azure Resource Manager](xplat-cli-azure-resource-manager.md).
* Si desea conocer las funciones del portal que permiten administrar la suscripción, consulte [Uso del Azure Portal para implementar y administrar los recursos de Azure](resource-group-portal.md).
* Para aprender a aplicar una organización lógica a los recursos, consulte [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md).
