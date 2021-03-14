# PDFs

## Compress with Ghostscript

`ghostscript` command can be used to compress PDF files. It has multiple predefined settings:

>`-dPDFSETTINGS=configuration`
>
>Presets the "distiller parameters" to one of four predefined settings:
>
>`/screen` selects low-resolution output similar to the Acrobat Distiller (up to version X) "Screen Optimized" setting.
>`/ebook` selects medium-resolution output similar to the Acrobat Distiller (up to version X) "eBook" setting.
>`/printer` selects output similar to the Acrobat Distiller "Print Optimized" (up to version X) setting.
>`/prepress` selects output similar to Acrobat Distiller "Prepress Optimized" (up to version X) setting.
>`/default` selects output intended to be useful across a wide variety of uses, possibly at the expense of a larger output file.

More information about these profiles can be found from https://www.ghostscript.com/doc/current/VectorDevices.htm#distillerparams

## References

* How can I reduce the file size of a scanned PDF file? - https://askubuntu.com/questions/113544/how-can-i-reduce-the-file-size-of-a-scanned-pdf-file
