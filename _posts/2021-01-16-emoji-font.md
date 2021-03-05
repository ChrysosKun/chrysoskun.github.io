---
title: archlinux下emoji显示不全
categories: [linux,arch]
comments: true
---

* TOC
{:toc}
 在一些软件中emoji显示不全。

# 安装字体

```bash
sudo pacman -S noto-fonts-emoji
```

# 配置文件

在 `/etc/fonts/conf.avail/` 中创建 `75-noto-color-emoji.conf` 文件

写入以下配置

```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>

    <!-- Add generic family. -->
    <match target="pattern">
        <test qual="any" name="family"><string>emoji</string></test>
        <edit name="family" mode="assign" binding="same"><string>Noto Color Emoji</string></edit>
    </match>

    <!-- This adds Noto Color Emoji as a final fallback font for the default font families. -->
    <match target="pattern">
        <test name="family"><string>sans</string></test>
        <edit name="family" mode="append"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test name="family"><string>serif</string></test>
        <edit name="family" mode="append"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test name="family"><string>sans-serif</string></test>
        <edit name="family" mode="append"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test name="family"><string>monospace</string></test>
        <edit name="family" mode="append"><string>Noto Color Emoji</string></edit>
    </match>

    <!-- Block Symbola from the list of fallback fonts. -->
    <selectfont>
        <rejectfont>
            <pattern>
                <patelt name="family">
                    <string>Symbola</string>
                </patelt>
            </pattern>
        </rejectfont>
    </selectfont>

    <!-- Use Noto Color Emoji when other popular fonts are being specifically requested. -->
    <match target="pattern">
        <test qual="any" name="family"><string>Apple Color Emoji</string></test>
        <edit name="family" mode="assign" binding="same"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test qual="any" name="family"><string>Segoe UI Emoji</string></test>
        <edit name="family" mode="assign" binding="same"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test qual="any" name="family"><string>Segoe UI Symbol</string></test>
        <edit name="family" mode="assign" binding="same"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test qual="any" name="family"><string>Android Emoji</string></test>
        <edit name="family" mode="assign" binding="same"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test qual="any" name="family"><string>Twitter Color Emoji</string></test>
        <edit name="family" mode="assign" binding="same"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test qual="any" name="family"><string>Twemoji</string></test>
        <edit name="family" mode="assign" binding="same"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test qual="any" name="family"><string>Twemoji Mozilla</string></test>
        <edit name="family" mode="assign" binding="same"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test qual="any" name="family"><string>TwemojiMozilla</string></test>
        <edit name="family" mode="assign" binding="same"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test qual="any" name="family"><string>EmojiTwo</string></test>
        <edit name="family" mode="assign" binding="same"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test qual="any" name="family"><string>Emoji Two</string></test>
        <edit name="family" mode="assign" binding="same"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test qual="any" name="family"><string>EmojiSymbols</string></test>
        <edit name="family" mode="assign" binding="same"><string>Noto Color Emoji</string></edit>
    </match>

    <match target="pattern">
        <test qual="any" name="family"><string>Symbola</string></test>
        <edit name="family" mode="assign" binding="same"><string>Noto Color Emoji</string></edit>
    </match>

</fontconfig>
```

# 启用

```bash
sudo ln -sf /etc/fonts/conf.avail/75-noto-color-emoji.conf /etc/fonts/conf.d/
```

# 参考资料

[[1]](https://www.reddit.com/r/archlinux/comments/9q8dlj/how_to_better_enable_color_emojis/)

[[2]](https://wiki.archlinux.org/index.php/Font_configuration)

