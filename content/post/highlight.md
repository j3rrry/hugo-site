+++
title = "Highlight"
subtitle = "Highlight"

# Add a summary to display on homepage (optional).
summary = "Highlight"

date = "2019-06-27T11:04:12+09:00"
publishDate = "2019-06-27T11:04:12+09:00"
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

# Index
1. [markup](#markup-index)  
1. [c](#c-index)  
1. [css](#css-index)
1. [python](#python-index)
1. [go](#go-index)

# [markup](#index)
```markup
markup
```

# [c](#index)
```c
#include <stdio.h>

int main(){
    int a = 1, b = 2;
    printf("%d\n", a+b);
}
```

# [css](#index)
```css
p {
    word-break: keep-all;
}
```

# [python](#index)
```python
import ctypes

def main():
    a = ctypes.c_int(0x1000000000000000000000).value
    print a

if __name__ == '__main__':
    main()
```

# [go](#index)
<pre data-line="2"><code class="language-go">
// golang
func checkOrigin(r *http.Request) bool {
    o := r.Header.Get("Origin")
    if o == "" {
        log.Print("Websocket missing origin and/or host")
        return false
    }
    return true
}
</code></pre>
