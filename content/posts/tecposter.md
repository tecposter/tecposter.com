---
date: 2024-08-11T12:28:21.886670881Z
title: TecPoster
---
[TecPoster.com](https://tecposter.com/) is built on the top of [Hugo](https://gohugo.io/) and all of its posts are exported from markdown documents in [Obsidian](https://obsidian.md/) by using the [TecExp](https://github.com/tecposter/tecexp).

# Hugo

After entering the project root directory.

```shell
cd ./tecposter.com
```

Generate static web files to `./public`

```shell
❯ hugo       
Start building sites … 
hugo v0.128.2+extended ...


                   | EN  
-------------------+-----
  Pages            | 30  
  Paginator pages  |  0  
  Non-page files   |  8  
  Static files     |  7  
  Processed images |  0  
  Aliases          |  1  
  Cleaned          |  0  

Total in 33 ms
```

Or run a self-hosted server with the following command, which is particularly useful when developing locally.

```shell
❯ hugo server --logLevel debug --bind="0.0.0.0" -D 
Watching for changes in /home/.../tecposter.com/{archetypes,assets,content,data,layouts,static,themes}
Watching for config changes in /home/.../tecposter.com/hugo.toml, /home/.../tecposter.com/themes/tec/hugo.toml
Start building sites …
```

# TecExp

Export markdown documents which contain property `publish: web` from Obsidian vault directory to Hugo content posts directory

```shell
tecexp -o /path/to/obsidian/vault -g /path/to/hugo-site/root 
```

Get help

```shell
❯ tecexp -h
Export mds from Obsidian to Hugo

Usage: tecexp [OPTIONS] --obsidian-dir <OBSIDIAN_DIR> --hugo-dir <HUGO_DIR>

Options:
  -o, --obsidian-dir <OBSIDIAN_DIR>        Obsidian vault dir
  -g, --hugo-dir <HUGO_DIR>                Hugo dir
  -p, --hugo-posts-dir <HUGO_POSTS_DIR>    Hugo posts sub dir [default: content/posts]
  -a, --hugo-assets-dir <HUGO_ASSETS_DIR>  Hugo assets sub dir [default: content/assets]
  -w, --watch                              Watch
  -h, --help                               Print help
  -V, --version                            Print version
```




