README
======

1. Introduction
2. Features
3. Configuration
4. Authors
5. Original Authors
6. Repository
7. Links


INTRODUCTION
============

brzboot is derived from gummiboot. It executes EFI images. The default entry
is selected by a configured pattern (glob) or an on-screen menu.  

brzboot operates on the EFI System Partition (ESP) only. Configuration file   
fragments, kernels, initrds, other EFI images need to reside on the ESP.   
Linux kernels must be built with CONFIG_EFI_STUB to be able to be directly  
executed as an EFI image.

brzboot reads simple and entirely generic configurion files; one file per  
boot entry, in legacy mode; otherwise, a single configurion file.  

Pressing Space (or most other) keys during bootup will show an on-screen  
menu with all configured entries to select from. Pressing enter on the  
selected entry loads and starts the EFI image.  

If no timeout is configured and no key pressed during bootup, the default  
entry is booted right away.  

FEATURES
========

   - Support for console menu.
   - Support for graphics menu.
   - Support for Linux EFI32 booting.
   - Support for Linux EFI64 booting.
   - Support for Windows EFI64 booting.
   - Support for Apple OSX EFI64 booting.
   - Support for Auto key enrollment (future release).


CONFIGURATION
=============

   An /EFI/brzboot/loader.conf for boot loader defaults.  
   An /EFI/brzboot/entries.conf for bootlist entries.  
   See manual brzboot.8 for more information.


AUTHORS
=======

Pierre Innocent (dev@breezeos.com)  
The Breeze::OS website: http://www.breezeos.com  


ORIGINAL AUTHORS
================

Kay Sievers (key@vrfy.org)


REPOSITORY
==========

   Brzboot github.io: https://dev-breeze-com.github.io/brzboot  
   Brzboot v0.9.1: https://www.github.com/dev-breeze-com/brzboot  


LINKS
=====

  http://www.freedesktop.org/wiki/Specifications/BootLoaderSpec  

