---
date: 2020-01-01
lastmod: ["lastmod"]
title: Hugo
description: How to build a blog using Hugo that is a fast and flexible static site generator made of Golang.
tags: ["Hugo", "Golang", "static contents"]
---

# Hugo

## What's Hugo?
* Hugo is a fast and flexible static site generator made of Golang.  
https://gohugo.io/about/

## Preparation

### Create a git repository using Github

1. Repositories > New
2. Repository name: sample
3. Click "Create repository"

### Install Hugo on macOS
```shell
$ brew install hugo
```

### Build a blog using Hugo
```shell
$ GITHUB_USERNAME=xxxx
$ GIT_REPOSITORY=sample
```
```shell
$ hugo new site ${GIT_REPOSITORY}
$ cd ${GIT_REPOSITORY}
$ echo "# ${GIT_REPOSITORY}" >> README.md
```

### Initialize git
```shell
$ git init
```

If you want to change the configrations of git
```shell
$ git config --local --list
$ git config --local user.name xxxx
$ git config --local user.email xxxx
```

```shell
$ git add .
$ git commit -m "Build a site using hugo"
$ git branch -M main
$ git remote add origin https://github.com/${GITHUB_USERNAME}/${GIT_REPOSITORY}.git
$ git push -u origin main
```

### Add .gitignore
```shell
$ vi .gitignore
```
Add a line break on the last line.  
```.gitignore```
```shell
public/
resources/_gen/
.DS_Store
*.swp

```

```shell
$ git add .
$ git commit -m "Add .gitignore"
```

### Add Hugo's theme
```shell
$ git submodule add https://github.com/alex-shpak/hugo-book themes/hugo-book
$ git add .
$ git commit -m "Add a theme as a submodule"
```

### Change config.toml
 
```config.toml```
```toml
baseURL = "/sample/" # Write your git repository as a directory
languageCode = "en-us"
title = "Sample"
publishDir = "docs"
theme = "hugo-book"
# Add a line break on the last line. 
```

```shell
$ git add .
$ git commit -m "Change config.toml"
```

### Add a page of hello world

```contents/hello-world.md```

```md
---
date: 2021-01-01
lastmod: ["lastmod"]
title: "Hello world"
tags: ["Foo","Bar"]
---

# Hello world!
```

```shell
$ git add .
$ git commit -m "Add a page of hello world"
```

### Generate static contents
```shell
$ hugo
```
Execute the following instead above one if you want to minify static contents
```shell
$ hugo --minify
```

```shell
$ git add .
$ git commit -m "Generate static contents"
```

### Check on your local environment
```shell
$ hugo server --minify -p 3000
```
Access the following URL using a browser.
```shell
$ echo http://localhost:3000/${GIT_REPOSITORY}/hello-world/
```

### Push to Github
```shell
$ git push -u origin main
```

## Github Pages
Access the following URL using a browser.
```shell
$ echo https://github.com/${GITHUB_USERNAME}/${GIT_REPOSITORY}/settings/pages
```

1. Source: > Branch: main > /docs
2. Click "Save"

Access the following URL using a browser after a few minute.
```shell
$ echo https://${GITHUB_USERNAME}.github.io/${GIT_REPOSITORY}/hello-world/
```

## Left Menu

{{<hint danger>}}
TODO: Bundle menu mode is deprecated and will be removed.
{{</hint>}}
```config.toml```
```toml {hl_lines=[2]}
[params]
  BookMenuBundle = '/menu'
```

```content/menu/index.md```

```md {hl_lines=[2,6]}
---
headless: true
---

- **Sample**
  - [Hello world]({{</* ref "hello-world" */>}})
  - Please write your link here as above.
```

## Breadcrumb

```layouts/partials/docs/breadcrumb.html```
```go-html-template
<ol  class="breadcrumb">
    {{ template "breadcrumbnav" (dict "p1" . "p2" .) }}
</ol>

{{ define "breadcrumbnav" }}
{{ if .p1.Parent }}
{{ template "breadcrumbnav" (dict "p1" .p1.Parent "p2" .p2 )  }}
{{ else if not .p1.IsHome }}
{{ template "breadcrumbnav" (dict "p1" .p1.Site.Home "p2" .p2 )  }}
{{ end }}
<li{{ if eq .p1 .p2 }} class="active"{{ end }}>
  <a href="{{ .p1.Permalink }}">{{ .p1.Title }}</a>
</li>
{{ end }}
```

```layouts/partials/docs/inject/content-before.html```
```go-html-template
{{ partial "docs/breadcrumb" . }}
```

```assets/_custom.scss```
```scss
/* Breadcrumb */
.breadcrumb {
    padding: 8px 15px;
    margin-bottom: 20px;
    list-style: none;
    background-color: var(--gray-100);
    border-radius: 4px;
    ol {
        display: block;
        list-style-type: decimal;
        margin-block-start: 1em;
        margin-block-end: 1em;
        margin-inline-start: 0px;
        margin-inline-end: 0px;
        padding-inline-start: 40px;
    }
    li {
        display: inline-block;
    }
    li+li:before {
        padding: 0 5px;
        color: #ccc;
        content: "/\00a0";
    }
    a {
        color: var(--color-link);
    }
}
```

## Multilingual Mode

