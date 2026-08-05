# PDF assets directory

Place the following file here before deploying:

| File | Description |
|------|-------------|
| `Sri_Koganti_Resume__AI.pdf` | Active CV/résumé file linked from the header, CV page, home page, and Contact page |
| `Sri_Koganti_CV.pdf` | Older CV filename kept for backward compatibility |

> **Note:** This directory is not gitignored by default. If your CV contains home address
> or phone number details you don't want publicly committed, either remove that information
> from the PDF or add `assets/files/` to `.gitignore` and serve the file via another method
> (e.g. a private S3 bucket URL).
