# Hydeout

Hydeout updates the original [Hyde](https://github.com/poole/hyde)
theme for [Jekyll](http://jekyllrb.com) 3.x and adds new functionality.

![Desktop](/_screenshots/1.png?raw=true)
<img alt="Mobile home page" src="/_screenshots/2.png?raw=true" width="300px" />
<img alt="Mobile post page" src="/_screenshots/3.png?raw=true" width="300px" />

### Usage

Hydeout is available as the `jekyll-theme-hydeout` Ruby Gem.
Add `gem "jekyll-theme-hydeout", "~> 3.4"` to your Gemfile and run
`bundle install`.

Hydeout uses pagination, so if you have an `index.md`, you'll need to swap
it with an `index.html` that uses the `index` layout:

```
---
layout: index
title: Home
---
```

