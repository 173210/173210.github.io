---
layout: post
title:  "RPi上でのQt WebEngineの進展"
date:   2016-05-25 10:29:32 +0900
---
進展があった.

# VC4 DRM Driverが動いた
ドライバを提供している[anholt氏のリポジトリ](https://github.com/anholt/linux/)で
[issueを開いた](https://github.com/anholt/linux/issues/29)ところ,
曰く[Wikiで指定されているブランチ](https://github.com/anholt/linux/tree/vc4-kms-v3d)は私が使うべきものではないと言うことで,
氏が[Wikiを更新してくれた](https://secure.freedesktop.org/cgit/dri/commit/VC4.mdwn?id=978d02d0d92a264f2607150bafc61daf277264cf).

その変更によると, `linux-next`を使うべきと言うことだった. ただ, 少し怖いので`master`ではなく`stable`ブランチを使った.
すると, Wikiに書いてありながら使用されていなかった`I2C_BCM2835`が使用されるようになったり,
`vc4-kms-v3d`オーバーレイが不要になっていたりという変更が施されていた.
あとは簡単, `bcm2835_defconfig`を元に, 次の変更を加えたらあっさり動作した.

```
CMA=y
DMA_CMA=y
CMA_SIZE_MBYTES=128 # Qt WebEngineにはこれぐらい必要だった.
```

# Qt WebEngineは未だ動かず, 勘違いに気づく
さて, いざ起動させてみると, 画面は真っ白になって何も映らない. 以前と全く同じ症状だ.
いい加減ログもないことにはわからないと, 調べてみると`--log-level=0`でログを出せることがわかった.
で, ログを出すとこんな感じに…

```
[0101/000155:ERROR:gl_surface_qt.cpp(419)] eglCreatePbufferSurface failed with error 
[0101/000155:ERROR:command_buffer_proxy_impl.cc(182)] Failed to initialize command buffer service.
[0101/000155:ERROR:webgraphicscontext3d_command_buffer_impl.cc(210)] CommandBufferProxy::Initialize failed.
[0101/000155:ERROR:webgraphicscontext3d_command_buffer_impl.cc(229)] Failed to initialize command buffer.
```

どれどれとソースコードを見てみると, このような記述になっていた.

```C++
        LOG(ERROR) << "eglCreatePbufferSurface failed with error ", GetLastEGLErrorString();
```

おかしい. エラーメッセージがない.

ここで勘ぐってみると, OpenGLの関数を呼び出せていないということになる.
かくしてまた詰んだ.

更に, 私はとんでもない勘違いをしていたことに気づいた. ChromiumはDRMに依存していたが,
これは囮だった. `src/core/config/embedded_linux.gypi`には次のような記述がある.

```
{
  'target_defaults': {
    # patterns used to exclude chromium files from the build when we have a drop-in replacement
    'sources/': [
      # We are using gl_context_qt.cc instead.
      ['exclude', 'gl_context_ozone.cc$'],
    ],
  },
}
```

そうだ. OpenGLをQtのものと置き換えていた. つまり, QtのOpenGLのコンテキストは共有されているため,
DRMを苦労して導入する必要はなかったのだ. もっとも, X Window Systemを使って`embedded_linux`版ではなくDesktop版を使用するとなればDRMは必須になるが,
クロスコンパイルの場合では自動的に`embedded_linux`版をビルドすることになっていたりと,
これもまた困難がありそうである.

続く…
