[Main guideline for PKGBUILD](https://wiki.archlinux.org/title/PKGBUILD)

## Other packaging guidelines & tips
### Common
* don't use custom variables when not needed (use `${pkgname%-git}` instead). All custom variables should contain leading "_"
* quoting in `arch=()` `license=()` `depends=()` `makedepends=()` `depends=()` is pointless and a personal choice, except when needed:
`optdepends=('gst-libav: additional codecs') license=('custom:WTFPL') license=('custom:corp EULA')`. But always use double quotting for `"$pkgdir"` and `"$pkgscr"`
* remember to quote variables for avoiding failures due to whitespaces in paths

### pkgbase
### pkgname
* just `name` of the package or `name-git` or `name-bin`. Do not use custom variables for naming
### pkgver
* cut 'v' and other prefixes from `pkgver`
### pkgrel
* number should be increased after **any** edit of `PKGBUILD` file
### pkgdesc
* Do not add "A" at description at start, just: `pkgdesc="Web user interface to manage your WireGuard setup"`
### arch
* if developer of package provide other versions of arches you can add them, e.g. `arch=(aarch64 armv7h i686 x86_64)`
### url
* use `pkgname` for url - `$pkgname`, `${pkgname%-bin}`, `${pkgname%-git}`
### license
* install not common licenses in `"$pkgdir"/usr/share/licenses/$pkgname`, not mandatory to install common licenses; see 'licenses' package. Use proper identifier from [SPDX list](https://spdx.org/licenses/preview/index.html). For `-git` `-bin` `-whatever` packages use `install -Dm644 LICENSE -t "$pkgdir"/usr/share/licenses/${pkgname%-bin}/`
* for MIT (or similar) license for Github sourse you could use `$url/raw/main/LICENSE`
### groups
### depends
* All depends should be writed at new string, and brackets too:
  ```
  depends=(
    gcc-libs
    glib2
    glibc
  ...
  )
  ```
### makedepends
* Same as depends
### checkdepends
* Same as depends
### optdepends
* Same as depends
### provides
* Use for `-git` and `-bin` packages only but not for named packages:
  `provides=(${pkgname%-bin})` or `provides=(${pkgname%-git})`
### conflicts
* Use for `-git` and `-bin` packages only but not for named packages:
  `conflicts=(${pkgname%-bin})` or `conflicts=(${pkgname%-git})`
  Not need to list all other packages, just write the main name
### replaces
### backup
### options
Recommeneded: `options=(!debug)`. And for Rust packages also need add `!lto`
### install
### changelog
### source
* prefer `git+$url` over `git://` for taking advantage of TLS encryption
* `pkgrel` is for internal use of the PKGBUILD and must not be used in `source=()` or as part of pkgver
* use `source_$ARCH` for `$ARCH` specific sources
* use a common source folder, `SRCDEST=` in `/etc/makepkg.conf`
* add git submodules to `source=()`, it is a good packaging practice and make sources re-usable, especially with a common `SRCDEST`
* make downloaded sources non-conflicting and re-usable, i.e.

  for Github releases use just `source=($url/archive/v$pkgver/$pkgname-$pkgver.tar.gz)` or `source=($pkgname-$pkgver.tar.gz::$url/....)` for other hostings.

  for git repository find a common ground for source name

    + for git avoid `$pkgname-$pkgver::git+url/name.git` (waste as redownload whole source )

    + for git avoid `$pkgname::git+url/name.git` when `pkgname=$NAME-git` (i.e. textosaurus and textosaurus-git use the same source)

    + for git avoid `$pkgname::git+url/name.git` when `pkgname=name` (pointless since source is already called 'name')

    + for ease of use when git source have uppercase name `${pkgname%-git}::git+url/Name.git`

    + use `source=(${pkgname%-git}::git+$url.git`

### noextract
### validpgpkeys
### Integrity
* `b2sum` is the preferred hash check
### prepare()
* for packages `-bin` you can use additional hashsum check (if contributor of software provide it) in `prepare` function:

```
prepare(){
    md5sum -c $pkgname-$pkgver.md5
    #OR
    echo $(<*.md5) *.tar.gz | md5sum -c -
}
```
### build()
* [Examples](https://wiki.archlinux.org/title/Creating_packages#More_detailed_guidelines)
### check()
### package()
Example for installing files:
```
install -vDm755 usr/bin/${pkgname%-bin}{,-session} -t "$pkgdir"/usr/bin/
```
