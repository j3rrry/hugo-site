+++
title = "Tmux"
subtitle = "Tmux"

# Add a summary to display on homepage (optional).
summary = "Tmux"

date = "2019-06-27T07:17:12+09:00"
publishDate = "2019-06-27T07:17:12+09:00"
expiryDate = ""

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = []

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["tmux"]
categories = []
keywords = ["tmux"]

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
1. [Prefix key](#prefix-key-index)  
1. [Sessions, Windows, Panes](#sessions-windows-panes-index)
1. [Reorder tmux windows?](#reorder-tmux-windows-index) `C-b + .`  
1. [Copy mode](#copy-mode-index)
1. [My config file](#my-config-file-index)  
&nbsp;  
&nbsp;  

# [Prefix key](#index)
프리픽스 키란, tmux 단축키를 사용하기위해 먼저 선행되어야하는 키이다.  
인공지능 스피커를 부를 때 선행되는 호출 명령어와 똑같다.  
기본 프리픽스 키는 C-b(Ctrl+b) 이다.  
만약 프리픽스 키를 [screen](https://www.gnu.org/software/screen/manual/screen.html)과 같이 C-a(Ctrl+a) 로 바꾸고 싶다면

```
# ~/.tmux.conf
set -g prefix C-a
unbind C-b
bind-key C-a send-prefix
```
&nbsp;  
&nbsp;  

# [Sessions, Windows, Panes](#index)
쉘에 `tmux` 명령어를 치면 생성되는 것이 바로 세션이다.  
세션이 생성될 때 1개의 윈도우도 같이 생성된다.  
세션 안에서 여러개의 윈도우를 생성할 수 있으며  
각각의 윈도우마다 경계선으로 구분되는 여러개의 팬(pane)을 만들 수 있다.
&nbsp;  
&nbsp;  

# [Reorder tmux windows?](#index)
보통 session 안에서 창을 여러개 띄워 놓고 하게 되는데, `창`의 순서를 바꾸고 싶을 때  
창의 이름을 바꿈으로써 순서를 바꿀 수 있다.
```
C-b + .
(move-window) [이름 바꾸기]
```

https://superuser.com/questions/343572/how-do-i-reorder-tmux-windows#comment-920091
&nbsp;  
&nbsp;  

# [Copy mode](#index)
`C-b` + `[` : copy 모드 진입  
`<Space Bar>` : copy 시작  
`<Enter>` : copy 끝  
`C-b` + `]` : paste 붙여넣기
&nbsp;  
&nbsp;  

# [My config file](#index)  
```py
# ~/.tmux.conf by j3rrry

# command key
set -g prefix C-a
unbind-key C-b
bind-key C-a send-prefix

# 0 is too far from ` ;)
set -g base-index 1

bind-key \ split-window -h
bind-key - split-window -v

# Use Alt-vim keys without prefix key to switch panes
bind -n M-h select-pane -L
bind -n M-j select-pane -D
bind -n M-k select-pane -U
bind -n M-l select-pane -R

set-window-option -g mode-keys vi

# No delay for escape key press
set -sg escape-time 0

# THEME
set -g status-bg black
set -g status-fg white
set -g window-status-current-attr bold
```
