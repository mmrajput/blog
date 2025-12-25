# Complete Guide to GitHub Pages Blog with Jekyll

A step-by-step tutorial to create your technical blog using GitHub Pages and Jekyll - perfect for documenting your HomeLab journey.

Key Sections:
✅ Complete installation guides for Windows, macOS, Ubuntu/WSL
✅ Theme recommendations (Chirpy is perfect for tech blogs)
✅ Directory structure explained
✅ Front matter (post metadata) examples
✅ Image handling for screenshots
✅ Custom domain setup (optional)
✅ SEO optimization tips
✅ Common issues and solutions
✅ Daily workflow for consistent posting

# Recommended Next Steps:

Start with Method 1 to get your blog live today
Migrate to Method 2 when you want more control
Use Chirpy theme - it's perfect for technical content
Post your Glances article as your first blog post
Add screenshots from your actual Pi 5 setup

# For Your HomeLab Portfolio:
Your blog URL will be: https://your-github-username.github.io
This demonstrates:

Version control skills (Git)
Documentation abilities
Technical writing
Modern DevOps tools
Professional online presence

Would you like help with:

Setting up your specific GitHub repository?
Choosing and configuring a theme?
Converting your Glances post to Jekyll format?
Adding code syntax highlighting?
---

## What is GitHub Pages + Jekyll?

**GitHub Pages** is a free hosting service by GitHub that serves static websites directly from your repository.

**Jekyll** is a static site generator that converts Markdown files into beautiful blogs. It's the default engine for GitHub Pages.

**Benefits:**
- ✅ **Free hosting** with custom domain support
- ✅ **Version control** for all your posts
- ✅ **Markdown writing** - simple and portable
- ✅ **No database** - just files
- ✅ **HTTPS** - automatic SSL certificates
- ✅ **Professional** - used by many developers

---

## Method 1: Quick Start (Recommended for Beginners)

### Step 1: Create GitHub Repository

