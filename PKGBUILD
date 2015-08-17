# Contributor Yarema aka Knedlyk <yupadmin@gmail.com>
pkgname=vdr-sc-hg
pkgver=574
pkgrel=1
arch=(i686 x86_64)
pkgdesc="An sc plugin for VDR"
url="http://85.17.209.13:6100/"
license="GPL"
conflicts=('vdr-plugin-sc')
groups=('vdr')
depends=('vdr>=1.6.0' 'mercurial')
source=('01_Makefile-MAXCAID.dpatch' 'commands.vdr-sc.conf' 'plugin.sc.conf' 'reload-softcam.key' 'vdr-plugin-sc.groups')
backup=()
md5sums=('ab77234b0286182ff910dbb5e09c24cf'
         '1f190101eb8ec7d46a6d9d3282f33317'
	 '0a1e85a99005696b3bd6507320ef4cad'
	 '57edddf2161a255cc2acea521d3d3b6f'
	 'c0d6e9f7c568c50bd13522dba67d1aa8')
				    
_hgroot=http://85.17.209.13:6100/
_hgname=sc
_hgrevision=560


build() {

    cd $srcdir
    mkdir -p ${pkgdir}/usr/lib/vdr/plugins/
    mkdir -p ${pkgdir}/etc/vdr/plugins/
    mkdir -p ${pkgdir}/usr/share/vdr/groups.d/
    mkdir -p ${pkgdir}/usr/lib/vdr-plugin-sc/
    mkdir -p ${pkgdir}/usr/share/vdr/command-hooks/

    msg "Connecting to Mercurial server...."
    if [ -d ${srcdir}/${_hgname} ]; then
        cd ${_hgname}
        hg pull -u
        msg "The local files are updated."
    else
	hg clone ${_hgroot}${_hgname} ${_hgname}  || return 1
	cd ${_hgname}
#	hg update ${_hgrevision} || return 1
    fi
    
    rm -rf ${srcdir}/${_hgname}-build
    cp -r ${srcdir}/${_hgname} ${srcdir}/${_hgname}-build
    cd ${srcdir}/${_hgname}-build
    
    msg "Mercurial clone done or server timeout"
    msg "Starting make..."


#    cd $srcdir/${_hgname}
# setting MAXCAID, not needed anymore (??)    
    patch -p2 < ${startdir}/01_Makefile-MAXCAID.dpatch || exit 1

# changing optimization option O3->O2
    sed -e  's/-O3/-O2/' -i  $srcdir/${_hgname}-build/Makefile

						

#changing ECM message size for long messages
#    sed -e  's/#define CWS_NETMSGSIZE 272/#define CWS_NETMSGSIZE 540/' -i  $srcdir/$_hgname/systems/cardclient/newcamd.c

if [ "$CARCH" = "i686" ]; then
    make VDRDIR=/usr INCLUDES=-I/usr/include/vdr \
    LIBDIR=${pkgdir}/usr/lib/vdr \
    LOCALEDIR=${pkgdir}/usr/share/locale all || return 1
else
    sed -i $srcdir/${_hgname}-build/Makefile -e "s:CPUOPT     ?= pentium:CPUOPT     ?= x86-64:"
    sed -i $srcdir/${_hgname}-build/Makefile -e "s:PARALLEL   ?= PARALLEL_32_INT:PARALLEL   ?= PARALLEL_128_SSE:"
#    sed -i $srcdir/${_hgname}-build/FFdecsa/Makefile -e "s:-march=pentium:-march=x86-64:"
    make CXXFLAGS="-march=x86-64 -O2 -g -fPIC -Wall -Woverloaded-virtual" \
    VDRDIR=/usr INCLUDES=-I/usr/include/vdr \
    LIBDIR=${pkgdir}/usr/lib/vdr \
    LOCALEDIR=${pkgdir}/usr/share/locale all || return 1
fi

    install -D libvdr-sc.so.* ${pkgdir}/usr/lib/vdr/plugins/
    install -D systems/*/libsc-*.so.* 	${pkgdir}/usr/lib/vdr/plugins/
    install -D ../plugin.sc.conf 	${pkgdir}/etc/vdr/plugins/
    install -D ../plugin.sc.conf	${pkgdir}/etc/vdr/plugins/
    install -D ../vdr-plugin-sc.groups	${pkgdir}/usr/share/vdr/groups.d/
    install -D ../reload-softcam.key	${pkgdir}/usr/lib/vdr-plugin-sc/
    install -D ../commands.vdr-sc.conf	${pkgdir}/usr/share/vdr/command-hooks/
#    install -D $startdir/pkg/usr/src/VDR/locale

}
