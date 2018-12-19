# Guide to using Phan/Phan

### Installing phpize

```
cd /usr/src
wget -O mypackage.tgz http://downloads.sourceforge.net/project/saprfc/saprfc/1.4.1/saprfc-1.4.1.tar.gz?r=http%3A%2F%2Fsaprfc.sourceforge.net%2Fhome.php&ts=1349204180&use_mirror=heanet
tar xvfz mypackage.tgz
cd saprfc*
phpize
./configure
make
make install
```
