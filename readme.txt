

# clone OpenWrt repository to local folder called source 
git clone https://git.openwrt.org/openwrt/openwrt.git source

cd source 
git checkout v17.01.2
make distclean

./scripts/feeds update -a
./scripts/feeds install -a

make menuconfig

make toolchain/install



