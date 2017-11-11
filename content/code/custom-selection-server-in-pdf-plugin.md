---
title: Custom selection server in PDF plugin
author: Viktor Halitsyn
date: 2007-10-19T14:21:00+00:00
type: post
categories:
  - tech
disqusIdentifier: 5468346569
tags:
  - c++

---
Recently i was developing a feature for the Adobe(R) Acrobat Reader(R) plugin and come across an issue of selecting/highlighting annotations on the document based on some criteria e.g. if an annotation is positioned too close to the margin of the page&#8230; &#8211; bad you are :). Here i&#8217;ll post a tutorial like code for atchieving this goal for when trying to resolve that problem the only helpful information i foud was the Adobe Acrobat 7.0.5 SDK.First i would like to say that i haven&#8217;t found better way(say another way) to make slection/highlight of an annotation that creating my own selection server for that annotation. It also has to be said that every single type in Acrobat that &#8220;can be highlighted&#8221; has its own selection server(of type AVDocSelectionServer) which in practise can be obtained if you know its &#8220;internal&#8221; name. For example if you want a selection server for type &#8220;Button&#8221; you can something as following: 

```cpp
AVDocGetSelectionServerByType(ASAtomFromString("Button"));
```

There are selection servers for simple annotations but my annotation was a custom object, created by plug-in and thus didn&#8217;t have one. It didn&#8217;t prevent acrobat to use normal highlighting for it as when treating it as simple annotation in object editing mode, but the request was to provide highligting regardless of mode. So solution is to register the selection server. There is an example shipped with SDK of how to make custom selection servers but this example has a minor bug in it(it is configured so that selection will be drawn on every page whereas it has to be drawn only on the page where the object resides) and it doesnt take into consideration possibility of acrobat to change selection servers without notifying current selection server to lose its selection(for example when user press CTRL+A). So here we go with the code:First thing is to create a class which would be holding our selection and provide the selection type. To manage selections on different pages i created a simple class containinng list with page/data pairs and made it static for cross-page persistance. secondly implement the selection server. There is one workaround here in my code: to handle CTRL+A for simple objects on the page i use its selection server by injecting &#8220;losing selection&#8221; funtionality before server case takes place and then return to the normal application flow.This is definetely not a good way &#8211; but i found no other solution and this one is working:).
  
So here is the header file of the Selection Server

