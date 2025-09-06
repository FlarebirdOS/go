pkgname=go
pkgver=1.25.1
pkgrel=2
pkgdesc="Core compiler tools for the Go programming language"
arch=('x86_64')
url="https://golang.org/"
license=('BSD-3-Clause')
makedepends=('go')
options=('!strip')
source=(https://go.dev/dl/${pkgname}${pkgver}.src.tar.gz)
sha256sums=(d010c109cee94d80efe681eab46bdea491ac906bf46583c32e9f0dbb0bd1a594)

prepare() {
    cd ${pkgname}

    # It leaves some traces...
    rm -vf runtime/{os_plan9.go.orig,os_windows.go.orig,proc.go.orig,vgetrandom_linux.go.orig}
}

build() {
    cd ${pkgname}/src

    export GOARCH=amd64
    # make sure we're building for the right x86-64 version
    export GOAMD64=v1
    export GOROOT_FINAL=/usr/lib64/go
    export GOROOT_BOOTSTRAP=${srcdir}/goroot/go

    # Disable dwarf5 until debugedit catches up
    export GOEXPERIMENT=nodwarf5

    ./make.bash -v
}

package() {
    cd ${pkgname}

    install -vdm755                                  \
        ${pkgdir}/usr/bin                            \
        ${pkgdir}/usr/lib64/go                       \
        ${pkgdir}/usr/share/doc/${pkgname}-${pkgver} \
        ${pkgdir}/usr/lib64/go/pkg/linux_amd64_{dynlink,race}

    cp -a bin pkg src lib misc api test ${pkgdir}/usr/lib64/go
    # We can't strip all binaries and libraries,
    # as that also strips some testdata directories and breaks the tests.
    # Just strip the packaged binaries as a compromise.
    strip $STRIP_BINARIES ${pkgdir}/usr/lib64/go{/bin/*,/pkg/tool/*/*}

    cp -r doc/* ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}

    ln -sf /usr/lib64/go/bin/go ${pkgdir}/usr/bin/go
    ln -sf /usr/lib64/go/bin/gofmt ${pkgdir}/usr/bin/gofmt
    ln -sf /usr/share/doc/${pkgname}-${pkgver} ${pkgdir}/usr/lib64/go/doc

    install -Dm644 VERSION ${pkgdir}/usr/lib64/go/VERSION

    rm -rf ${pkgdir}/usr/lib64/go/pkg/bootstrap

    # TODO: Figure out if really needed
    rm -rf ${pkgdir}/usr/lib64/go/pkg/obj/go-build

    # https://github.com/golang/go/issues/57179
    install -Dm644 go.env ${pkgdir}/usr/lib64/go/go.env
}
