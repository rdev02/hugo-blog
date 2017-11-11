---
title: Tiff to Picture box in PDF plugin ADM dialog
author: Viktor Halitsyn
date: 2007-11-01T10:22:00+00:00
type: post
categories:
  - tech
  - code
disqusIdentifier: 6001065618
tags:
  - c++

---
Today i would like to continue PDF plugins development theme. The next thing i was struggling with for about three days was displaying \*TIFF ot \*TIF images in Picture box control on he ADMDialog. Well nice thing is it can do this, bad thing is: not the way we think it should be. Unfortunately i havent found the other way as to convert image file to JPEG format(through PDF) and that load it as BITMAP which i could put into the ADMIconRef&#8230;Also i would like to admit that after loading the HBITMAP i had to resize it to the proper the of pcture box i had. There i used some nice piece of code found on http://www.codeguru.com/cpp/g-m/bitmap/specialeffects/article.php/c4897/. So here is the code i used.
  
//header file for TIFF-PDF-JPEG-HBITMAP conversion


```cpp
#include 

<map>
  #include <olectl.h>
  #include <cstring>
  #include "AVCmdDefs.h"
  /*
  * PDF to HBITMAP conversion classes
  */
  typedef map<int,string> TiffToJPGMap;
  class TiFFDialogPrivewHelper
  {
  public: 
  static HBITMAP getBitmapFromPDF(int documentIndex,WORD width, WORD height);
  static void initTempFolder();
  static void cleanTempFolder();
  private:
  static TiffToJPGMap m_convertedPDFs;
  static bool m_isTempFolderInit;
  static string m_tempFolderName;
  static void convertPDFToJPG(int documentIndex, string jpegFullPath);
  static HBITMAP resizeBitmap(HBITMAP hBmp, WORD wNewWidth, WORD wNewHeight);
  static HBITMAP getJPEGFromPDF(string jpegName);
  static HBITMAP getBitmapFromJPEG(string jpegFullPath);
  /*
  * Classt tha provides PDF to JPEG file conversion via AVConversion API
  */
  class ConversionFromPDFProvider
  {
  private:
  static AVConversionFromPDFHandler gConversionHandler;
  PDEContent volatile pdeContent;
  
  void GetJPEGHandler(); 
  public:
  ACCB1 void ACCB2 Convert (int documentIndex, string jpegFullpath);
  static ACCB1 ASBool ACCB2 fromPDFEnumProc(AVConversionFromPDFHandler handler, AVConversionEnumProcData data);
  };
  /*
  * BITMAP resize functions
  */
  //
  // Functions for smooth bitmap resize
  //
  // Improvement: float calculations changed to int.
  //
  // Ivaylo Byalkov, January 24, 2000
  // e-mail: ivob@i-n.net
  //
  // helper function prototypes
  static BITMAPINFO *PrepareRGBBitmapInfo(WORD wWidth, WORD wHeight);
  static void ShrinkDataInt(BYTE *pInBuff,WORD wWidth,WORD wHeight,BYTE *pOutBuff,WORD wNewWidth,WORD wNewHeight);
  static void EnlargeDataInt(BYTE *pInBuff,WORD wWidth,WORD wHeight,BYTE *pOutBuff,WORD wNewWidth,WORD wNewHeight);
  static int *CreateCoeffInt(int nLen, int nNewLen, BOOL bShrink);
  ///////////////////////////////////////////////////////////
  // Main resize function
  static HBITMAP ScaleBitmapInt(HBITMAP hBmp,WORD wNewWidth,WORD wNewHeight);
  };
   
  /*
  //// realization is quite big but it works 😉
  /*
  * PDF to HBITMAP conversion implementation code
  */
  /*
  * TiFFDialogPrivewHelper function implementation
  */
  string TiFFDialogPrivewHelper::m_tempFolderName;
  HBITMAP TiFFDialogPrivewHelper::getBitmapFromJPEG(string jpegFullPath)
  {
  LPSTR pFile = new char[jpegFullPath.length() + 1];
  strcpy(pFile,jpegFullPath.c_str());
  HANDLE hFile; 
  HBITMAP hBmp; 
  DWORD dwSize; 
  DWORD dwRead; 
  HGLOBAL hMemJpeg; 
  LPSTREAM lpStream; 
  OLE_HANDLE hJpegBmp; 
  HRESULT hr; 
  LPPICTURE lpPicture = NULL; 
  void *pMemJpeg; 
  
  /* Open the file and get the size. */ 
  if((hFile = CreateFile(pFile, GENERIC_READ, FILE_SHARE_READ, 
  NULL, OPEN_EXISTING, 0, NULL)) == INVALID_HANDLE_VALUE) 
  return NULL; 
  if((dwSize = GetFileSize(hFile, NULL)) == 0xFFFFFFFF) 
  { 
  CloseHandle(hFile); 
  return NULL; 
  } 
  
  /* Allocate space for file, read it in, and then close the file again. */ 
  if((hMemJpeg = GlobalAlloc(GMEM_MOVEABLE, dwSize)) == NULL) 
  { 
  CloseHandle(hFile); 
  return NULL; 
  } 
  if((pMemJpeg = GlobalLock(hMemJpeg)) == NULL) 
  { 
  CloseHandle(hFile); 
  GlobalFree(hMemJpeg); 
  return NULL; 
  } 
  if(!ReadFile(hFile, pMemJpeg, dwSize, &dwRead, NULL)) 
  { 
  CloseHandle(hFile); 
  GlobalFree(hMemJpeg); 
  return NULL; 
  } 
  CloseHandle(hFile); 
  GlobalUnlock(hMemJpeg); 
  
  /* Create the stream and load the picture. */ 
  if((hr = CreateStreamOnHGlobal(hMemJpeg, TRUE, &lpStream)) != S_OK) 
  { 
  GlobalFree(hMemJpeg); 
  return NULL; 
  } 
  if(OleLoadPicture(lpStream, dwSize, FALSE, IID_IPicture,(void**) &lpPicture) != S_OK) 
  { 
  GlobalFree(hMemJpeg); 
  lpStream->Release(); 
  return NULL; 
  } 
  
  /* Get the handle to the image, and then copy it. */ 
  if((lpPicture->get_Handle( &hJpegBmp)) != S_OK) 
  { 
  GlobalFree(hMemJpeg); 
  lpStream->Release(); 
  lpPicture->Release(); 
  return NULL; 
  } 
  if((hBmp = (HBITMAP)CopyImage((HANDLE *) hJpegBmp, IMAGE_BITMAP, 0, 0, LR_COPYRETURNORG)) == NULL) 
  { 
  GlobalFree(hMemJpeg); 
  lpStream->Release(); 
  lpPicture->Release(); 
  return NULL; 
  } 
  
  /* Free the original image and memory. */ 
  GlobalFree(hMemJpeg); 
  lpStream->Release(); 
  lpPicture->Release(); 
  return hBmp; 
  }
  void TiFFDialogPrivewHelper::initTempFolder()
  {
  ASFileSys currentFS = ASGetDefaultFileSys();
  ASPathName asPath = ASFileSysGetTempPathName(currentFS, NULL);
  if (asPath == NULL)
  {
  m_tempFolderName = "c:";
  }
  // convert pathname from Acrobat pathname to char
  char *path = ASFileSysDisplayStringFromPath(currentFS, asPath); // buffer should be freed
  ASFileSysReleasePath(currentFS, asPath);
  if (path == NULL)
  {
  m_tempFolderName = "c:";
  }
  string pathString = path;
  ASfree(path);
  //remove the temp file name
  int slashIndex = pathString.find_last_of('\');
  if (slashIndex <= 0) {
  slashIndex = pathString.find_last_of('/');
  }
  pathString.erase(slashIndex,pathString.length() - slashIndex);
  m_tempFolderName = pathString + "//JPEGsFromPDfs//";
  ASFileSysCreateFolder(currentFS,ASFileSysCreatePathName(currentFS, ASAtomFromString("Cstring"), m_tempFolderName.c_str(), 0),false);
  m_isTempFolderInit = true;
  }
  void TiFFDialogPrivewHelper::cleanTempFolder()
  {
  ASFileSys currentFS = ASGetDefaultFileSys();
  ASPathName tempFolderName = ASFileSysCreatePathName(currentFS, ASAtomFromString("Cstring"), m_tempFolderName.c_str(), 0);
  for(TiffToJPGMap::iterator it = m_convertedPDFs.begin(); it != m_convertedPDFs.end(); it++)
  {
  ASPathName convertedJPEG = ASFileSysCreatePathName(currentFS, ASAtomFromString("Cstring"), ((pair<int,string>)(*it)).second.c_str(), 0);
  ASFileSysRemoveFile(currentFS,convertedJPEG);
  ASFileSysReleasePath(currentFS,convertedJPEG);
  }
  ASFileSysRemoveFolder(currentFS,tempFolderName);
  ASFileSysReleasePath(currentFS,tempFolderName);
  m_convertedPDFs.clear();
  m_isTempFolderInit = false;
  }
  TiffToJPGMap TiFFDialogPrivewHelper::m_convertedPDFs;
  bool TiFFDialogPrivewHelper::m_isTempFolderInit = false;
  void TiFFDialogPrivewHelper::convertPDFToJPG(int documentIndex, string jpegFullPath)
  {
  ConversionFromPDFProvider provider;
  provider.Convert(documentIndex,jpegFullPath);
  }
  HBITMAP TiFFDialogPrivewHelper::resizeBitmap(HBITMAP hBmp, WORD wNewWidth, WORD wNewHeight)
  {
  HBITMAP result = ScaleBitmapInt(hBmp,wNewWidth,wNewWidth);
  return result;
  }
  HBITMAP TiFFDialogPrivewHelper::getBitmapFromPDF(int documentIndex,WORD width, WORD height)
  {
  if(m_isTempFolderInit)
  {
  TiffToJPGMap::iterator it = m_convertedPDFs.find(documentIndex);
  if (it == m_convertedPDFs.end()) {
  //evaluate corresponding jpeg path
  string combinedJpegPath;
  combinedJpegPath.insert(0,m_tempFolderName);
  combinedJpegPath += "ConvertedPDF";
  std::stringstream ss;
  std::string stringIndex;
  ss << documentIndex;
  ss >> stringIndex;
  combinedJpegPath += stringIndex;
  combinedJpegPath += ".jpeg"; 
  convertPDFToJPG(documentIndex,combinedJpegPath);
  m_convertedPDFs.insert(pair<int,string>(documentIndex,combinedJpegPath));
  it = m_convertedPDFs.find(documentIndex);
  }
  HBITMAP jpg = getBitmapFromJPEG(((pair<int,string>)(*it)).second);
  return resizeBitmap(jpg,width,height);
  }
  return NULL;
  }
  //////////////////////////////////////////////////////////////////////////
  //////////////////////////////////////////////////////////////////////////
  //////////////////////////////////////////////////////////////////////////
  /*
  * ConversionFromPDFProvider functions
  */
  AVConversionFromPDFHandler TiFFDialogPrivewHelper::ConversionFromPDFProvider::gConversionHandler = NULL;
  
  ACCB1 void ACCB2 TiFFDialogPrivewHelper::ConversionFromPDFProvider::Convert(int documentIndex, string jpegFullPath)
  {
  ASAtom cmdName;
  AVCommand cmd;
  AVConversionFlags flags=0;
  const int MAX_PATH_LENGTH = 255;
  char temp[MAX_PATH_LENGTH]="";
  PDDoc pdDoc;
  
  //filename for the jpeg output file
  char* outFileName = new char[jpegFullPath.length() + 1];
  strcpy(outFileName, jpegFullPath.c_str());
  
  DURING 
  Dialog::getStatement()->ConvertTiffToPdf(&pdDoc,documentIndex);
  ASPathName jpgPathName = ASFileSysCreatePathName(ASGetDefaultFileSys(), ASAtomFromString("Cstring"), outFileName, 0);
  cmdName=ASAtomFromString("GeneralInfo");
  cmd=AVCommandNew(cmdName);
  volatile ASCab settings=ASCabNew();
  ASCab inputs = ASCabNew();
  ASCabPutPointer (inputs, kAVCommandKeyPDDoc, PDDoc, pdDoc, NULL);
  if (kAVCommandReady != AVCommandSetInputs (cmd, inputs)) 
  {
  //some error handling;
  }
  ASCab config = ASCabNew();
  ASCabPutInt (config, "UIPolicy", kAVCommandUIInteractive);
  if(kAVCommandReady!=AVCommandSetConfig (cmd, config))
  {
  //some error handling;
  }
  ASCabPutInt (settings, kExtractImgCmdKeyConvFormat, kImgConversionFormatJpeg);
  ASCabPutInt (settings, kExtractJpegCmdKeyCompression, kJpegCompressionLow);
  ASCabPutInt (settings, kExtractJpegCmdKeyFormat, kJpegFormatOptimized);
  ASCabPutInt (settings, kExtractImgCmdKeyColorSpace, kColorSpaceGrayscale);
  ASCabPutInt (settings, kExtractImgCmdKeyResolution, kImgResolution300);
  
  if(kAVCommandReady!=AVCommandSetParams (cmd, settings))
  {
  //some error handling;
  }
  GetJPEGHandler();
  //handler for jpeg
  if(kAVConversionFailed == AVConversionConvertFromPDFWithHandler (gConversionHandler,
  settings,//param settings 
  kAVConversionNoFlags, 
  pdDoc//active pddoc
  , jpgPathName//destn filename
  , ASGetDefaultFileSys(), NULL))
  { 
  //some error logging
  }
  PDDocClose(pdDoc);
  HANDLER
  END_HANDLER
  }
  /* fromPDFEnumProc
  * ----------------------------------------------------------------------
  *
  * Called by Acrobat as it cycles through each FromPDF handler.
  */ 
  ACCB1 ASBool ACCB2 TiFFDialogPrivewHelper::ConversionFromPDFProvider::fromPDFEnumProc(AVConversionFromPDFHandler handler, AVConversionEnumProcData data)
  {
  DURING
  //Get the name of this filter
  ASInt32 len = 255;
  AVFileFilterRec filterRec = handler->convFilter;
  char * filterName = ASTextGetPDTextCopy(filterRec.filterDescription, &len);
  //If it's the JPEG handler, then set our global handler to it.
  //Name changed to just "JPEG" in 6.0
  if ((!strcmp(filterName, "JPEG Files")) || (!strcmp(filterName, "JPEG")))
  {
  gConversionHandler = handler;
  }
  HANDLER
  return false;
  END_HANDLER
  return true;
  }
  
  void TiFFDialogPrivewHelper::ConversionFromPDFProvider::GetJPEGHandler()
  {
  AVConversionFromPDFEnumProc fromPDFEnumProcCB =
  ASCallbackCreateProto(AVConversionFromPDFEnumProc, &TiFFDialogPrivewHelper::ConversionFromPDFProvider::fromPDFEnumProc);
  AVConversionEnumFromPDFConverters (fromPDFEnumProcCB, NULL);
  ASCallbackDestroy(fromPDFEnumProcCB);
  }
  
  //////////////////////////////////////////////////////////////////////////
  //////////////////////////////////////////////////////////////////////////
  //////////////////////////////////////////////////////////////////////////
  /*
  * In memory bitmap resizing functions realizations
  */
  HBITMAP TiFFDialogPrivewHelper::ScaleBitmapInt(HBITMAP hBmp,WORD wNewWidth,WORD wNewHeight)
  {
  BITMAP bmp;
  ::GetObject(hBmp, sizeof(BITMAP), &bmp);
  // check for valid size
  if((bmp.bmWidth > wNewWidth
  && bmp.bmHeight < wNewHeight)
  || (bmp.bmWidth < wNewWidth
  && bmp.bmHeight > wNewHeight))
  return NULL;
  HDC hDC = ::GetDC(NULL);
  BITMAPINFO *pbi = PrepareRGBBitmapInfo((WORD)bmp.bmWidth,
  (WORD)bmp.bmHeight);
  BYTE *pData = new BYTE[pbi->bmiHeader.biSizeImage];
  ::GetDIBits(hDC, hBmp, 0, bmp.bmHeight, pData, pbi, DIB_RGB_COLORS);
  delete pbi;
  pbi = PrepareRGBBitmapInfo(wNewWidth, wNewHeight);
  BYTE *pData2 = new BYTE[pbi->bmiHeader.biSizeImage];
  if(bmp.bmWidth >= wNewWidth
  && bmp.bmHeight >= wNewHeight)
  ShrinkDataInt(pData,
  (WORD)bmp.bmWidth,
  (WORD)bmp.bmHeight,
  pData2,
  wNewWidth,
  wNewHeight);
  else
  EnlargeDataInt(pData,
  (WORD)bmp.bmWidth,
  (WORD)bmp.bmHeight,
  pData2,
  wNewWidth,
  wNewHeight);
  delete pData;
  HBITMAP hResBmp = ::CreateCompatibleBitmap(hDC,
  wNewWidth,
  wNewHeight);
  ::SetDIBits(hDC,
  hResBmp,
  0,
  wNewHeight,
  pData2,
  pbi,
  DIB_RGB_COLORS);
  ::ReleaseDC(NULL, hDC);
  delete pbi;
  delete pData2;
  return hResBmp;
  }
  ///////////////////////////////////////////////////////////
  BITMAPINFO *TiFFDialogPrivewHelper::PrepareRGBBitmapInfo(WORD wWidth, WORD wHeight)
  {
  BITMAPINFO *pRes = new BITMAPINFO;
  ::ZeroMemory(pRes, sizeof(BITMAPINFO));
  pRes->bmiHeader.biSize = sizeof(BITMAPINFOHEADER);
  pRes->bmiHeader.biWidth = wWidth;
  pRes->bmiHeader.biHeight = wHeight;
  pRes->bmiHeader.biPlanes = 1;
  pRes->bmiHeader.biBitCount = 24;
  pRes->bmiHeader.biSizeImage =
  ((3 * wWidth + 3) & ~3) * wHeight;
  return pRes;
  }
  ///////////////////////////////////////////////////////////
  int *TiFFDialogPrivewHelper::CreateCoeffInt(int nLen, int nNewLen, BOOL bShrink)
  {
  int nSum = 0, nSum2;
  int *pRes = new int[2 * nLen];
  int *pCoeff = pRes;
  int nNorm = (bShrink)
  ? (nNewLen << 12) / nLen : 0x1000;
  int nDenom = (bShrink)? nLen : nNewLen;
  ::ZeroMemory(pRes, 2 * nLen * sizeof(int));
  for(int i = 0; i < nLen; i++, pCoeff += 2)
  {
  nSum2 = nSum + nNewLen;
  if(nSum2 > nLen)
  {
  *pCoeff = ((nLen - nSum) << 12) / nDenom;
  pCoeff[1] = ((nSum2 - nLen) << 12) / nDenom;
  nSum2 -= nLen;
  }
  else
  {
  *pCoeff = nNorm;
  if(nSum2 == nLen)
  {
  pCoeff[1] = -1;
  nSum2 = 0;
  }
  }
  nSum = nSum2;
  }
  return pRes;
  }
  ///////////////////////////////////////////////////////////
  void TiFFDialogPrivewHelper::ShrinkDataInt(BYTE *pInBuff,
  WORD wWidth,
  WORD wHeight,
  BYTE *pOutBuff,
  WORD wNewWidth,
  WORD wNewHeight)
  {
  BYTE *pLine = pInBuff, *pPix;
  BYTE *pOutLine = pOutBuff;
  DWORD dwInLn = (3 * wWidth + 3) & ~3;
  DWORD dwOutLn = (3 * wNewWidth + 3) & ~3;
  int x, y, i, ii;
  BOOL bCrossRow, bCrossCol;
  int *pRowCoeff = CreateCoeffInt(wWidth,
  wNewWidth,
  TRUE);
  int *pColCoeff = CreateCoeffInt(wHeight,
  wNewHeight,
  TRUE);
  int *pXCoeff, *pYCoeff = pColCoeff;
  DWORD dwBuffLn = 3 * wNewWidth * sizeof(DWORD);
  DWORD *pdwBuff = new DWORD[6 * wNewWidth];
  DWORD *pdwCurrLn = pdwBuff,
  *pdwCurrPix,
  *pdwNextLn = pdwBuff + 3 * wNewWidth;
  DWORD dwTmp, *pdwNextPix;
  ::ZeroMemory(pdwBuff, 2 * dwBuffLn);
  y = 0;
  while(y < wNewHeight)
  {
  pPix = pLine;
  pLine += dwInLn;
  pdwCurrPix = pdwCurrLn;
  pdwNextPix = pdwNextLn;
  x = 0;
  pXCoeff = pRowCoeff;
  bCrossRow = pYCoeff[1] > 0;
  while(x < wNewWidth)
  {
  dwTmp = *pXCoeff * *pYCoeff;
  for(i = 0; i < 3; i++)
  pdwCurrPix[i] += dwTmp * pPix[i];
  bCrossCol = pXCoeff[1] > 0;
  if(bCrossCol)
  {
  dwTmp = pXCoeff[1] * *pYCoeff;
  for(i = 0, ii = 3; i < 3; i++, ii++)
  pdwCurrPix[ii] += dwTmp * pPix[i];
  }
  if(bCrossRow)
  {
  dwTmp = *pXCoeff * pYCoeff[1];
  for(i = 0; i < 3; i++)
  pdwNextPix[i] += dwTmp * pPix[i];
  if(bCrossCol)
  {
  dwTmp = pXCoeff[1] * pYCoeff[1];
  for(i = 0, ii = 3; i < 3; i++, ii++)
  pdwNextPix[ii] += dwTmp * pPix[i];
  }
  }
  if(pXCoeff[1])
  {
  x++;
  pdwCurrPix += 3;
  pdwNextPix += 3;
  }
  pXCoeff += 2;
  pPix += 3;
  }
  if(pYCoeff[1])
  {
  // set result line
  pdwCurrPix = pdwCurrLn;
  pPix = pOutLine;
  for(i = 3 * wNewWidth; i > 0; i--, pdwCurrPix++, pPix++)
  *pPix = ((LPBYTE)pdwCurrPix)[3];
  // prepare line buffers
  pdwCurrPix = pdwNextLn;
  pdwNextLn = pdwCurrLn;
  pdwCurrLn = pdwCurrPix;
  ::ZeroMemory(pdwNextLn, dwBuffLn);
  y++;
  pOutLine += dwOutLn;
  }
  pYCoeff += 2;
  }
  delete [] pRowCoeff;
  delete [] pColCoeff;
  delete [] pdwBuff;
  }
  ///////////////////////////////////////////////////////////
  void TiFFDialogPrivewHelper::EnlargeDataInt(BYTE *pInBuff,
  WORD wWidth,
  WORD wHeight,
  BYTE *pOutBuff,
  WORD wNewWidth,
  WORD wNewHeight)
  {
  BYTE *pLine = pInBuff,
  *pPix = pLine,
  *pPixOld,
  *pUpPix,
  *pUpPixOld;
  BYTE *pOutLine = pOutBuff, *pOutPix;
  DWORD dwInLn = (3 * wWidth + 3) & ~3;
  DWORD dwOutLn = (3 * wNewWidth + 3) & ~3;
  int x, y, i;
  BOOL bCrossRow, bCrossCol;
  int *pRowCoeff = CreateCoeffInt(wNewWidth,
  wWidth,
  FALSE);
  int *pColCoeff = CreateCoeffInt(wNewHeight,
  wHeight,
  FALSE);
  int *pXCoeff, *pYCoeff = pColCoeff;
  DWORD dwTmp, dwPtTmp[3];
  y = 0;
  while(y < wHeight)
  {
  bCrossRow = pYCoeff[1] > 0;
  x = 0;
  pXCoeff = pRowCoeff;
  pOutPix = pOutLine;
  pOutLine += dwOutLn;
  pUpPix = pLine;
  if(pYCoeff[1])
  {
  y++;
  pLine += dwInLn;
  pPix = pLine;
  }
  while(x < wWidth)
  {
  bCrossCol = pXCoeff[1] > 0;
  pUpPixOld = pUpPix;
  pPixOld = pPix;
  if(pXCoeff[1])
  {
  x++;
  pUpPix += 3;
  pPix += 3;
  }
  dwTmp = *pXCoeff * *pYCoeff;
  for(i = 0; i < 3; i++)
  dwPtTmp[i] = dwTmp * pUpPixOld[i];
  if(bCrossCol)
  {
  dwTmp = pXCoeff[1] * *pYCoeff;
  for(i = 0; i < 3; i++)
  dwPtTmp[i] += dwTmp * pUpPix[i];
  }
  if(bCrossRow)
  {
  dwTmp = *pXCoeff * pYCoeff[1];
  for(i = 0; i < 3; i++)
  dwPtTmp[i] += dwTmp * pPixOld[i];
  if(bCrossCol)
  {
  dwTmp = pXCoeff[1] * pYCoeff[1];
  for(i = 0; i < 3; i++)
  dwPtTmp[i] += dwTmp * pPix[i];
  }
  }
  for(i = 0; i < 3; i++, pOutPix++)
  *pOutPix = ((LPBYTE)(dwPtTmp + i))[3];
  pXCoeff += 2;
  }
  pYCoeff += 2;
  }
  delete [] pRowCoeff;
  delete [] pColCoeff;
  }
  //finally the Dialog::getStatement()->ConvertTiffToPdf(&pdDoc,documentIndex); which could cause questios. Here is realization of ConvertTiffToPdf function
   
  bool ConvertTiffToPdf(PDDoc *TargetPdDoc, unsigned long aIndex)
  {
  std::string aFileName = getFilenameByIndex(aIndex);
  if(aFileName.empty()) return false;
  std::string ImageFilePath = getDirectoryName() + aFileName;
  ASPathName File_To_Be_Converted_To_PDF =
  ASFileSysCreatePathName(
  ASGetDefaultFileSys(),
  ASAtomFromString("Cstring"), 
  ImageFilePath.c_str(),
  NULL);
  AVConversionStatus status = AVConversionConvertToPDF(
  kAVConversionNoFlags, 
  File_To_Be_Converted_To_PDF, 
  ASGetDefaultFileSys(), 
  TargetPdDoc, 
  NULL);
  ASFileSysReleasePath (ASGetDefaultFileSys(), File_To_Be_Converted_To_PDF);
  if(status != kAVConversionSuccess) return false;
  return true;
  }
  </int,string></int,string></int,string></int,string></cstring></olectl.h>
</map>
```