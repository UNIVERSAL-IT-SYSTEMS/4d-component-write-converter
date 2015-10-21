# 4d-component-write-converter
4D Write to HTML/MIME converter, originally presented at the 2011 Summit.

About
---

This component provides methods to convert 4D Write documents to HTML/MIME (IE7 Quirks), XHTML or 4D multi style text. Optionally, you can inlcude jQuery in the HTML and/or replace 4D expressions with 4D TAGS. Images are embedded in the multi part MIME.

Convert 4D Write to HTML
---

```
$filePath:=Get 4D folder(Current Resources folder)+"document"

  //convert the document to a proprietary xml
$documentData:=WDATA_Create_from_document ($filePath)

Case of 
: (False)
  //default; use v12 4DTEXT tag for embedded expressions (useful to insert mutistyle text)
WDATA_SET_OPTION ("text_reference";"4d_text")
: (False)
  //use v12 4DHTML tag for embedded expressions
WDATA_SET_OPTION ("text_reference";"4d_html")
: (False)
  //use v12 4DHTMLVAR tag for embedded expressions
WDATA_SET_OPTION ("text_reference";"4d_htmlvar")
: (False)
  //use v12 4DVAR tag for embedded expressions (useful to insert mutistyle text, before 12.1)
WDATA_SET_OPTION ("text_reference";"4d_var")
End case 


Case of 
: (False)
  //default; all pages
WDATA_SET_OPTION ("page";"0")
: (False)
  //page 1
WDATA_SET_OPTION ("page";"1")
End case 


Case of 
: (False)
  //default; <body contenteditable="false">
WDATA_SET_OPTION ("content_editable";"'false'")
: (False)
  //<body contenteditable="true">
WDATA_SET_OPTION ("content_editable";"'true'")
End case 


Case of 
: (False)
  //default; do not include jQuery
WDATA_SET_OPTION ("include_jquery";"'false'")
: (False)
  //include jQuery (version1.4.2) in the result HTML
WDATA_SET_OPTION ("include_jquery";"'true'")
  //NOTE: to update jQuery, modify the file 
  //Resources/XSL/templates/scripts/jquery.xml
End case 


If (True)
  //explicit DOCTYPE of XHTML; force IE9 to standard mode
$writeData:=WRITE_Create_HTM ($documentData;"HTML")
Else 
  //implicit DOCTYPE; IE9 will default to Quirks (IE7) mode
$writeData:=WRITE_Create_HTM ($documentData;"XHTML")
End if 

If (False)
  //process 4D tags used by text_reference
PROCESS HTML TAGS($writeData;$writeData)
End if 

  //could be any temporary folder
$path:=WRITE_Get_work_folder +"document.html"

TEXT_TO_DOCUMENT ($writeData;$path)

OPEN WEB URL($path)

```

Convert 4D Write to MIME
---

```
$filePath:=Get 4D folder(Current Resources folder)+"document"

  //convert the document to a proprietary xml
$documentData:=WDATA_Create_from_document ($filePath)

Case of 
: (False)
  //default; use v12 4DTEXT tag for embedded expressions (useful to insert mutistyle text)
WDATA_SET_OPTION ("text_reference";"4d_text")
: (False)
  //use v12 4DHTML tag for embedded expressions
WDATA_SET_OPTION ("text_reference";"4d_html")
: (False)
  //use v12 4DHTMLVAR tag for embedded expressions
WDATA_SET_OPTION ("text_reference";"4d_htmlvar")
: (False)
  //use v12 4DVAR tag for embedded expressions (useful to insert mutistyle text, before 12.1)
WDATA_SET_OPTION ("text_reference";"4d_var")
End case 

  //omit page number or specify 0 to print all pages.
$template:=WRITE_MIME_Create_template ($documentData;1)

ARRAY TEXT($headerNames;4)
$headerNames{1}:="From"
$headerNames{2}:="Subject"
$headerNames{3}:="To"
$headerNames{4}:="To"

ARRAY TEXT($headerValues;4)

$from:="Keisuke MIYAKO <keisuke.miyako@4d.com>"
$subject:="test"
$To1:="miyako@4d-japan.com"
$To2:="Keisuke MIYAKO <keisuke.miyako@4d.com>"

$headerValues{1}:=$from
$headerValues{2}:=$subject
$headerValues{3}:=$To1
$headerValues{4}:=$To2

ARRAY TEXT($attachmentPaths;0)

WRITE_MIME_SET_PARAMETER (->$template;->$headerNames;->$headerValues;->$attachmentPaths)

$mimeData:=WRITE_MIME_Get_data ($template)

  //could be any temporary folder
$path:=WRITE_Get_work_folder +"document.eml"

BLOB TO DOCUMENT($path;$mimeData)

OPEN WEB URL($path)
```

Convert 4D Write to Multi Style Text
---

```
$filePath:=Get 4D folder(Current Resources folder)+"document"

  //convert the document to a proprietary xml
$documentData:=WDATA_Create_from_document ($filePath)

Case of 
: (False)
  //default; use v12 4DTEXT tag for embedded expressions (useful to insert mutistyle text)
WDATA_SET_OPTION ("text_reference";"4d_text")
: (False)
  //use v12 4DHTML tag for embedded expressions
WDATA_SET_OPTION ("text_reference";"4d_html")
: (False)
  //use v12 4DHTMLVAR tag for embedded expressions
WDATA_SET_OPTION ("text_reference";"4d_htmlvar")
: (False)
  //use v12 4DVAR tag for embedded expressions (useful to insert mutistyle text, before 12.1)
WDATA_SET_OPTION ("text_reference";"4d_var")
End case 

$writeData:=WRITE_Create_HTM ($documentData;"4D")

If (False)
  //process 4D tags used by text_reference
PROCESS HTML TAGS($writeData;$writeData)
End if 

  //could be any temporary folder
$path:=WRITE_Get_work_folder +"document.html"

TEXT_TO_DOCUMENT ($writeData;$path)

OPEN WEB URL($path)
```

Remarks
---

* Since the Summit, a container ```<div>``` with relative positioning for HTML/XHTML body has been added. This is to avoid overlaps when quoted in a reply email. In addition, typo in HTML is corrected; ```<header>``` is changed to ```<head>```. 

* $2 of ```WRITE_MIME_Create_template``` has been removed. (fixed to XHTML)
 
Because the code internally uses XSLT, it will not work with 4D or 4D Server 64 bits on OS X. Please consider the use of 4D Write Pro. 
