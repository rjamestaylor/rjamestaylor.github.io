# Robot Terror Blog

This repository contains the source code for my personal blog at [rjamestaylor.github.io](https://rjamestaylor.github.io). The blog is built with Jekyll and hosted on GitHub Pages.

## Overview

This blog documents my tech projects, travel adventures, and code explorations while traveling around Asia. It features articles about cloud infrastructure, mobile development, and AI-assisted programming.

## Technology Stack

- **Static Site Generator**: Jekyll 4.3.2
- **Hosting**: GitHub Pages
- **Theme**: Modified Minima
- **CSS**: SCSS with custom styling
- **Version Control**: Git
- **CI/CD**: GitHub Actions

## Local Development

### Prerequisites

- Ruby 2.7.0 or higher
- Bundler
- Jekyll

### Setup

1. Clone the repository:
   ```bash
   git clone https://github.com/rjamestaylor/rjamestaylor.github.io.git
   cd rjamestaylor.github.io
   ```

2. Install dependencies:
   ```bash
   bundle install
   ```

3. Start local server:
   ```bash
   bundle exec jekyll serve
   ```

4. View the site at [http://localhost:4000](http://localhost:4000)

## Site Structure

```
├── _posts/             # Blog posts in Markdown
├── _includes/          # Custom header, footer, etc.
├── _layouts/           # Page layouts
├── assets/             # CSS, images, and other static assets
├── _config.yml         # Jekyll configuration
├── index.md            # Homepage
├── about.md            # About page
└── Gemfile             # Ruby dependencies
```

## Creating Content

### Adding a New Post

1. Create a new Markdown file in `_posts/` with the format `YYYY-MM-DD-title.md`
2. Add front matter at the top:
   ```yaml
   ---
   layout: post
   title: "Your Post Title"
   date: YYYY-MM-DD HH:MM:SS +0800
   categories: [category1, category2]
   tags: [tag1, tag2, tag3]
   image: /assets/images/your-image.png
   excerpt: "A brief excerpt of your post"
   ---
   ```
3. Write your post content in Markdown below the front matter

### Adding Images

1. Place images in `assets/images/`
2. Reference them in posts using:
   ```markdown
   ![Alt text](/assets/images/your-image.png)
   ```

## Deployment

The site automatically deploys to GitHub Pages when changes are pushed to the main branch.

## License

The content of this blog is licensed under the [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/), and the underlying source code is licensed under the [MIT License](https://opensource.org/licenses/MIT).

## Contact

For questions or suggestions, please open an issue in this repository or contact me at re@liability.engineering