---
title: Limitaciones de Azure Cloud Shell | Microsoft Docs
description: "Introducción a las limitaciones de Azure Cloud Shell"
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: juluk
ms.openlocfilehash: 08426b6142dd125a5981d65635ecc55336cb3d15
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitaciones de Azure Cloud Shell

Azure Cloud Shell tiene las limitaciones conocidas siguientes:

## <a name="general-limitations"></a>Limitaciones generales

### <a name="system-state-and-persistence"></a>Persistencia y estado del sistema

La máquina que proporciona la sesión de Cloud Shell es temporal y se recicla después de que la sesión esté inactiva durante 20 minutos. Cloud Shell requiere montar un recurso compartido de archivos de Azure. Como resultado, la suscripción debe poder configurar los recursos de almacenamiento para tener acceso a Cloud Shell. Otras consideraciones:

* Con el almacenamiento montado, solo se conservan las modificaciones dentro del directorio `clouddrive`. En Bash, también se conserva el directorio `$Home`.
* Solo se pueden montar recursos compartidos de archivos de Azure desde la [región asignada](persisting-shell-storage.md#mount-a-new-clouddrive).
  * En Bash, ejecute `env` para buscar la región establecida como `ACC_LOCATION`.
* Azure Files solo admite cuentas de almacenamiento con redundancia local o de almacenamiento con redundancia geográfica.

### <a name="browser-support"></a>Compatibilidad con exploradores

Cloud Shell es compatible con las versiones más recientes de Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox y Safari de Apple. Safari en modo privado no es compatible.

### <a name="copy-and-paste"></a>Copiar y pegar

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Solo puede haber un shell activo para un usuario determinado

Los usuarios solo pueden iniciar un tipo de shell a la vez, ya sea **Bash** o **PowerShell**. Pero puede haber varias instancias de Bash o PowerShell en ejecución al mismo tiempo. Los cambios entre Bash y PowerShell reinician Cloud Shell, lo que finaliza las sesiones existentes.

### <a name="usage-limits"></a>Límites de uso

Cloud Shell está pensado para casos de uso interactivos. Por tanto, todas las sesiones que no sean de este tipo y que se prolonguen durante mucho tiempo se finalizarán sin previo aviso.

## <a name="bash-limitations"></a>Limitaciones de Bash

### <a name="user-permissions"></a>Permisos de usuario

Los permisos se establecen como usuarios normales sin acceso a sudo. No se conserva cualquier instalación fuera del directorio `$Home`.

### <a name="clouddrive-smb-limited-permissions"></a>Permisos limitados de SMB de clouddrive
Algunos comandos del directorio `clouddrive`, como `git clone`, no tienen los permisos adecuados para leer o escribir determinados archivos. Si se produce este problema, vuelva a intentarlo desde el directorio `$Home` que no tiene limitaciones de SMB.

### <a name="editing-bashrc"></a>Editar .bashrc

Tenga cuidado al editar .bashrc, ya que puede producir errores inesperados en Cloud Shell.

### <a name="bashhistory"></a>.bash_history

El historial de comandos de Bash puede ser incoherente debido a una interrupción de la sesión de Cloud Shell o a sesiones simultáneas.

## <a name="powershell-limitations"></a>Limitaciones de PowerShell

### <a name="slow-startup-time"></a>Tiempo de inicio lento

PowerShell en Azure Cloud Shell (versión preliminar) podría tardar hasta 60 segundos en inicializarse durante la versión preliminar.

### <a name="no-home-directory-persistence"></a>El directorio $Home no persiste

Los datos que cualquier aplicación (como git, vim y otras) escriben en `$Home` no se conservan entre las sesiones de PowerShell. [Consulte aquí](troubleshooting.md#powershell-resolutions) para ver una solución alternativa.

### <a name="default-file-location-when-created-from-azure-drive"></a>Ubicación del archivo predeterminada cuando se crea a partir de la unidad de Azure:

Con los cmdlets de PowerShell, los usuarios no pueden crear archivos en la unidad de Azure. Cuando los usuarios crean nuevos archivos con otras herramientas, como vim o nano, los archivos se guardan de forma predeterminada en la carpeta C:\Users. 

### <a name="gui-applications-are-not-supported"></a>No se admiten las aplicaciones con GUI

Si el usuario ejecuta un comando que crearía un cuadro de diálogo de Windows, como `Connect-AzureAD` o `Login-AzureRMAccount`, se ve un mensaje de error como el siguiente: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

## <a name="next-steps"></a>pasos siguientes

[Solución de problemas de Cloud Shell](troubleshooting.md) <br>
[Inicio rápido de Bash](quickstart.md) <br>
[Guía de inicio rápido de PowerShell](quickstart-powershell.md)
