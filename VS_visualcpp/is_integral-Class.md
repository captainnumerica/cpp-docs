---
title: "is_integral Class"
ms.custom: na
ms.date: 10/06/2016
ms.devlang: 
  - C++
ms.prod: visual-studio-dev14
ms.reviewer: na
ms.suite: na
ms.technology: 
  - devlang-cpp
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: fe9279d0-4495-4e88-bf23-153cc6602397
caps.latest.revision: 15
manager: ghogen
translation.priority.mt: 
  - cs-cz
  - de-de
  - es-es
  - fr-fr
  - it-it
  - ja-jp
  - ko-kr
  - pl-pl
  - pt-br
  - ru-ru
  - tr-tr
  - zh-cn
  - zh-tw
---
# is_integral Class
Tests if type is integral.  
  
## Syntax  
  
```  
template<class Ty>  
    struct is_integral;  
```  
  
#### Parameters  
 `Ty`  
 The type to query.  
  
## Remarks  
 An instance of the type predicate holds true if the type `Ty` is one of the integral types, or a `cv-qualified` form of one of the integral types, otherwise it holds false.  
  
 An integral type is one of `bool`, `char`, `unsigned char`, `signed char`, `wchar_t`, `short`, `unsigned short`, `int`, `unsigned int`, `long`, and `unsigned long`. In addition, with compilers that provide them, an integral type can be one of `long long`, `unsigned long long`, `__int64`, and `unsigned __int64`.  
  
## Example  
  
```  
// std_tr1__type_traits__is_integral.cpp   
// compile with: /EHsc   
#include <type_traits>   
#include <iostream>   
  
struct trivial   
    {   
    int val;   
    };   
  
int main()   
    {   
    std::cout << "is_integral<trivial> == " << std::boolalpha   
        << std::is_integral<trivial>::value << std::endl;   
    std::cout << "is_integral<int> == " << std::boolalpha   
        << std::is_integral<int>::value << std::endl;   
    std::cout << "is_integral<float> == " << std::boolalpha   
        << std::is_integral<float>::value << std::endl;   
  
    return (0);   
    }  
  
```  
  
  **is_integral<trivial\> == false**  
**is_integral<int\> == true**  
**is_integral<float\> == false**    
## Requirements  
 **Header:** <type_traits>  
  
 **Namespace:** std  
  
## See Also  
 [<type_traits>](../VS_visualcpp/-type_traits-.md)   
 [is_enum Class](../VS_visualcpp/is_enum-Class.md)   
 [is_floating_point Class](../VS_visualcpp/is_floating_point-Class.md)