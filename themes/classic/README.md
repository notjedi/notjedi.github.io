# Hugo.io - Classic Theme

Classic is a stylized fork of the **XMin** theme, written by [Yihui Xie](https://yihui.name).

Provides `highlight.js` for syntax highlighting, emoji support, Inter and optional **_darkmode_**.

[**View live demo**](https://goodroot.ca)

### Instructions

1: Install Hugo.

```
brew install hugo
```

2: Create a new site.

```
hugo new site classic
```

3: Change to themes dir.

```
cd classic/themes
```

4: Clone the repo

```
git clone git@github.com:goodroot/hugo-classic.git
```

5: Copy files within the `exampleSite` directory

```
cp -a hugo-classic/exampleSite/. ../
```

6: Run `hugo server` within `classic/`

```
cd .. && hugo server
```

### New Posts

Make new posts:

```
hugo new post/good-to-great.md
```

### Header Colour

Adjust header colour within `static/css/style.css`

```
header {
    background: #613DC1;
}
```

... `background:` to any colour value you'd like!

For header font:

```
header a {
    color: #fff;
}
```

Change `color:` to a nice matching colour.

### Darkmode

Match or over-ride system-wide dark/light settings

1. Open `static/css/style.css`

2. Edit the following attributes to match light/dark

```css
/* darkmode */
@media (prefers-color-scheme: dark) {
    ...
}

/* lightmode */
@media (prefers-color-scheme: light) {
    ...
}
```

#### Screenshot

![Hugo Classic dark mode](/images/dark.png)
![Hugo Classic light mode](/images/light.png)

## Blog Posts

hugo-classic has appeared in...

[15 Hugo Framework blog themes](https://terrty.net/2018/15-hugo-framework-blog-themes/) by [paskal](https://github.com/paskal)
