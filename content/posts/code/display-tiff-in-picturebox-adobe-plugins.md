---
title: Display TIFF in picturebox (Adobe plugins)
author: Viktor Halitsyn
date: 2007-11-05T16:50:00+00:00
categories:
  - tech
  - code
tags:
  - c++

---
The previous post highlighted how to accomplish the given task using TIFF-PDF-JPEG-BITMAP conversion. Though this was is complicated it works perfect. But so much code for so little functionality often is not considered good if even eccepted. Recently a new way was found. It has to be said that it is not &#8220;the proper way&#8221;, because it needs some annatural code, but it is considerably lighter than the previous one. SO the idea is to draw direktly on the window using its HWND and SDK drawContentsToFunction. Not say , much &#8211; just look at the code &#8211; it is fairly simple:

//declare it somwhere in the code 🙂

```cpp
PDPage g_page;
//use this code when user selects the tiff file to display
Dialog::getStatement()->ConvertTiffToPdf(&doc, documentIndex);
if(g_page) 
{
PDDoc docToRelease = PDPageGetDoc(g_page);
PDPageRelease(g_page);
PDDocClose(docToRelease);
}
g_page = PDDocAcquirePage (doc, 0);
```

//use this code in the OnRefresh/OnPaint event handler for the desired picturebox or any HWND


```cpp
void ASAPI AnotStatementADMDialogDrawProc (ADMDialogRef inDialog, ADMDrawerRef inDrawer)
{
if (!g_page) sADMDialog->DefaultDraw(inDialog, inDrawer);
HWND hwnd = ::FindWindowEx(0, 0, 0, "Add Anoto Statement");
HWND bitmaphwnd = GetDlgItem(hwnd,IDC_STATICIMAGE);
HDC hdc = GetDC(bitmaphwnd);
RECT rect;
GetClientRect( bitmaphwnd, &rect);
::FillRect(hdc, &rect, (HBRUSH) (COLOR_WINDOW));
ASFixedMatrix matrix;
//if needed change this matrix to provide proper size
PDPageGetFlippedMatrix(g_page, &matrix);
PDPageDrawContentsToWindow(g_page, bitmaphwnd , hdc, false, &matrix, 0,0,0);
ReleaseDC(hwnd,hdc);
DeleteDC(hdc);
}
```