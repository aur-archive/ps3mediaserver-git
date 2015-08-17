# Maintainer: Kwrazi <kwrazi@gmail.com>

_pkgname=ps3mediaserver
_shortname=pms

pkgname=${_pkgname}-git
pkgver=20140122
pkgrel=1
pkgdesc="PS3 Media Server (PMS) is a cross-platform DLNA-compliant UPnP Media Server."
arch=('any')
url="http://www.ps3mediaserver.org/"
license=('GPL2')
depends=('mplayer' 'tsmuxer' 'ffmpeg' 'mencoder' 'libmediainfo' 'java-environment')
optdepends=("vlc: Internet video/audio support", 
            "dcraw: decodes any raw image from any digital camera")
makedepends=('git' 'maven')
provides=("${_pkgname}")
conflicts=('pms','pms-svn')
replaces=()
backup=()
install=
# pms.service and pms.desktop borrowed from pms package by eworm
source=('git+https://github.com/ps3mediaserver/ps3mediaserver.git'
        'pms.service'
        'pms.desktop')
md5sums=('SKIP'
         'c25573afe77b4c390712507ceee6b02d'
         '8b89300252848fbc86336421766e8988')

pkgver() {
  cd ${srcdir}/${_pkgname}
  git log -1 --format='%cd' --date=short | tr -d -- '-'
}

build() {
  cd "$srcdir/${_pkgname}"
  mvn com.savage7.maven.plugins:maven-external-dependency-plugin:resolve-external
  mvn com.savage7.maven.plugins:maven-external-dependency-plugin:install-external
  mvn package
}

package() {
  cd "$srcdir/${_pkgname}"
  
  install -d -m 0755 ${pkgdir}/opt/${_shortname}
  install -d -m 0775 -g users ${pkgdir}/opt/${_shortname}/database
  install -d -m 0755 ${pkgdir}/usr/bin
  
  # Replicate the official binary package
  install -D -m 0644 CHANGELOG.txt ${pkgdir}/opt/${_shortname}/
  install -D -m 0644 INSTALL.txt ${pkgdir}/opt/${_shortname}/
  install -D -m 0644 LICENSE.txt ${pkgdir}/opt/${_shortname}/
  install -D -m 0644 README.md ${pkgdir}/opt/${_shortname}/
  install -D -m 0644 target/pms.jar ${pkgdir}/opt/${_shortname}/
  install -D -m 0755 src/main/external-resources/PMS.sh ${pkgdir}/opt/${_shortname}/
  install -D -m 0664 src/main/external-resources/PMS.conf ${pkgdir}/opt/${_shortname}/
  install -D -m 0644 src/main/external-resources/WEB.conf ${pkgdir}/opt/${_shortname}/
  install -D -m 0644 src/main/external-resources/logback.xml ${pkgdir}/opt/${_shortname}/
  install -D -m 0644 src/main/external-resources/logback.headless.xml ${pkgdir}/opt/${_shortname}/

  # Install subdirectories
  cd "$srcdir/${_pkgname}/src/main/external-resources/"
  find plugins -type d -print0 | xargs -0 -n1 -r -Izzz -- install -d -m 0755 ${pkgdir}/opt/${_shortname}/zzz
  find plugins -type f -print0 | xargs -0 -n1 -r -Izzz -- install -D -m 0644 zzz ${pkgdir}/opt/${_shortname}/zzz
  find renderers -type d -print0 | xargs -0 -n1 -r -Izzz -- install -d -m 0755 ${pkgdir}/opt/${_shortname}/zzz
  find renderers -type f -print0 | xargs -0 -n1 -r -Izzz -- install -D -m 0644 zzz ${pkgdir}/opt/${_shortname}/zzz
  find documentation -type d -print0 | xargs -0 -n1 -r -Izzz -- install -d -m 0755 ${pkgdir}/opt/${_shortname}/zzz
  find documentation -type f -print0 | xargs -0 -n1 -r -Izzz -- install -D -m 0644 zzz ${pkgdir}/opt/${_shortname}/zzz
  cd "$srcdir/${_pkgname}"

  # link tsMuxeR binary
  ln -s /usr/bin/tsMuxeR ${pkgdir}/opt/${_shortname}/tsMuxeR
  # link executable script
  ln -s /opt/${_shortname}/PMS.sh ${pkgdir}/usr/bin/ps3mediaserver
  # systemd service
  install -D -m 0644 ${srcdir}/${_shortname}.service ${pkgdir}/usr/lib/systemd/system/${_shortname}@.service
  # desktop configuration
  install -D -m 0644 ${srcdir}/${_shortname}.desktop ${pkgdir}/usr/share/applications/${_shortname}.desktop
  install -D -m 0644 target/classes/resources/images/clients/ps3.png ${pkgdir}/usr/share/pixmaps/pms.png
}

# vim:set ts=2 sw=2 et:
