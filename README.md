# Note

* This is a consolidated version of `compress/lzw` that supports GIF, TIFF and PDF.
* Please refer to this [golang proposal](https://github.com/golang/go/issues/25409) for details.
* For example [github.com/hhrutter/tiff](https://github.com/hhrutter/tiff) uses this package to extend [golang.org/x/image/tiff](https://github.com/golang/image/tree/master/tiff).


## Background

* PDF's LZWDecode filter comes with the optional parameter `EarlyChange`.
* The type of this parameter is `int` and the defined values are 0 and 1.
* The default value is 1.

This parameter implies two variants of lzw. (See the [PDF spec](https://www.adobe.com/content/dam/acom/en/devnet/pdf/pdfs/PDF32000_2008.pdf)).

[compress/lzw](https://github.com/golang/go/tree/master/src/compress/lzw):

* the algorithm implied by EarlyChange value 1
* provides both Reader and Writer.

[golang.org/x/image/tiff/lzw](https://github.com/golang/image/tree/master/tiff/lzw):

* the algorithm implied by EarlyChange value 0
* provides a Reader, lacks a Writer

In addition PDF expects a leading `clear_table` marker right at the beginning
which is not something the original `compress/lzw` takes into account.

There are numerous PDF Writers out there and the following can be observed on arbitrary PDF files that use the LZWDecode filter:

* Some PDF writers do not write the EOD (end of data) marker.
* Some PDF writers do not write the final bits after the EOD marker.

## Goal

A `compress/lzw` that works for a maximum number of components with a specific need for `lzw` support (as of now GIF, TIFF and PDF) and as a side effect of this an improved TIFF package.