### Change config.toml
Add the following to ```config.toml```
```toml
defaultContentLanguage = "en"
defaultContentLanguageInSubdir = true

[languages]
  [languages.en]
    languageName = "English"
    languageCode = "en"
    # contentDir = "content/en"
    weight = 1
    #[languages.en.params]
    #  key1 = value1
  [languages.ja]
    languageName = "日本語"
    languageCode = "ja"
    # contentDir = "content/ja"
    weight = 2
    #[languages.ja.params]
    #  key1 = value1
```

### Customize a select box of languages
```layouts/partials/docs/languages.html```  
Overwrite ```themes/hugo-book/layouts/partials/docs/languages.html```.

```go-html-template {hl_lines=["23-24",30]}
<!-- Merge home and current page translations -->
{{ $bookTranslatedOnly := default false .Site.Params.BookTranslatedOnly }}
{{ $translations := dict }}
{{ if (eq $bookTranslatedOnly false ) }}
  {{ range .Site.Home.AllTranslations }}
    {{ $translations = merge $translations (dict .Language.Lang .) }}
  {{ end }}
{{ end }}
{{ range .Translations }}
  {{ $translations = merge $translations (dict .Language.Lang .) }}
{{ end }}

<div class="book-languages" tabindex="0" aria-haspopup="true">
  <ul>
    <li class="flex align-center">
      <img src="{{ "svg/translate.svg" | relURL }}" class="book-icon" alt="Languages" />
      {{ $.Site.Language.LanguageName }}
    </li> 
  </ul>

  <ul class="book-languages-list">
    {{ range .Site.Languages }}{{ with index $translations .Lang }}
      {{ if (ne $.Site.Language .Language) }}
      <li>
      <a href="{{ .Permalink }}" class="flex align-center">
          <img src="{{ "svg/translate.svg" | relURL }}" class="book-icon" alt="Languages" />
          {{ .Language.LanguageName }}
      </a>
      </li>
    {{ end }}{{ end }}{{ end }}
  </ul>
</div>
```

```layouts/partials/docs/search.html```  
Overwrite ```themes/hugo-book/layouts/partials/docs/search.html```  
to put a select box of languages above a search box.

```go-html-template {hl_lines=[1]}
{{ partial "docs/languages" . }}

{{ if default true .Site.Params.BookSearch }}
<div class="book-search">
  <input type="text" id="book-search-input" placeholder="{{ i18n "Search" }}" aria-label="{{ i18n "Search" }}" maxlength="64" data-hotkeys="s/" />
  <div class="book-search-spinner hidden"></div>
  <ul id="book-search-results"></ul>
</div>
{{ end }}
```

```assets/_custom.scss```
```scss
/* Multilingual Mode */
.book-languages {
    .book-languages-list {
        bottom: 74%;
    }
    .book-languages-list a {
        padding: .3rem 1rem;
    }
}

.book-menu-content {
    .book-languages {
        img {
            height: 1em;
            width: 1em;
            margin-inline-end: .5rem;
        }
        .book-languages-list {
            padding: 0;
            bottom: 68%;
        }
        .book-languages-list a {
            padding: 0 1rem;
        }
    }
}
```

## Tags of a page

```layouts/partials/docs/tags.html```
```go-html-template
<h2>Tags</h2>

{{ with .Params.tags }}
  <ul class="tags">
    {{- range . -}}
      <li><a href="{{ "/tags/" | relLangURL }}{{ . | urlize }}">{{ . }}</a>
    {{- end -}}
  </ul>
{{ end }}
```

```layouts/partials/docs/inject/toc-before.html```  
Overwrite ```themes/hugo-book/layouts/partials/docs/inject/toc-before.html```
```go-html-template
{{ partial "docs/tags" . }}
```

```layouts/partials/docs/toc.html```  
Overwrite ```themes/hugo-book/layouts/partials/docs/toc.html```
```go-html-template {hl_lines=[2]}
{{ partial "docs/inject/toc-before" . }}
<h2>Table of Contents</h2>
{{ .TableOfContents }}
{{ partial "docs/inject/toc-after" . }}
```

```assets/_custom.scss```
```scss
/* Table of Contents */
.book-toc-content {
    ul.tags {
        padding-inline-start: 0;
        li {
            line-height: 2;
            list-style: none;
        }
    }
}
```


## Syntax Highlighting
https://gohugo.io/content-management/syntax-highlighting/  
e.g) If you write the following in sample.md file of your article.
{{<highlight text>}}
```go {linenos=table,hl_lines=["4-5",9],linenostart=1}
package main

import (
	"fmt"
	"time"
)

func main() {
	fmt.Println("The time is", time.Now())
}
```
{{</highlight>}}

You can see the following syntax highlighting on the web page.
```go {linenos=table,hl_lines=["4-5",9],linenostart=1}
package main

import (
	"fmt"
	"time"
)

func main() {
	fmt.Println("The time is", time.Now())
}
```

## Mermaid Diagram
https://mermaid-js.github.io/mermaid/#/  
e.g) If you write the following in ``sample.md`` file of your article
```text
{{</*mermaid*/>}}
graph LR
    A[Square Rect] -- Text --> B((Circle))
    A --> C(Round Rect)
    B --> D{Rhombus}
    C --> D
{{</*/mermaid*/>}}
```
You can see the following diagram on the web page.
{{<mermaid>}}
graph LR
    A[Square Rect] -- Text --> B((Circle))
    A --> C(Round Rect)
    B --> D{Rhombus}
    C --> D
{{</mermaid>}}

## References

* https://gohugo.io/about/
* https://gohugo.io/content-management/multilingual/
* https://gohugo.io/content-management/syntax-highlighting/
* https://mermaid-js.github.io/mermaid/#/