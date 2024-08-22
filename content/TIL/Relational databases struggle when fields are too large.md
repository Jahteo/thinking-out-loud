---
draft: true
tags:
  - TIL
  - NeedMoreInfo
  - Backend
description: 
aliases:
---
Interesting convo about how the size of 1 field with 100's of kilobytes. bc the way teh tree is aligned, adding more ???XXX??? degrades performance

Not about the whole database being huge, but specifically single fields having lots of data.

Either use some sort of compression/tehnique to shrink it, or ensure that fields just don't have large data fields.
- Specific case was shrinking field size with compressors. But result was that going forward all material was being broken into smaller peices, thus smaller field sizes.
