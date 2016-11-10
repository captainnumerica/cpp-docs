---
title: "Special CWinApp Services"
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
ms.assetid: 0480cd01-f629-4249-b221-93432d95b431
caps.latest.revision: 9
manager: ghogen
translation.priority.ht: 
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
# Special CWinApp Services
Besides running the message loop and giving you an opportunity to initialize the application and clean up after it, [CWinApp](../VS_visualcpp/CWinApp-Class.md) provides several other services.  
  
##  <a name="_core_shell_registration"></a> Shell Registration  
 By default, the MFC Application Wizard makes it possible for the user to open data files that your application has created by double-clicking them in File Explorer or File Manager. If your application is an MDI application and you specify an extension for the files your application creates, the MFC Application Wizard adds calls to the [RegisterShellFileTypes](../Topic/CWinApp::RegisterShellFileTypes.md) and [EnableShellOpen](../Topic/CWinApp::EnableShellOpen.md) member functions of [CWinApp](../VS_visualcpp/CWinApp-Class.md) to the `InitInstance` override that it writes for you.  
  
 `RegisterShellFileTypes` registers your application's document types with File Explorer or File Manager. The function adds entries to the registration database that Windows maintains. The entries register each document type, associate a file extension with the file type, specify a command line to open the application, and specify a dynamic data exchange (DDE) command to open a document of that type.  
  
 `EnableShellOpen` completes the process by allowing your application to receive DDE commands from File Explorer or File Manager to open the file chosen by the user.  
  
 This automatic registration support in `CWinApp` eliminates the need to ship a .reg file with your application or to do special installation work.  
  
 If you want to initialize GDI+ for your application (by calling [GdiplusStartup](_gdiplus_FUNC_GdiplusStartup_token_input_output_) in your [InitInstance](../Topic/CWinApp::InitInstance.md) function), you have to suppress the GDI+ background thread.  
  
 You can do this by setting the **SuppressBackgroundThread** member of the [GdiplusStartupInput](_gdiplus_STRUC_GdiplusStartupInput) structure to **TRUE**. When suppressing the GDI+ background thread, the **NotificationHook** and **NotificationUnhook** calls (see [GdiplusStartupOutput](_gdiplus_STRUC_GdiplusStartupOutput)) should be made just prior to entering and exiting the application's message loop. Therefore, a good place to call **GdiplusStartup** and the hook notification functions would be in an override of the virtual function [CWinApp::Run](../Topic/CWinApp::Run.md), as shown below:  
  
 [!CODE [NVC_MFCDocView#6](../CodeSnippet/VS_Snippets_Cpp/NVC_MFCDocView#6)]  
  
 If you do not suppress the background GDI+ thread, DDE commands can be prematurely issued to the application before its main window has been created. The DDE commands issued by the shell can be prematurely aborted, resulting in error messages.  
  
##  <a name="_core_file_manager_drag_and_drop"></a> File Manager Drag and Drop  
 Files can be dragged from the file view window in File Manager or File Explorer to a window in your application. You might, for example, enable one or more files to be dragged to an MDI application's main window, where the application could retrieve the file names and open MDI child windows for those files.  
  
 To enable file drag and drop in your application, the MFC Application Wizard writes a call to the [CWnd](../VS_visualcpp/CWnd-Class.md) member function [DragAcceptFiles](../Topic/CWnd::DragAcceptFiles.md) for your main frame window in your `InitInstance`. You can remove that call if you do not want to implement the drag-and-drop feature.  
  
> [!NOTE]
>  You can also implement more general drag-and-drop capabilities—dragging data between or within documents—with OLE. For information, see the article [Drag and Drop (OLE)](../VS_visualcpp/Drag-and-Drop--OLE-.md).  
  
##  <a name="_core_keeping_track_of_the_most_recently_used_documents"></a> Keeping Track of the Most Recently Used Documents  
 As the user opens and closes files, the application object keeps track of the four most recently used files. The names of these files are added to the File menu and updated when they change. The framework stores these file names in either the registry or in the .ini file, with the same name as your project and reads them from the file when your application starts up. The `InitInstance` override that the MFC Application Wizard creates for you includes a call to the [CWinApp](../VS_visualcpp/CWinApp-Class.md) member function [LoadStdProfileSettings](../Topic/CWinApp::LoadStdProfileSettings.md), which loads information from the registry or .ini file, including the most recently used file names.  
  
 These entries are stored as follows:  
  
-   In Windows NT, Windows 2000, and later, the value is stored to a registry key.  
  
-   In Windows 3.x, the value is stored in the WIN.INI file.  
  
-   In Windows 95 and later, the value is stored in a cached version of WIN.INI.  
  
## See Also  
 [CWinApp: The Application Class](../VS_visualcpp/CWinApp--The-Application-Class.md)