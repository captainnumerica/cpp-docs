---
title: "How to: Detect -clr Compilation"
ms.custom: na
ms.date: "10/14/2016"
ms.prod: "visual-studio-dev14"
ms.reviewer: na
ms.suite: na
ms.technology: 
  - "devlang-cpp"
ms.tgt_pltfrm: na
ms.topic: "get-started-article"
dev_langs: 
  - "C++"
helpviewer_keywords: 
  - "compilation, detecting /clr"
  - "/clr compiler option [C++], detecting use of"
ms.assetid: a9310045-4810-4637-a64a-0b31a08791c1
caps.latest.revision: 10
ms.author: "mblome"
manager: "ghogen"
translation.priority.ht: 
  - "cs-cz"
  - "de-de"
  - "es-es"
  - "fr-fr"
  - "it-it"
  - "ja-jp"
  - "ko-kr"
  - "pl-pl"
  - "pt-br"
  - "ru-ru"
  - "tr-tr"
  - "zh-cn"
  - "zh-tw"
---
# How to: Detect /clr Compilation
Use the `_MANAGED` or `_M_CEE` macro to see if a module is compiled with **/clr**. For more information, see [/clr (Common Language Runtime Compilation)](../buildref/-clr--common-language-runtime-compilation-.md).  
  
 For more information about macros, see [Predefined Macros](../c/predefined-macros.md).  
  
## Example  
  
```  
// detect_CLR_compilation.cpp  
// compile with: /clr  
#include <stdio.h>  
  
int main() {  
   #if (_MANAGED == 1) || (_M_CEE == 1)  
      printf_s("compiling with /clr\n");  
   #else  
      printf_s("compiling without /clr\n");  
   #endif  
}  
```  
  
## See Also  
 [Using C++ Interop (Implicit PInvoke)](../cli/using-c---interop--implicit-pinvoke-.md)