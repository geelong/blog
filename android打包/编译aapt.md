源码根目录

source build/envsetup.sh

lunch sdk-eng

make aapt

生成地址(如果已经有相同的文件，是不会再生成的)

./out/host/darwin-x86/bin/aapt