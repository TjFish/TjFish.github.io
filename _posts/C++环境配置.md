## linux上安装ffmpeg

```shell
wget https://ffmpeg.org/releases/ffmpeg-4.2.2.tar.bz2
tar xvf ffmpeg-4.2.2.tar.bz2

cd ffmpeg-4.2.2

./configure --disable-shared --enable-static --disable-asm --enable-debug

make

make install
```

## linux上安装Crypto++

```shell
wget https://www.cryptopp.com/cryptopp860.zip
unzip cryptopp860.zip -d cryptopp860
cd cryptopp860
make
make install
```

