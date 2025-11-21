# High Performance Computing Center (SNS)

This repository contains the source code for the [High Performance Computing Center](https://hpccenter.sns.it/) website of the Scuola Normale Superiore.

The site is built using [Hugo](https://gohugo.io/), a fast and modern static site generator.

## Prerequisites

- [Hugo](https://gohugo.io/installation/) (Extended version recommended)
- [Git](https://git-scm.com/)

## Getting Started

1.  **Clone the repository:**

    ```bash
    git clone https://github.com/chpcsns/hpccenter.sns.it.git
    cd hpccenter.sns.it
    ```

2.  **Run the development server:**

    ```bash
    hugo server
    ```

    This will start a local web server. You can view the site at `http://localhost:1313/`. The site will automatically reload when you make changes to the content.

## Building the Site

To build the static site for production, run:

```bash
hugo
```

The generated files will be in the `public/` directory.

## Project Structure

- `content/`: Markdown content for pages.
- `layouts/`: HTML templates.
- `static/`: Static assets (images, CSS, fonts, documents).
- `hugo.toml`: Main configuration file.
