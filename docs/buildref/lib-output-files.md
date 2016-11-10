---
title: "LIB Output Files"
ms.custom: na
ms.date: "10/14/2016"
ms.prod: "visual-studio-dev14"
ms.reviewer: na
ms.suite: na
ms.technology: 
  - "devlang-cpp"
ms.tgt_pltfrm: na
ms.topic: "article"
f1_keywords: 
  - "Lib"
dev_langs: 
  - "C++"
helpviewer_keywords: 
  - "output files, LIB"
ms.assetid: e73d2f9b-a42d-402b-b7e3-3a94bebb317e
caps.latest.revision: 7
ms.author: "corob"
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
# LIB Output Files
The output files produced by LIB depend on the mode in which it is being used, as shown in the following table.  
  
|Mode|Output|  
|----------|------------|  
|Default (building or modifying a library)|COFF library (.lib)|  
|Extracting a member with /EXTRACT|Object (.obj) file|  
|Building an export file and import library with /DEF|Import library (.lib) and export (.exp) file|  
  
## See Also  
 [Overview of LIB](../buildref/overview-of-lib.md)