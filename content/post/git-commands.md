+++
title = "git commands"
subtitle = "주로 hugo 사용하면서 썼던 것들"

# Add a summary to display on homepage (optional).
summary = "주로 hugo 사용하면서 썼던 것들"

date = "2019-03-12T02:22:38+09:00"
publishDate = "2019-03-12T02:22:38+09:00"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["git"]
categories = []
keywords = ["git"]

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references 
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
# projects = ["internal-project"]

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
[image]
  # Caption (optional)
  caption = ""

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = ""

draft = false
+++

submodule을 포함한 Repo를 clone할 때 명령어 옵션 `--recurse-submodules`
```
git clone --recurse-submodules https://github.com/j3rrry/hugo-site.git
```
