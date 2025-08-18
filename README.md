# SeqWeb website & documentation repo

This repository contains the static pages for [SeqWeb](https://www.seqweb.org/).
The site is built and served by GitHub Pages using Jekyll.
It also is home for all project documentation (see `docs/` folder)

## Repository Structure

```
seqweb/                   # Root directory
├── _drafts/              # Development drafts, midst-migration debris and other WIP
├── _includes/            # Jekyll includes
│   ├── footer.html       # Footer template
│   ├── license_notice.md # License notice content for footer (both CC-BY-SA and MIT)
│   └── navigation.html   # Navbar header template
├── _layouts/             # Jekyll layouts
│   └── default.html      # Default page layout
├── assets/               # Asset files
│   ├── drawings/         # Diagrams etc - `.drawio` sources and `.svg` renderings
│   └── icons/            # Icon `.png` files - scaless sources, 32x32 & 64x64 sizes
├── docs/                 # Documentation content pages
├── .gitignore            # Git ignore patterns
├── _config.yml           # Jekyll configuration
├── CNAME                 # Custom domain config
├── CONTRIBUTING.md       # Contributing guidelines [todo: more content]
├── index.md              # Landing page
├── LICENSE               # MIT license for this repo
└── README.md             # This README file
```
