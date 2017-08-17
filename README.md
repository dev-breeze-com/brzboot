brzboot Simple UEFI boot manager
================================

brzboot executes EFI images. The default entry is selected by a configured  
pattern (glob) or an on-screen menu.  

brzboot operates on the EFI System Partition (ESP) only. Configuration  
file fragments, kernels, initrds, other EFI images need to reside on the  
ESP. Linux kernels must be built with CONFIG_EFI_STUB to be able to be  
directly executed as an EFI image.  

brzboot reads simple and entirely generic configurion files; one file  
per boot entry to select from.  

Pressing Space (or most other) keys during bootup will show an on-screen  
menu with all configured entries to select from. Pressing enter on the  
selected entry loads and starts the EFI image.  

If no timeout is configured and no key pressed during bootup, the default  
entry is booted right away.  

Links:
  http://www.freedesktop.org/wiki/Specifications/BootLoaderSpec  
