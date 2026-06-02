# Second Order (有点稳)

A personal blog built with [Hugo](https://gohugo.io/) and the [Stack theme](https://github.com/CaiJimmy/hugo-theme-stack).

**Site:** [blog.nicelylit.net](https://blog.nicelylit.net)

## Overview

The site documents real-world problems and fundamental methods in software engineering, alongside reflections on growth, observation, and introspection beyond code.

## Features

- **Bilingual Support:** Content is available in both Chinese and English.
- **Categories:**
  - **Engineering (工程):** Problems, Methods, and Reflections in Software Engineering.
  - **Writing (随笔):** Thinking, Reading, Living, and Practice.
- **Search:** Uses Stack’s built-in search page.
- **Comments**: giscus powered by GitHub Discussions.
- **Math Support:** LaTeX rendering for mathematical expressions.
- **Responsive Design:** Optimized for mobile and desktop using the Stack theme.
- **Analytics**: Baidu site statistics integrated, English version TBD.
- **Deployment**: CI builds on push and syncs the generated site to an OSS bucket.
- **CDN**: Speedup with Aliyun CDN for visits from CN, global speedup TBD.

## Getting Started

### Prerequisites

- [Hugo](https://gohugo.io/installation/) (Extended version recommended)
- [Git](https://git-scm.com/)

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/nicelylit/secondorder-site.git
   cd secondorder-site
   ```

2. Initialize the theme submodule:
   ```bash
   git submodule update --init --recursive
   ```

### Development

Run the local development server:

```bash
hugo server -D
```

The site will be available at `http://localhost:1313`.

### Building

To generate the static site for production:

```bash
hugo --minify
```

The output will be in the `public/` directory.

## Project Structure

- `content/`: Contains all blog posts and pages, organized by language (`zh-cn`, `en`).
- `layouts/`: Custom layout overrides for the theme.
- `static/`: Static assets like images and favicons.
- `config.yaml`: Main configuration file for Hugo.
- `themes/`: The Stack theme (managed as a submodule).

## Contributing

If you'd like to contribute or suggest changes, please open an issue or submit a pull request.

## License

This project is open-source. Please refer to the theme's license in `themes/stack/LICENSE` for more details regarding the theme usage.
