title: '使用FBX SDK导出ASCII的FBX文件'
date: 2016-07-25 14:48:06
categories: ['技术指南']
tags: ['FBX', '3D']
---

使用[FBX SDK](http://www.autodesk.com/products/fbx/overview)的时候，导出的FBX默认总是Binary的，也就是说它直接用notepad打开后多数内容是乱码。使用binary的方式可以使文件size变小，但是却失去了可读性，这对于FBX SDK的初学者是很不利的。所以我认为FBX SDK一定会有导出ASCII的方法，然而[FBX SDK Help文档](http://help.autodesk.com/view/FBX/2017/ENU/)里并没有相关的直接指导。最后还是看到了一篇[论坛文章](
http://forums.autodesk.com/t5/fbx-sdk/fbx-export-binary-ascii/td-p/4146847?nobounce)才让我茅塞顿开。
当然这个论坛讨论中，第一个solution采用了EXP_ASCIIFBX，这个经过测试是无效的，但是第二个方法却很有用。
如果只是寻找解决方法的话，那么完全没有必要去点开那个论坛，我会在下面详细说明。

<!--more-->

其实Sample程序中就有内容可以参照：

```bash
$FBXSDK_DIR/201X.X/samples/ConvertScene/main.cxx
```

这一段就是与导出的格式直接相关的。总体来说，它的实现很简单，只是通过

```c++
int lFormat = lSdkManager->GetIOPluginRegistry()->FindWriterIDByDescription(fileTypeExpression);
```

找到Format对应的标号，然后导出的时候作为参数导出即可。

```c++
lExporter->Initialize(lNewFileName, lFormat, lSdkManager->GetIOSettings());
lExporter->Export(lScene);
```

这篇短文没什么技术含量，做一小记，希望能够帮助后来人。

附main.cxx代码全文。

```c++
/****************************************************************************************

   Copyright (C) 2014 Autodesk, Inc.
   All rights reserved.

   Use of this software is subject to the terms of the Autodesk license agreement
   provided at the time of installation or download, or which otherwise accompanies
   this software in either electronic or hard copy form.

****************************************************************************************/

/////////////////////////////////////////////////////////////////////////
//
// This program converts any file in a format supported by the FBX SDK
// into DAE, FBX, 3DS, OBJ and DXF files.
//
// Steps:
// 1. Initialize SDK objects.
// 2. Load a file(fbx, obj,...) to a FBX scene.
// 3. Create a exporter.
// 4. Retrieve the writer ID according to the description of file format.
// 5. Initialize exporter with specified file format
// 6. Export.
// 7. Destroy the exporter
// 8. Destroy the FBX SDK manager
//
/////////////////////////////////////////////////////////////////////////

#include <fbxsdk.h>

#include "../Common/Common.h"

#define SAMPLE_FILENAME "box.fbx"

const char* lFileTypes[] =
{
    "_dae.dae",            "Collada DAE (*.dae)",
    "_fbx7binary.fbx", "FBX binary (*.fbx)",
    "_fbx7ascii.fbx",  "FBX ascii (*.fbx)",
    "_fbx6binary.fbx", "FBX 6.0 binary (*.fbx)",
    "_fbx6ascii.fbx",  "FBX 6.0 ascii (*.fbx)",
    "_obj.obj",            "Alias OBJ (*.obj)",
    "_dxf.dxf",            "AutoCAD DXF (*.dxf)"
};

int main(int argc, char** argv)
{
	FbxString lFilePath("");
	for( int i = 1, c = argc; i < c; ++i )
	{
		if( FbxString(argv[i]) == "-test" ) continue;
		else if( lFilePath.IsEmpty() ) lFilePath = argv[i];
	}
	if( lFilePath.IsEmpty() ) lFilePath = SAMPLE_FILENAME;

    FbxManager* lSdkManager = NULL;
    FbxScene* lScene = NULL;

    // Prepare the FBX SDK.
    InitializeSdkObjects(lSdkManager, lScene);

	bool lResult = LoadScene(lSdkManager, lScene, lFilePath.Buffer());
    if( lResult )
    {		
		const size_t lFileNameLength = strlen((argc>=3)?argv[2]:lFilePath.Buffer());
        char* lNewFileName = new char[lFileNameLength+64];
        FBXSDK_strcpy(lNewFileName,lFileNameLength+64,(argc>=3)?argv[2]:lFilePath.Buffer());

        const size_t lFileTypeCount = sizeof(lFileTypes)/sizeof(lFileTypes[0])/2;

        for(size_t i=0; i<lFileTypeCount; ++i)
        {
            // Retrieve the writer ID according to the description of file format.
            int lFormat = lSdkManager->GetIOPluginRegistry()->FindWriterIDByDescription(lFileTypes[i*2+1]);

            // Construct the output file name.
            FBXSDK_strcpy(lNewFileName+lFileNameLength-4,60, lFileTypes[i*2]);

            // Create an exporter.
            FbxExporter* lExporter = FbxExporter::Create(lSdkManager, "");

            // Initialize the exporter.
			lResult = lExporter->Initialize(lNewFileName, lFormat, lSdkManager->GetIOSettings());
            if( !lResult )
            {
                FBXSDK_printf("%s:\tCall to FbxExporter::Initialize() failed.\n", lFileTypes[i*2+1]);
                FBXSDK_printf("Error returned: %s\n\n", lExporter->GetStatus().GetErrorString());
            }
            else
            {
                // Export the scene.
				lResult = lExporter->Export(lScene);
                if( !lResult )
                {
                    FBXSDK_printf("Call to FbxExporter::Export() failed.\n");
                }
            }

            // Destroy the exporter.
            lExporter->Destroy();
        }
        delete[] lNewFileName;
    }
	else
    {
        FBXSDK_printf("Call to LoadScene() failed.\n");
    }

    // Delete the FBX SDK manager. All the objects that have been allocated
    // using the FBX SDK manager and that haven't been explicitly destroyed
    // are automatically destroyed at the same time.
    DestroySdkObjects(lSdkManager, lResult);
    return 0;
}

```
