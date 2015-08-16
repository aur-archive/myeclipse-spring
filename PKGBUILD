# Maintainer: Firef0x <firefgx { at } gmail { dot } com>
# Contributor: Firef0x <firefgx { at } gmail { dot } com>

pkgname=myeclipse-spring
_realname=myeclipseforspring
_shortcutname=MyEclipse_for_Spring
pkgver=11.0.2
_internal_pkgver=2013-SR2
_datever=11.0.1.me201309011543
pkgrel=2
pkgdesc="A set of integrated plugins to the eclipse platform, built on the eclipse classic distribution which includes basic java development tools"
arch=('i686' 'x86_64')
url="http://www.myeclipseide.com/"
license=('custom:GEULA')
options=('!strip' 'emptydirs')
depends=('gtk2'
		     'unzip'
		     'webkitgtk2'
		     'libxtst')
optdepends=('nss>=3.12.2: for XULRunner 1.9.0.7 in MyEclipse Visual HTML Editor'
            'gconf: for XULRunner 1.9.0.7 in MyEclipse Visual HTML Editor'
            'libgnome-keyring: for XULRunner 1.9.0.7 in MyEclipse Visual HTML Editor')
conflicts=('xulrunner')
install=${pkgname}.install
source=("http://downloads.myeclipseide.com/downloads/products/eworkbench/2013/installers/${pkgname}-${_internal_pkgver}-offline-installer-linux.run"
		"${_realname}.sh"
		"response")
md5sums=('7c50fe440a7801fa0f33bf70c730178c'
         'b38e37af50376b1a3462cb0801289bb2'
         '9433ef5fc08bc80966eda4267f13af05')
# If you cannot download myeclipse, try DLAGENTS
#DLAGENTS=("http::/usr/bin/curl -A 'Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1)' -fLC - --retry 3 --retry-delay 3 -O --header 'Cookie: gpw_e24=h'")
# If you don't want to compress the package at all, try PKGEXT
#PKGEXT='.pkg.tar'
_rmbinary=1
_lndropins=1

prepare() {
	echo -e "\033[0;31m IMPORTANT"
	echo -e "\033[0;0m  Please read comments by Firef0x in \033[0;32m https://aur.archlinux.org/packages/myeclipse-spring \033[0;0m before you make your own package."
}

build() {

  # Judge Arch & Replace Unattended Configuration
  if [ "$CARCH" = "x86_64" ]; then
  	sed -i "s/x86/x86_64/" response
  fi

  # Since this file will influence default myeclipse install folder, it should be renamed.
  if [ -w ~/.deliverycenter.installs ]; then
	  mv "~/.deliverycenter.installs" "~/.deliverycenter.installs.original"
  fi

  # Replace by actual path
  sed -i "s|INSTALLATION_FOLDER|${srcdir}/usr/share/${pkgname}|" response

  # Prepare to run install
  cd "${srcdir}"
  chmod a+x "${pkgname}-${_internal_pkgver}-offline-installer-linux.run"

  #Install MyEclipse
  sh "./${pkgname}-${_internal_pkgver}-offline-installer-linux.run" --unattended ${srcdir}/response
  if [ $? -ne 0 ]; then
	  msg2 "Please make sure you have at least 1.3GB free space in /tmp and have turned on swap, then try it again."
	  return 1
  fi

  # Restore this file to origin
  rm -f ~/.deliverycenter.installs
  if [ -w ~/.deliverycenter.installs.original ]; then
	  mv "~/.deliverycenter.installs.original" "~/.deliverycenter.installs"
  fi

  # Remove Desktop Shortcuts
  mv "~/.local/share/applications/${_shortcutname}.desktop" "${srcdir}/${_shortcutname}"
  mv "~/.local/share/applications/Uninstall_${_shortcutname}.desktop" "${srcdir}/${_shortcutname}"

  # Remove install log files & temp files
  rm -f /tmp/unattended.log
  rm -f /tmp/pulse-one-*.log
  rm -f /tmp/MyEclipse*-support-*.zip

}