```cpp
/*********************************************************************
ADOBE SYSTEMS INCORPORATED
Copyright (C) 1994-2003 Adobe Systems Incorporated
All rights reserved.
NOTICE: Adobe permits you to use, modify, and distribute this file
in accordance with the terms of the Adobe license agreement
accompanying it. If you have received this file from a source other
than Adobe, then your use, modification, or distribution of it
requires the prior written permission of Adobe.
---------------------------------------------------------------------
SelectionServer.h
*********************************************************************/

#ifndef PIDGETSELECTIONSERVER_H
#define PIDGETSELECTIONSERVER_H
#include <list>
#include <map>

typedef pair<pdpage,list<asfixedRect>*> PageSelection;
  /* CustomClassSelection
  ** ------------------------------------------------------
  **
  ** This is the structure used to store selection data for
  ** selections of type "CustomClass". All callbacks in our
  ** AVDocSelectionServer can assume that selData is a
  ** pointer to one of these.
  */
  typedef struct _t_CustomClassSelection
  {
    PDAnnot annotToScrollTo;// the first annot that is highlighted
    list<pageSelection>* pageSelectionToHighlight;
    AVDoc currentDoc;
    bool isAnnotToScrollToSet;
  } CustomClassSelectionRec, *CustomClassSelection;

  // Pidget selection/highlight methods. These methods are
  // shared between the selection server and the AVTool.
  void InitializeCustomClassSelectionServer (void);
  /* SelectionServerGetType
  ** ------------------------------------------------------
  ** */
  /** We're registering a selection server of type "CustomClass".
  **
  ** @return the selection server type.
  ** */
  static ACCB1 ASAtom ACCB2 CustomClassSelectionServerGetType ()
  {
    return ASAtomFromString ("CustomClass");
  }

  ACCB1 void ACCB2 DrawCustomClassSelection (AVPageView pageView, AVDevRect *updateRect, void *data);
  extern ACCB1 void ACCB2 SelectionServerLosingSelection (AVDoc doc, void *selData, ASBool highlight);
  void* MySelectAllProc (AVDoc doc, void * selData);
  extern AVDocSelectionSelectAllProc prc;
  #endif 
  
  // !SELECTIONSERVER_H
  // ***************Its corresponding realization and helper 'drawing utils' file intended to actually draw the selection************
  /*********************************************************************
  ADOBE SYSTEMS INCORPORATED
  Copyright (C) 1994-2003 Adobe Systems Incorporated
  All rights reserved.
  NOTICE: Adobe permits you to use, modify, and distribute this file
  in accordance with the terms of the Adobe license agreement
  accompanying it. If you have received this file from a source other
  than Adobe, then your use, modification, or distribution of it
  requires the prior written permission of Adobe.
  -------------------------------------------------------------------*/ 
  /**
  file SelectionServer.cpp
  - The SelectionServer plug-in implements a selection server to handle
  pidgets. The pidgets selection tool allows users to select pidgetss by calling
  AVDocSetSelection() with a selection type of "CustomClass".
  This call passes control to the selection server, which proceeds to
  highlight the pidget.
  - The selection server functionality is limited to getting, showing, and
  losing a selection.
  *********************************************************************/
  #ifndef MAC_PLATFORM
  #include "PIHeaders.h"
  #endif
  #include "CommonHeaders.h"
  #include "SelectionServer.h"
  #include "Page.h"
  
  /*-------------------------------------------------------
  Constants/Declarations
  -------------------------------------------------------*/
  static ASAtom CustomClass_K;
  static AVPageViewDrawProc cbDrawCustomClassSelection;
  static AVDocSelectionServerRec CustomClassSelectionServer;
  static AVDocSelectionSelectAllProc prc;
  /*-------------------------------------------------------
  AVDocSelectionServer Callbacks
  -------------------------------------------------------*/
  /* SelectionServerGettingSelection
  ** ------------------------------------------------------
  ** */ 
  //** This is the selection server's callback for when a
  //** selection is registered. 
  //**
  //** @param selData pointer to an CustomClassSelectionRec allocated 
  //** through ASmalloc. The LosingSelection callback will call 
  //** ASfree() on it.
  //**
  //** @see AVAppRegisterForPageViewDrawing
  //** */
  static ACCB1 void ACCB2 SelectionServerGettingSelection (AVDoc doc, void *selData, ASBool highlight)
  {
    ASAtom atm = ASAtomFromString("Authoring");
    AVDocSelectionServer srvr = AVDocGetSelectionServerByType(atm);
    prc = srvr->SelectAll;
    srvr->SelectAll = ASCallbackCreateProto (AVDocSelectionSelectAllProc, &MySelectAllProc);
    
    
    AVPageView pageView = AVDocGetPageView(doc);
    CustomClassSelection isel = (CustomClassSelection)selData;
    // Draw the selection. It will only be drawn if the page
    // holding the selection is being displayed.
    DrawCustomClassSelection (pageView, NULL, selData);
    // Register the AVPageViewDrawProc so we can redraw the selection
    // when the page is updated.
    AVAppRegisterForPageViewDrawing (cbDrawCustomClassSelection, isel);
  }

  void* MySelectAllProc (AVDoc doc, void * selData){
    SelectionServerLosingSelection(AVAppGetActiveDoc(),Page::currentSelection,false);
    ASAtom atm = ASAtomFromString("Authoring");
    AVDocSelectionServer srvr = AVDocGetSelectionServerByType(atm);
    srvr->SelectAll = prc;
    return srvr->SelectAll(doc,selData);
  }
  /* SelectionServerLosingSelection
  ** ------------------------------------------------------
  ** */ 
  /** This is called whenever we have an active selection
  ** and the Viewer is attempting to change the selection
  ** to something else.
  **
  ** @see AVDocGetPageView
  ** @see AVAppUnregisterForPageViewDrawing
  ** @see PDPageGetNumber
  ** @see AVPageViewGetFirstVisiblePageNum
  ** @see AVPageViewGetLastVisiblePageNum
  ** @see AVPageViewRectToDevice
  ** @see AVPageViewInvalidateRect
  ** @see AVPageViewDrawNow
  ** @see PDPageRelease
  ** @see ASFree
  */
  ACCB1 void ACCB2 SelectionServerLosingSelection (AVDoc doc, void *selData, ASBool highlight)
  {
    AVPageView pageView = AVDocGetPageView(doc);
    CustomClassSelection isel = (CustomClassSelection)selData;
    ASAtom atm = ASAtomFromString("Authoring");
    AVDocSelectionServer srvr = AVDocGetSelectionServerByType(atm);
    srvr->SelectAll = prc;
    
    // Make sure that an exception doesn't prevent us from
    // releasing the PDPage.
    DURING
    // Unregister the AVPageViewDrawProc; there's no selection
    // to draw any more.
    AVAppUnregisterForPageViewDrawing (cbDrawCustomClassSelection);
    
    for(list<pageSelection>::iterator i = isel->pageSelectionToHighlight->begin(); i != isel->pageSelectionToHighlight->end(); i++)
    {
      PageSelection* iteratorSel = &(*i);
      ASInt32 pageNum = PDPageGetNumber (iteratorSel->first);
      if ((pageNum >= AVPageViewGetFirstVisiblePageNum(pageView)) &&
      (pageNum <= AVPageViewGetLastVisiblePageNum(pageView)) &#038;&#038; highlight)
      {
        AVDevRect viewRect;
        for(list<asfixedRect>::iterator pageRect = iteratorSel->second->begin(); pageRect != iteratorSel->second->end();pageRect++)
        {
          AVPageViewRectToDevice (pageView, &(*pageRect), &viewRect);
          AVPageViewInvalidateRect (pageView, &viewRect);
        }
        AVPageViewDrawNow (pageView);
      }
    }
    
    HANDLER
    END_HANDLER;
    // Release the page.
    
    for(list<pageSelection>::iterator i = isel->pageSelectionToHighlight->begin(); i != isel->pageSelectionToHighlight->end(); i++)
    {
      PDPageRelease (((PageSelection)(*i)).first);
    }
    // Free the selection data. Note that anyone calling AVDocSetSelection
    // for our selection server must have used ASmalloc to allocate the
    // memory, or this won't work.
    ASfree (isel);
  }
  /* SelectionServerShowSelection
  ** ------------------------------------------------------
  ** */ 
  /** If the user has strayed to a page different from the
  / ** selection, this callback will take the user to the
  ** correct page and scroll the selection into view.
  **
  ** @see AVPageViewBeginOperation
  ** @see AVPageViewGoTo
  ** @see AVPageViewRectToDevice
  ** @see AVPageViewScrollToRect
  ** @see AVPageViewEndOperation
  **/
  static ACCB1 void ACCB2 SelectionServerShowSelection (AVDoc doc, void *selData)
  {
  //currently not used
  }

  ASBool SelectionServerKeyDown (AVDoc doc, void * data, AVKeyCode key, AVFlagBits16 flags){
    if (key == 127) {
      AVDocClearSelection(AVAppGetActiveDoc(),false);
    }
    return false;
  }
  
  void* SelectionServerRemovedFromSelection (AVDoc doc, void * curData, void * remData, ASBool highlight)
  {
    int x = 10;
    return NULL;
  }

  /* InitializeSelectionServer
  ** ------------------------------------------------------
  ** */ 
  //** Create and register the selection server for pidgets.
  // **
  // ** @see ASAtomFromString
  // ** @see AVDocRegisterSelectionServer
  // ** */
  void InitializeCustomClassSelectionServer (void)
  {
    // Initialize our supporting globals.
    CustomClass_K = CustomClassSelectionServerGetType();
    cbDrawCustomClassSelection = ASCallbackCreateProto (AVPageViewDrawProc, DrawCustomClassSelection);
    // Set up the pidget selection server.
    memset (&CustomClassSelectionServer, 0, sizeof(AVDocSelectionServerRec));
    CustomClassSelectionServer.size = sizeof(AVDocSelectionServerRec);
    // Currently, we only implement Getting, Losing and Showing selections.
    CustomClassSelectionServer.GetType =
    ASCallbackCreateProto (AVDocSelectionGetTypeProc, &CustomClassSelectionServerGetType);
    CustomClassSelectionServer.GettingSelection =
    ASCallbackCreateProto (AVDocSelectionGettingSelectionProc, &SelectionServerGettingSelection);
    CustomClassSelectionServer.LosingSelection =
    ASCallbackCreateProto (AVDocSelectionLosingSelectionProc, &SelectionServerLosingSelection);
    CustomClassSelectionServer.ShowSelection =
    ASCallbackCreateProto (AVDocSelectionShowSelectionProc, &SelectionServerShowSelection);
    CustomClassSelectionServer.KeyDown =
    ASCallbackCreateProto (AVDocSelectionKeyDownProc, &SelectionServerKeyDown);
    CustomClassSelectionServer.RemovedFromSelection =
    ASCallbackCreateProto (AVDocSelectionRemovedFromSelectionProc, &SelectionServerRemovedFromSelection);
    AVDocRegisterSelectionServer (&CustomClassSelectionServer);
  }
 ```
  
  
  <p>
    // drawing utils implementation
  </p>
  
  
