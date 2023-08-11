# RPM build and repo

## Build package
```
rpm -i $pgk.srpm
rpmbuild -ba --clean SPECS/$pkg.spec
```

We skip the step of signing the package for now.

## Create repo
```bash
createrepo .
```

# DEB build and repo

## Build package
```bash
dpkg-source -x $pkg.dsc
cd $pkg
mk-build-deps --install debian/control
debuild -b -uc -us
```

## Create repo
We create a very basic Debian repo, not using a pool/ directory and Release files with GPG signatures.

```bash
ARCH="`dpkg --print-architecture`"

mkdir -p dists/main/local/binary-$ARCH
mkdir -p dists/main/local/source
```

Move binary packages to `binary-$ARCH` and sources to `source`.

```
dpkg-scanpackages dists/main/local/binary-$ARCH /dev/null \
  | gzip -9 \
  > dists/main/local/binary-$ARCH/Packages.gz

dpkg-scansources dists/main/local/source \
  | gzip -9 \
  > dists/main/local/source/Sources.gz
```
