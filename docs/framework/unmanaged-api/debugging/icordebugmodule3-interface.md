---
title: ICorDebugModule3 インターフェイス
ms.date: 03/30/2017
api_name:
- ICorDebugModule3
api_location:
- CorDebug.dll
api_type:
- COM
f1_keywords:
- ICorDebugModule3
helpviewer_keywords:
- ICorDebugModule3 interface [.NET Framework debugging]
ms.assetid: 0b69f945-263a-4e11-8512-89d27f6ea296
topic_type:
- apiref
author: rpetrusha
ms.author: ronpet
ms.openlocfilehash: 58bad617eb91b7e529ff3d95edd06a5c73feee64
ms.sourcegitcommit: fb78d8abbdb87144a3872cf154930157090dd933
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47196822"
---
# <a name="icordebugmodule3-interface"></a>ICorDebugModule3 インターフェイス
動的モジュールのシンボル リーダーを作成します。  
  
## <a name="syntax"></a>構文  
  
```  
interface ICorDebugModule3 : IUnknown  
{  
    HRESULT CreateReaderForInMemorySymbols  
      (  
      [in] REFIID riid,  
      [out][iid_is(riid)] void **  ppObj  
      );  
};  
```  
  
## <a name="methods"></a>メソッド  
  
|メソッド|説明|  
|------------|-----------------|  
|[ICorDebugModule3::CreateReaderForInMemorySymbols メソッド](../../../../docs/framework/unmanaged-api/debugging/icordebugmodule3-createreaderforinmemorysymbols-method.md)|シンボル リーダーを作成します (通常[ISymUnmanagedReader インターフェイス](../../../../docs/framework/unmanaged-api/diagnostics/isymunmanagedreader-interface.md)) の動的モジュール。|  
  
## <a name="remarks"></a>Remarks  
 このインターフェイスは、"ICorDebugModule"および"ICorDebugModule2"インターフェイスを論理的に拡張します。  
  
> [!NOTE]
>  このインターフェイスは、コンピューター間またはプロセス間でのリモート呼び出しをサポートしていません。  
  
## <a name="requirements"></a>要件  
 **:**「[システム要件](../../../../docs/framework/get-started/system-requirements.md)」を参照してください。  
  
 **ヘッダー:** CorDebug.idl、CorDebug.h  
  
 **ライブラリ:** CorGuids.lib  
  
 **.NET framework のバージョン:** 4.5、4、3.5 SP1
  
## <a name="see-also"></a>関連項目  
 [ICorDebugRemoteTarget インターフェイス](../../../../docs/framework/unmanaged-api/debugging/icordebugremotetarget-interface.md)  
 [ICorDebug インターフェイス](../../../../docs/framework/unmanaged-api/debugging/icordebug-interface.md)  
    
 [デバッグ インターフェイス](../../../../docs/framework/unmanaged-api/debugging/debugging-interfaces.md)
