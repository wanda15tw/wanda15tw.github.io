# wanda15tw.github.io

Personal blog of Wanda Juan, built with Jekyll and the [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) theme. GitHub Actions builds and deploys it (`.github/workflows/pages-deploy.yml`).

## Run locally

Requires Ruby 3.1 or newer.

```sh
bundle install
bundle exec jekyll serve   # then open http://127.0.0.1:4000
```

## Where things live

- Posts: `_posts/` (`description:` is the summary shown on the home page; `pin: true` pins a post)
- Sidebar pages: `_tabs/`
- Sidebar contact icons: `_data/contact.yml`; share buttons: `_data/share.yml`
- Brand colours: bottom of `assets/css/jekyll-theme-chirpy.scss`
- Favicons: `assets/img/favicons/` (panda from Microsoft Fluent Emoji, MIT license)
- Social preview image: `assets/img/social-preview.png`
