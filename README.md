# hexo
用于hexo同步

# 安装

``` bash
git clone https://github.com/xiongraorao/hexo.git
cd hexo
npm install -g hexo-cli
npm install hexo --save
npm install -f package.json

# 下载 next 主题
mkdir themes
git clone https://github.com/xiongraorao/hexo-theme-next.git themes/next
## 下载canvas 动态背景插件 (optional)
cd themes/next/
git clone https://github.com/theme-next/theme-next-canvas-nest source/lib/canvas-nest
git clone https://github.com/theme-next/theme-next-three source/lib/three
git clone https://github.com/theme-next/theme-next-canvas-ribbon source/lib/canvas-ribbon
```
