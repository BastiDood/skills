# Asset Distribution

Prefer not to distribute third-party static assets. Fonts, images, PDFs, documents, and other blobs from outside sources introduce provenance, license, attribution, and redistribution obligations that prompt-only skills avoid. Templates and other files that the skill authors itself carry no such obligations.

When a third-party asset is essential, identify its source and license before adding it. Bundle every license and attribution file required by that asset's terms. A repository license does not replace a third-party asset's license.

Do not redistribute a logo only because the source repository contains it. A symlink, copied file, or package reference does not grant redistribution rights.

Before release, inventory the packaged skill rather than only the source tree. Remove non-redistributable assets, remove their metadata references, and delete empty asset directories.