1. Go to [GitHub](https://github.com) and sign in
2. Click **"New repository"** (green button)
3. Name it: `username.github.io` (replace `username` with your GitHub username)
   - Example: `mahmood.github.io`
4. Select **Public**
5. Check **"Add a README file"**
6. Click **"Create repository"**

Your blog will be available at: `https://username.github.io`

### Step 2: Enable GitHub Pages

1. Go to repository **Settings**
2. Scroll to **Pages** section (left sidebar)
3. Under **Source**, select:
   - Branch: `main`
   - Folder: `/ (root)`
4. Click **Save**
5. Wait 1-2 minutes, refresh page
6. You'll see: "Your site is published at https://username.github.io"

### Step 3: Choose a Theme

1. In **Settings** → **Pages**
2. Click **"Choose a theme"**
3. Browse themes and click **"Select theme"**
4. This creates `_config.yml` automatically

### Step 4: Create Your First Blog Post

1. In your repository, click **"Add file"** → **"Create new file"**
2. Name it: `_posts/2024-12-17-my-first-post.md`
   - Format: `YYYY-MM-DD-title.md`
3. Add this content:

```markdown
---
layout: post
title: "My First Blog Post"
date: 2024-12-17
categories: blog
---

# Hello World!

This is my first post on GitHub Pages.

## What I'm Learning

- Kubernetes
- Linux Administration
- DevOps Tools
```

4. Click **"Commit new file"**
5. Visit `https://username.github.io` after 1-2 minutes

---

## Method 2: Local Development (Professional Setup)

This method lets you preview posts before publishing and gives full control.

### Prerequisites

Install Git, Ruby, and Jekyll on your system.

#### On Ubuntu/WSL (Windows Subsystem for Linux):

```bash
# Update packages
sudo apt update

# Install Ruby and dependencies
sudo apt install ruby-full build-essential zlib1g-dev git -y

# Configure Ruby gems path
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Install Jekyll and bundler
gem install jekyll bundler

# Verify installation
jekyll --version
```

#### On Windows (native):

1. Download [RubyInstaller](https://rubyinstaller.org/) (Ruby+Devkit 3.2.X)
2. Run installer, check "Add Ruby to PATH"
3. Open Command Prompt:

```cmd
gem install jekyll bundler
jekyll --version
```

#### On macOS:

```bash
# Install Homebrew if not installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Ruby
brew install ruby

# Add to PATH
echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# Install Jekyll
gem install jekyll bundler
```

### Step 1: Create GitHub Repository

Same as Method 1 - create `username.github.io` repository.

### Step 2: Clone Repository Locally

```bash
# Clone your repository
git clone https://github.com/username/username.github.io.git
cd username.github.io

# Verify you're in the right place
pwd
ls -la
```

### Step 3: Initialize Jekyll Site

```bash
# Create Jekyll site in current directory
jekyll new . --force

# Your directory now contains:
# _config.yml       - Site configuration
# _posts/           - Blog posts folder
# _site/            - Generated site (don't edit)
# Gemfile           - Ruby dependencies
# index.markdown    - Homepage
# about.markdown    - About page
```

### Step 4: Configure Your Site

Edit `_config.yml`:

```yaml
title: Mahmood's HomeLab Journey
email: your-email@example.com
description: >-
  Documenting my Kubernetes learning journey. Building a production-grade
  K3s cluster on Raspberry Pi 5 for DevOps and cloud-native skills.
baseurl: ""
url: "https://username.github.io"
twitter_username: your_twitter
github_username: username

# Build settings
theme: minima
plugins:
  - jekyll-feed
  - jekyll-seo-tag

# Show excerpts on home page
show_excerpts: true
```

### Step 5: Create Your First Post

```bash
# Create posts directory
mkdir -p _posts

# Create first post
nano _posts/2024-12-17-glances-monitoring-homelab.md
```

Add this content:

```markdown
---
layout: post
title: "Monitoring Raspberry Pi 5 HomeLab with Glances"
date: 2024-12-17 14:30:00 +0100
categories: [homelab, kubernetes, monitoring]
tags: [raspberry-pi, glances, k3s, ubuntu]
---

Your blog post content here...

## Introduction

Building a Kubernetes HomeLab on Raspberry Pi 5...

[Rest of your Glances blog post]
```

### Step 6: Preview Locally

```bash
# Install dependencies
bundle install

# Serve site locally
bundle exec jekyll serve

# Or with live reload
bundle exec jekyll serve --livereload
```

Open browser: `http://localhost:4000`

**Common Commands:**
```bash
# Build site (creates _site/ folder)
bundle exec jekyll build

# Serve with drafts
bundle exec jekyll serve --drafts

# Serve on different port
bundle exec jekyll serve --port 4001

# Clean generated files
bundle exec jekyll clean
```

### Step 7: Publish to GitHub

```bash
# Add all files
git add .

# Commit with message
git commit -m "Add Glances monitoring blog post"

# Push to GitHub
git push origin main
```

Visit `https://username.github.io` after 1-2 minutes to see your live blog!

---

## Using a Professional Jekyll Theme

The default `minima` theme is basic. Let's use a better theme.

### Option 1: Chirpy Theme (Recommended for Technical Blogs)

Beautiful, feature-rich theme with dark mode, search, and categories.

```bash
# Clone Chirpy starter
git clone https://github.com/cotes2020/chirpy-starter.git username.github.io
cd username.github.io

# Install dependencies
bundle install

# Configure _config.yml
nano _config.yml
```

Update these fields:
```yaml
title: Mahmood's Tech Blog
tagline: Kubernetes | Linux | DevOps
url: 'https://username.github.io'
github:
  username: username
twitter:
  username: your_twitter
social:
  name: Your Name
  email: your@email.com
  links:
    - https://twitter.com/your_twitter
    - https://github.com/username

avatar: /assets/img/avatar.jpg  # Your photo
timezone: Europe/Berlin

theme_mode: dual  # light, dark, or dual
```

Create posts in `_posts/` with this front matter:
```markdown
---
title: Monitoring Raspberry Pi 5 with Glances
date: 2024-12-17 14:30:00 +0100
categories: [HomeLab, Monitoring]
tags: [raspberry-pi, glances, kubernetes, ubuntu]
pin: true  # Pin to top of home page
image:
  path: /assets/img/posts/glances-dashboard.png
  alt: Glances Web Dashboard
---
```

### Option 2: Minimal Mistakes

Another excellent theme for technical blogs.

```bash
# Add to Gemfile
echo 'gem "minimal-mistakes-jekyll"' >> Gemfile

# Install
bundle install
```

Update `_config.yml`:
```yaml
theme: minimal-mistakes-jekyll
minimal_mistakes_skin: "dark"  # "air", "aqua", "contrast", "dark", "dirt", "neon", "mint", "plum", "sunrise"

author:
  name: "Your Name"
  avatar: "/assets/images/bio-photo.jpg"
  bio: "Kubernetes enthusiast learning K8s administration"
  location: "Germany"
  links:
    - label: "GitHub"
      icon: "fab fa-fw fa-github"
      url: "https://github.com/username"
    - label: "Twitter"
      icon: "fab fa-fw fa-twitter-square"
      url: "https://twitter.com/username"
```

### Option 3: Browse More Themes

- **GitHub Pages Themes:** https://pages.github.com/themes/
- **Jekyll Themes:** https://jekyllthemes.io/
- **Free Themes:** http://jekyllthemes.org/

---

## Blog Post Front Matter Explained

Every post needs front matter (metadata) at the top:

```markdown
---
layout: post                    # Use post layout
title: "Your Post Title"        # Post title
date: 2024-12-17 14:30:00 +0100 # Publication date + timezone
categories: [homelab, k8s]      # Categories (max 2 recommended)
tags: [raspberry-pi, ubuntu]    # Tags for filtering
author: mahmood                 # Author name
image: /assets/img/post.jpg     # Featured image
description: "Short summary"    # Meta description for SEO
published: true                 # false to hide post
pin: false                      # true to pin to top
---

Your markdown content starts here...
```

---

## Directory Structure

```
username.github.io/
├── _config.yml           # Site configuration
├── _posts/               # Blog posts (YYYY-MM-DD-title.md)
│   ├── 2024-12-17-glances-monitoring.md
│   └── 2024-12-18-k3s-setup.md
├── _drafts/              # Unpublished drafts
│   └── upcoming-post.md
├── assets/               # Images, CSS, JS
│   ├── img/
│   └── css/
├── _site/                # Generated site (ignored by git)
├── index.markdown        # Homepage
├── about.markdown        # About page
├── Gemfile               # Ruby dependencies
└── .gitignore            # Files to ignore
```

---

## Adding Images to Posts

### Step 1: Add Images to Repository

```bash
# Create assets directory
mkdir -p assets/img/posts

# Add your image
cp ~/glances-screenshot.png assets/img/posts/

# Commit
git add assets/
git commit -m "Add images"
git push
```

### Step 2: Reference in Markdown

```markdown
![Glances Dashboard](/assets/img/posts/glances-screenshot.png)
*Figure 1: Glances web interface monitoring Raspberry Pi 5*

# Or with HTML for more control
<img src="/assets/img/posts/glances-screenshot.png" alt="Glances Dashboard" width="800">
```

---

## Custom Domain (Optional)

Want to use your own domain like `blog.yourname.com`?

### Step 1: Add CNAME File

Create `CNAME` file in repository root:
```
blog.yourname.com
```

### Step 2: Configure DNS

At your domain registrar (Namecheap, GoDaddy, etc.), add:

**For subdomain (blog.yourname.com):**
```
Type: CNAME
Host: blog
Value: username.github.io
```

**For apex domain (yourname.com):**
```
Type: A
Host: @
Value: 185.199.108.153
       185.199.109.153
       185.199.110.153
       185.199.111.153
```

Wait 24-48 hours for DNS propagation.

---

## Essential Jekyll Commands

```bash
# Create new post
touch _posts/$(date +%Y-%m-%d)-post-title.md

# Start local server
bundle exec jekyll serve

# Build for production
JEKYLL_ENV=production bundle exec jekyll build

# Check for broken links (requires html-proofer)
bundle exec htmlproofer ./_site

# Update gems
bundle update

# Clean build files
bundle exec jekyll clean
```

---

## Writing Tips for Technical Blogs

### 1. Use Code Blocks with Syntax Highlighting

```markdown
```bash
sudo apt install glances
```
```

Supported languages: bash, python, javascript, yaml, json, dockerfile, etc.

### 2. Add Table of Contents

```markdown
* TOC
{:toc}

## Section 1
## Section 2
```

### 3. Callouts/Alerts

```markdown
> **Note:** This is important information.

> **Warning:** Be careful with this command.

> **Tip:** Pro tip for advanced users.
```

### 4. Include Gists

```liquid
{% gist username/gist-id %}
```

### 5. Add Reading Time

Most themes calculate this automatically from word count.

---

## SEO Optimization

### Update _config.yml

```yaml
plugins:
  - jekyll-seo-tag
  - jekyll-sitemap
  - jekyll-feed

google_analytics: UA-XXXXXXXXX-X  # Your GA tracking ID
```

### Add Meta Description to Posts

```yaml
---
title: "Post Title"
description: "Brief 150-160 character summary for search results"
---
```

### Create robots.txt

```
User-agent: *
Allow: /
Sitemap: https://username.github.io/sitemap.xml
```

---

## Common Issues & Solutions

### Issue 1: Site Not Updating
**Solution:** Wait 2-3 minutes, clear browser cache, check Actions tab for build errors

### Issue 2: Jekyll Version Conflict
```bash
bundle update github-pages
```

### Issue 3: Permission Denied
```bash
bundle install --path vendor/bundle
bundle exec jekyll serve
```

### Issue 4: Localhost Not Loading
```bash
# Try different port
bundle exec jekyll serve --port 4001

# Or bind to all interfaces
bundle exec jekyll serve --host 0.0.0.0
```

---

## Recommended Workflow

### Daily Blogging Workflow

```bash
# 1. Pull latest changes
git pull origin main

# 2. Create new post
touch _posts/$(date +%Y-%m-%d)-topic.md

# 3. Write post in your editor
code _posts/2024-12-17-topic.md

# 4. Preview locally
bundle exec jekyll serve --livereload

# 5. Review at http://localhost:4000

# 6. Commit and push
git add .
git commit -m "Add new post: Topic"
git push origin main

# 7. Check live site in 2 minutes
```

### Create Post Template

Save as `_drafts/template.md`:

```markdown
---
layout: post
title: "Title Here"
date: YYYY-MM-DD HH:MM:SS +0100
categories: [category1, category2]
tags: [tag1, tag2, tag3]
---

## Introduction

Context and problem statement...

## Prerequisites

What you need...

## Step-by-Step Guide

### Step 1: First Step

Commands and explanation...

### Step 2: Second Step

More details...

## Troubleshooting

Common issues...

## Conclusion

Summary and next steps...

## Resources

- Link 1
- Link 2
```

---

## Next Steps

1. **Set up your GitHub repository** (`username.github.io`)
2. **Choose Method 1 or 2** based on your preference
3. **Pick a theme** (Chirpy recommended for tech blogs)
4. **Migrate your Glances blog post**
5. **Add screenshots** from your actual setup
6. **Share on social media** and dev communities
7. **Write consistently** - weekly posts build audience

---

## Resources

- **GitHub Pages Docs:** https://docs.github.com/pages
- **Jekyll Docs:** https://jekyllrb.com/docs/
- **Chirpy Theme:** https://github.com/cotes2020/jekyll-theme-chirpy
- **Markdown Guide:** https://www.markdownguide.org/
- **Jekyll Cheatsheet:** https://devhints.io/jekyll

---

## Quick Start Checklist

- [ ] Create GitHub account (if needed)
- [ ] Create `username.github.io` repository
- [ ] Enable GitHub Pages in Settings
- [ ] Choose a theme or use default
- [ ] Create first post in `_posts/`
- [ ] Add your Glances blog post
- [ ] Add screenshots to `assets/img/`
- [ ] Update `_config.yml` with your info
- [ ] Add README.md explaining your blog
- [ ] Share your blog URL on LinkedIn/Twitter

---

**Your blog will be live at: `https://username.github.io`**

Start documenting your HomeLab journey and build your professional portfolio! 🚀