---
title: "__setusermatherr"
ms.custom: na
ms.date: 10/03/2016
ms.devlang: 
  - C++
ms.prod: visual-studio-dev14
ms.reviewer: na
ms.suite: na
ms.technology: 
  - devlang-cpp
ms.tgt_pltfrm: na
ms.topic: article
apiname: 
  - __setusermatherr
apilocation: 
  - msvcr80.dll
  - msvcr90.dll
  - msvcrt.dll
  - msvcr110.dll
  - msvcr110_clr0400.dll
  - msvcr120.dll
  - msvcr100.dll
  - api-ms-win-crt-math-l1-1-0.dll
apitype: DLLExport
ms.assetid: f306818d-381a-4d68-8739-71b92bacb5ea
caps.latest.revision: 2
manager: ghogen
translation.priority.ht: 
  - de-de
  - es-es
  - fr-fr
  - it-it
  - ja-jp
  - ko-kr
  - ru-ru
  - zh-cn
  - zh-tw
translation.priority.mt: 
  - cs-cz
  - pl-pl
  - pt-br
  - tr-tr
---
# __setusermatherr
Specifies a user-supplied rountine to handle math errors, instead of the [_matherr](../VS_visualcpp/_matherr.md) routine.  
  
## Syntax  
  
```cpp  
void __setusermatherr(  
   _HANDLE_MATH_ERROR pf   
   )  
```  
  
#### Parameters  
 `pf`  
 Pointer to an implementation of `_matherr` that is supplied by the user.  
  
 The type of the `pf` parameter is declared as `typedef int (__cdecl * _HANDLE_MATH_ERROR)(struct _exception *)`.  
  
## Remarks  
  
## Requirements  
  
|Routine|Required header|  
|-------------|---------------------|  
|__setusermatherr|matherr.c|