package() {

  cd "${srcdir}/usr/share/${pkgname}"

  # Remove Shipped-with Java environment and drop-in plugins' folder
  if [ ${_rmbinary} -eq 1 ]; then
	  rm -rf binary
	  sed -i "/^-vm$/{N;d}" \
		${_realname}.ini
		depends+=('java-environment')
  fi

  if [ ${_lndropins} -eq 1 ]; then
	  rm -rf dropins
  fi

  # Patch to delete default vm location
  sed -i -e "/^-configuration$/{N;d}" \
    -e "s|${srcdir}/usr/share/${pkgname}|/usr/share/${pkgname}|g" \
    -e "s/^-Xmx512m$/-Xmx640m/" \
	${_realname}.ini

  # Patch for correct install location
  sed -i "s|${srcdir}/usr/share/${pkgname}|/usr/share/${pkgname}|" \
    configuration/config.ini

  sed -i "s|${srcdir}/usr/share/${pkgname}|/usr/share/${pkgname}|" \
    Uninstaller/configuration/config.ini

  sed -i "s|${srcdir}/usr/share/${pkgname}|/usr/share/${pkgname}|" \
    configuration/com.genuitec.pulse.client.delivery.install.meta/shortcuts.properties

  sed -i "s|${srcdir}/usr/share/${pkgname}|/usr/share/${pkgname}|" \
	p2/org.eclipse.equinox.p2.engine/profileRegistry/com.genuitec.delivery.package.profile.me-${pkgname/myeclipse-}-11.profile/*.profile

  # Python2 fix
  sed -i 's|#!/usr/bin/python|#!/usr/bin/python2|' \
	plugins/org.apache.ant_1.8.3.v201301120609/bin/runant.py

  # Patch desktop shortcuts
  sed -i -e "s/^Exec=.*$/Exec=${_realname}/" \
    -e "s|${srcdir}/usr/share/${pkgname}|/usr/share/${pkgname}|" \
    ${srcdir}/${_shortcutname}.desktop

  sed -i "s|${srcdir}/usr/share/${pkgname}|/usr/share/${pkgname}|" \
    ${srcdir}/Uninstall_${_shortcutname}.desktop

  # Install icons and desktop shortcuts
  install -d -m755 "${pkgdir}/usr/share/applications"
  install -m644 "${srcdir}/${_shortcutname}.desktop" "${pkgdir}/usr/share/applications/${_shortcutname}.desktop"
  install -m644 "${srcdir}/Uninstall_${_shortcutname}.desktop" "${pkgdir}/usr/share/applications/Uninstall_${_shortcutname}.desktop"

  # Install license file
  install -d -m755 "${pkgdir}/usr/share/licenses/${pkgname}"
  install -m644 "plugins/com.genuitec.myeclipse.product_${_datever}/LICENSES/LICENSE_GENUITEC.txt" \
    "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"

  # Patch bin file
  sed -i -e "s|INSTALLATION_FOLDER|/usr/share/${pkgname}|" \
    -e "s/EXECUTABLE/${_realname}/" ${srcdir}/${_realname}.sh

  # Install bin file
  install -d -m755 "${pkgdir}/usr/bin"
  install -m755 "${srcdir}/${_realname}.sh" "${pkgdir}/usr/bin/${_realname}"

  # Move everything
  install -d -m755 "${pkgdir}/usr/share/${pkgname}"
  mv .eclipseproduct "${pkgdir}/usr/share/${pkgname}/"
  mv * "${pkgdir}/usr/share/${pkgname}/"

  # Link drop-ins folder to eclipse's drop-ins folder
  if [ ${_lndropins} -eq 1 ]; then
	  install -d -m755 "${pkgdir}/usr/share/eclipse/dropins"
	  cd "${pkgdir}/usr/share/${pkgname}"
	  ln -s "/usr/share/eclipse/dropins" "${pkgdir}/usr/share/${pkgname}/dropins"
  fi

}
# vim:set ts=2 sw=2 et:
