# Maintainer: YOUR NAME <you@example.com>
pkgname=matuwall-shellfree
pkgver=0.1.6
pkgrel=1
pkgdesc="Minimal GTK4+libadwaita wallpaper switcher for Wayland. shellfree fork: instant swaybg wallpaper swap, 2% click-away scrim, largest-monitor scrim sizing"
arch=('x86_64')
url="https://github.com/naurissteins/Matuwall"
license=('GPL-3.0-or-later')
depends=('python' 'python-gobject' 'gtk4' 'libadwaita' 'gtk4-layer-shell')
makedepends=('python-build' 'python-installer' 'python-wheel' 'python-setuptools')
optdepends=(
  'matugen: generate colorschemes from the applied wallpaper'
  'vips: downscale giant wallpapers inside the matuwall-swaybg wrapper'
)
conflicts=('matuwall' 'matuwall-bin' 'matuwall-git')
source=("matuwall-$pkgver.tar.gz::https://github.com/naurissteins/Matuwall/archive/refs/tags/$pkgver.tar.gz"
        'shellfree.patch')
sha256sums=('89e7b577d23bc77e87de5c06e42bb979463256750b06f2cba1672b7b6f8b6046'
            'fc5775eb4e20ea2f03298b37028a48fb535ad0de0430e77268c5df8f5a011264')

prepare() {
  cd "Matuwall-$pkgver"
  patch -p1 -i ../shellfree.patch
}

build() {
  cd "Matuwall-$pkgver"
  python -m build --wheel --no-isolation
}

package() {
  cd "Matuwall-$pkgver"
  python -m installer --destdir="$pkgdir" dist/*.whl
  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}