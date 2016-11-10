---
title: "Stream Operations in Rich Edit Controls"
ms.custom: na
ms.date: "10/14/2016"
ms.prod: "visual-studio-dev14"
ms.reviewer: na
ms.suite: na
ms.technology: 
  - "devlang-cpp"
ms.tgt_pltfrm: na
ms.topic: "article"
dev_langs: 
  - "C++"
helpviewer_keywords: 
  - "CRichEditCtrl class, stream operations"
  - "CRichEditCtrl class, stream storage"
  - "rich edit controls, stream operations"
  - "storage, stream in CRichEditCtrl"
  - "stream operations in CRichEditCtrl"
  - "stream storage and CRichEditCtrl"
ms.assetid: 110b4684-1e76-4ca6-9ef0-5bc8b2d93c78
caps.latest.revision: 8
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
# Stream Operations in Rich Edit Controls
You can use streams to transfer data into or out of a rich edit control ([CRichEditCtrl](../mfcref/cricheditctrl-class.md)). A stream is defined by an [EDITSTREAM](http://msdn.microsoft.com/library/windows/desktop/bb787891) structure, which specifies a buffer and an application-defined callback function.  
  
 To read data into a rich edit control (that is, stream the data in), use the [StreamIn](../Topic/CRichEditCtrl::StreamIn.md) member function. The control repeatedly calls the application-defined callback function, which transfers a portion of the data into the buffer each time.  
  
 To save the contents of a rich edit control (that is, stream the data out), you can use the [StreamOut](../Topic/CRichEditCtrl::StreamOut.md) member function. The control repeatedly writes to the buffer and then calls the application-defined callback function. For each call, the callback function saves the contents of the buffer.  
  
## See Also  
 [Using CRichEditCtrl](../mfc/using-cricheditctrl.md)   
 [Controls](../mfc/controls--mfc-.md)