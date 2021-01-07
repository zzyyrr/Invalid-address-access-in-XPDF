# Invalid-address-access-in-XPDF 
A invalid address access in XPDF-4.02

We met a segmentation fault when using pdfimages.
According to the information from gdb, we find that there is a invalid address access in PageLabelNode::~PageLabelNode, in Catalog.cc, in XPDF 4.02.

How to reproduce: ./pdfimages id:000064,sig:11,src:001656+000550,op:splice,rep:2 /dev/null



The backtrace of gdb:
(gdb) bt
#0  PageLabelNode::~PageLabelNode (this=0x21, __in_chrg=<optimized out>)
    at /home/fack/programs/xpdf-4.02/xpdf/Catalog.cc:141
#1  0x000000000040e713 in Catalog::~Catalog (this=0x8dd9d0, 
    __in_chrg=<optimized out>)
    at /home/fack/programs/xpdf-4.02/xpdf/Catalog.cc:295
#2  0x0000000000469350 in PDFDoc::setup2 (this=this@entry=0x8db420, 
    ownerPassword=ownerPassword@entry=0x0, 
    userPassword=userPassword@entry=0x0, repairXRef=repairXRef@entry=1)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:312
#3  0x000000000046942c in PDFDoc::setup (this=this@entry=0x8db420, 
    ownerPassword=ownerPassword@entry=0x0, userPassword=userPassword@entry=0x0)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:266
#4  0x0000000000469714 in PDFDoc::PDFDoc (this=0x8db420, 
    fileNameA=0x7fffffffe192 "./id:000064,sig:11,src:001656+000550,op:splice,rep:2", ownerPassword=0x0, userPassword=0x0, coreA=<optimized out>)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:208
#5  0x0000000000401e6b in main (argc=3, argv=0x7fffffffdd88)
    at /home/fack/programs/xpdf-4.02/xpdf/pdfimages.cc:117


The detail gdb information:

$ gdb ./pdfimages 

(gdb) set args ./id:000064,sig:11,src:001656+000550,op:splice,rep:2 /dev/null
(gdb) b 117
Breakpoint 1 at 0x401e47: file /home/fack/programs/xpdf-4.02/xpdf/pdfimages.cc, line 117.
(gdb) r
Starting program: /home/fack/下载/crashs/pdfimages/pdfimages ./segment_fault/id:000064,sig:11,src:001656+000550,op:splice,rep:2 /dev/null

Breakpoint 1, main (argc=3, argv=0x7fffffffdda8)
    at /home/fack/programs/xpdf-4.02/xpdf/pdfimages.cc:117
117	  doc = new PDFDoc(fileName, ownerPW, userPW);
(gdb) s
PDFDoc::PDFDoc (this=0x8db410, 
    fileNameA=0x7fffffffe1a6 "./segment_fault/id:000064,sig:11,src:001656+000550,op:splice,rep:2", ownerPassword=0x0, userPassword=0x0, coreA=0x0)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:157