```cpp
/*********************************************************************
ADOBE SYSTEMS INCORPORATED
Copyright (C) 1994-2003 Adobe Systems Incorporated
All rights reserved.
NOTICE: Adobe permits you to use, modify, and distribute this file
in accordance with the terms of the Adobe license agreement
accompanying it. If you have received this file from a source other
than Adobe, then your use, modification, or distribution of it
requires the prior written permission of Adobe.
-------------------------------------------------------------------*/ 
/**
file SelectionServerDrawUtils.cpp
- Implementation to draw the highlight around the selected pidget.
*********************************************************************/
#ifndef MAC_PLATFORM
#include "PIHeaders.h"
#endif
#include "CommonHeaders.h"
#include "SelectionServer.h"
#include "Resource.h"
/*-------------------------------------------------------
Helper Methods
-------------------------------------------------------*/
/* DrawCustomClassSelectionHighlight
** ------------------------------------------------------
** */ 
/** Draws the selection on the pageview. Simply draws a
** rectangle around the pidget.
**
** @see AVPageViewSetColor
** @see AVPageViewDrawRectOutline
** */
static void DrawCustomClassSelectionHighlight (AVPageView pageView, const AVDevRect *selBounds)
{
  PDColorValueRec colorVal;
  // Define the color for our selection.
  colorVal.space = PDDeviceRGB ;
  colorVal.value[0] = ASInt32ToFixed(1);
  colorVal.value[1] = colorVal.value[2] = ASInt32ToFixed(0);

  // Set the drawing color for the pageview.
  AVPageViewSetColor (pageView, &colorVal);
  // Draw the highlight.
  AVPageViewDrawRectOutline(pageView, selBounds, 2, NULL, 0);
}
/* DrawCustomClassSelection
** ------------------------------------------------------
** */ 
/** This is the AVPageViewDrawProc that draws the current
** selection. It is registered whenever the selection server's
** GettingSelection callback is called, and unregistered when
** the LosingSelection callback is called. The callback is
** created in InitializeSelectionServer().
**
** @see AVPageViewGetFirstVisiblePageNum
** @see AVPageViewGetLastVisiblePageNum
** @see AVPageViewRectToDevice
** */
ACCB1 void ACCB2 DrawCustomClassSelection (AVPageView pageView, AVDevRect *updateRect, void *data)
{

  CustomClassSelection isel = (CustomClassSelection)data;
  for(list<pageSelection>::iterator i = isel->pageSelectionToHighlight->begin(); i != isel->pageSelectionToHighlight->end(); i++)
  {
    PageSelection* iteratorSel = &(*i);
    if(AVAppGetActiveDoc() != isel->currentDoc)
    {
      //this is not the document we need
      return;
    }
    ASInt32 pageNum = PDPageGetNumber (iteratorSel->first);

    DURING
    if ((pageNum >= AVPageViewGetFirstVisiblePageNum(pageView)) &&
    (pageNum <= AVPageViewGetLastVisiblePageNum(pageView))&#038;&#038;(pageNum == AVPageViewGetPageNum(pageView)))
    {
      // Convert the pidgets's bounding rect to device space.
      for(list<asfixedRect>::iterator pageRect = iteratorSel->second->begin(); pageRect != iteratorSel->second->end();pageRect++)
      {
        ASFixedRect rect = *pageRect;
        AVDevRect viewRect;
        AVPageViewRectToDevice (pageView, &rect, &viewRect);
        DrawCustomClassSelectionHighlight (pageView, &viewRect);
      }
    }
    HANDLER
    END_HANDLER
  }
}
```
  
  
  <p>
    //we&#8217;ll also need some class to hold the selection class instance
  </p>
  
  
```cpp
#include "CustomClassSelectionServer/SelectionServer.h"
class Page:
{
    public:
    Page(Form *parent);
    ~Page();
    //other custom code

    static void InitSelection();
    static void SetSelection();
    static CustomClassSelection currentSelection;
};

typedef vector<page> PageVector;

//making the selection would be fairly easy:
Page::InitSelection();
for(every_page)
{
    if( need_selection )
    {
        / /add selection rectangle
        pageSlctn.second->push_back(boundingRect);
        if(!currentSelection->isAnnotToScrollToSet)
        {
            currentSelection->annotToScrollTo = annot;
            currentSelection->isAnnotToScrollToSet = true;
            //also add referrence to document to avoid cross document painting
            currentSelection->currentDoc = AVAppGetActiveDoc();
        }


        currentSelection->pageSelectionToHighlight->push_back(pageSlctn);
    }
}

Page::SetSelection();
```