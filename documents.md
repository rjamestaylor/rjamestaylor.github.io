---
layout: default
title: Documents
permalink: /documents/
exclude_from_search: true
---

# Documents

This page contains links to important documents.

* [Sample Document](/assets/documents/sample-document.pdf)
* [Dear Saints in the Lord's Recovery](/assets/documents/Dear%20Saints%20in%20the%20Lord%27s%20Recovery%2C.pdf)

## Notes on Linking to Files with Special Characters

When linking to files with spaces, single quotes, and other special characters in Markdown:

1. **URL-encoded approach (recommended for maximum compatibility):**
   ```markdown
   [Link Text](/assets/documents/Dear%20Saints%20in%20the%20Lord%27s%20Recovery%2C.pdf)
   ```

2. **Direct approach (often works in Jekyll):**
   ```markdown
   [Link Text](/assets/documents/Dear Saints in the Lord's Recovery,.pdf)
   ```

Jekyll typically handles URL encoding automatically, but the URL-encoded approach is safer for all systems.