warning: Source file is more recent than executable.
157	PDFDoc::PDFDoc(char *fileNameA, GString *ownerPassword,
(gdb) b 208
Breakpoint 2 at 0x469706: file /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc, line 208.
(gdb) c
Continuing.

Breakpoint 2, PDFDoc::PDFDoc (this=0x8db410, 
    fileNameA=0x7fffffffe1a6 "./segment_fault/id:000064,sig:11,src:001656+000550,op:splice,rep:2", ownerPassword=0x0, userPassword=0x0, coreA=<optimized out>)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:208
208	  ok = setup(ownerPassword, userPassword);
(gdb) s
PDFDoc::setup (this=this@entry=0x8db410, 
    ownerPassword=ownerPassword@entry=0x0, userPassword=userPassword@entry=0x0)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:253
253	sGBool PDFDoc::setup(GString *ownerPassword, GString *userPassword) {
(gdb) b 266
Breakpoint 3 at 0x469419: file /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc, line 266.
(gdb) c
Continuing.
Syntax Warning: PDF version H.////?ler<</Snze -- xpdf supports version 2.0 (continuing anyway)
Syntax Error: Couldn't read xref table
Syntax Warning: PDF file is damaged - attempting to reconstruct xref table...

Breakpoint 3, PDFDoc::setup (this=this@entry=0x8db410, 
    ownerPassword=ownerPassword@entry=0x0, userPassword=userPassword@entry=0x0)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:266
266	      if (!PDFDoc::setup2(ownerPassword, userPassword, gTrue)) {
(gdb) s
PDFDoc::setup2 (this=this@entry=0x8db410, 
    ownerPassword=ownerPassword@entry=0x0, 
    userPassword=userPassword@entry=0x0, repairXRef=repairXRef@entry=1)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:288
288			     GBool repairXRef) {
(gdb) b 312
Breakpoint 4 at 0x46933f: file /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc, line 312.
(gdb) c
Continuing.
Syntax Error (63): Invalid hex escape in name
Syntax Error (101): Illegal character '>'
Syntax Error (124): Dictionary key must be a name object
Syntax Error (129): Dictionary key must be a name object
Syntax Error: End of file inside array
Syntax Error: End of file inside dictionary
Syntax Error (124): Dictionary key must be a name object
Syntax Error (129): Dictionary key must be a name object
Syntax Error: Catalog object is wrong type (null)
Syntax Error: Couldn't read page catalog

Breakpoint 4, PDFDoc::setup2 (this=this@entry=0x8db410, 
    ownerPassword=ownerPassword@entry=0x0, 
    userPassword=userPassword@entry=0x0, repairXRef=repairXRef@entry=1)
    at /home/fack/programs/xpdf-4.02/xpdf/PDFDoc.cc:312
312	    delete catalog;
(gdb) s
Catalog::~Catalog (this=0x8dda00, __in_chrg=<optimized out>)
    at /home/fack/programs/xpdf-4.02/xpdf/Catalog.cc:260
260	Catalog::~Catalog() {
(gdb) n
263	  if (pageTree) {
(gdb) 
264	    delete pageTree;
(gdb) 
266	  if (pages) {
(gdb) 
276	  gDestroyMutex(&pageMutex);
(gdb) 
278	  dests.free();
(gdb) 
279	  nameTree.free();
(gdb) 
280	  if (baseURI) {
(gdb) 
281	    delete baseURI;
(gdb) 
283	  metadata.free();
(gdb) 
284	  structTreeRoot.free();
(gdb) 
285	  outline.free();
(gdb) 
286	  acroForm.free();
(gdb) 
287	  if (form) {
(gdb) 
288	    delete form;
(gdb) 
290	  ocProperties.free();
(gdb) 
291	  if (embeddedFiles) {
(gdb) 
294	  if (pageLabels) {
(gdb) 
295	    deleteGList(pageLabels, PageLabelNode);
(gdb) s
GList::get (i=0, this=0x8ddb30) at /home/fack/programs/xpdf-4.02/goo/GList.h:48
48	  void *get(int i) { return data[i]; }
(gdb) n
Catalog::~Catalog (this=0x8dda00, __in_chrg=<optimized out>)
    at /home/fack/programs/xpdf-4.02/xpdf/Catalog.cc:295
295	    deleteGList(pageLabels, PageLabelNode);
(gdb) p pageLabels 
$1 = (GList *) 0x8ddb30
(gdb) p pageLabelNode
No symbol "pageLabelNode" in current context.
(gdb) p PageLabelNode
试图使用类型名作为一个表达式
(gdb) s
GList::get (i=1, this=0x8ddb30) at /home/fack/programs/xpdf-4.02/goo/GList.h:48
48	  void *get(int i) { return data[i]; }
(gdb) p data[i]
$2 = (void *) 0x21
(gdb) p i
$3 = 1
(gdb) n
Catalog::~Catalog (this=0x8dda00, __in_chrg=<optimized out>)
    at /home/fack/programs/xpdf-4.02/xpdf/Catalog.cc:295
295	    deleteGList(pageLabels, PageLabelNode);
(gdb) s
PageLabelNode::~PageLabelNode (this=0x21, __in_chrg=<optimized out>)
    at /home/fack/programs/xpdf-4.02/xpdf/Catalog.cc:140
140	PageLabelNode::~PageLabelNode() {
(gdb) n
141	  delete prefix;
(gdb) p prefix
Cannot access memory at address 0x29
(gdb) 
