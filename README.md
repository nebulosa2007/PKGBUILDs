[My PKGBUILD list](https://aur.archlinux.org/packages?SeB=m&K=Nebulosa)

[Main guideline for PKGBUILD](https://wiki.archlinux.org/title/PKGBUILD)

### Other packaging guidelines & tips

* for packages `-bin` you can use additional sumhash check (if contributor of software provide it) in `prepare` function:

```
prepare(){
    md5sum -c $pkgname-$pkgver.md5
    #OR
    echo $(<*.md5) *.tar.gz | md5sum -c -
}
```

* prefer `git+$url` over `git://` for taking advantage of TLS encryption

* `b2sum` is the preferred hash check

* cut 'v' and other prefixes from `pkgver`

* don't use custom variables when not needed (like use `${pkgname%-git}`)

* `pkgrel` is for internal use of the PKGBUILD and must not be used in `source=()` or as part of pkgver

* quoting in `arch=()` `license=()` `depends=()` `makedepends=()` `depends=()` is pointless and a personal choice, except when needed:
`optdepends=('gst-libav: additional codecs') license=('custom:WTFPL') license=('custom:corp EULA')`. But always use double quotting for `"$pkgdir"` and `"$pkgscr"`

* remember to quote variables for avoiding failures due to whitespaces in paths

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

* install not common licenses in `"$pkgdir"/usr/share/licenses/$pkgname`, not mandatory to install common licenses; see 'licenses' package. Use porper identifier from [SPDX list](https://spdx.org/licenses/preview/index.html). For `-git` `-bin` `-whatever` packages use `install -Dm644 LICENSE -t "$pkgdir"/usr/share/licenses/${pkgname%-bin}/`
