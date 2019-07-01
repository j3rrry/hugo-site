+++
title = "Prismjs Hugo"
subtitle = "Prismjs Hugo"

# Add a summary to display on homepage (optional).
summary = "Prismjs Hugo"

date = "2019-06-27T08:37:16+09:00"
publishDate = "2019-06-27T08:37:16+09:00"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = []
categories = []
keywords = []

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

layout/partials/custom_head.html

https://sourcethemes.com/academic/docs/customization/#customize-style-css  
config/_default/params.toml  
custom.css  
ssets/css/  
config/_default/params.toml  
plugins_css = ["custom"] 확장자없이  

https://sourcethemes.com/academic/docs/writing-markdown-latex/#highlighting-options  
highlight = false

|  config.toml  | page front matter | highlighting enabled for page? |
|---------------|-------------------|--------------------------------|
| unset or true |   unset or true   |               yes              |
| unset or true |       false       |               no               |
|    false      |   unset or false  |               no               |
|    false      |        true       |               yes              |

위의 [표](https://sourcethemes.com/academic/docs/writing-markdown-latex/#option-highlight)를 보면 config.toml이나 param.toml에 unset된 상태이고 page front matter 도 unset이면 기본으로 highlighting이 된다.
