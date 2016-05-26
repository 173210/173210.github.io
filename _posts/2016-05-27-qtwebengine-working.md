---
layout: post
title:  "RPiでQt WebEngineが動いた"
date:   2016-05-27 07:46:26 +0900
---
ついにだ, ついにQt WebEngineが動いた.

# `drm`バックエンドのスタブ
`drm`バックエンドの`eglCreatePbufferSurface`はスタブになっていた.

[https://cgit.freedesktop.org/mesa/mesa/commit/src/egl/drivers/dri2/platform_drm.c?id=bf20076bafcf0809529ae470fb12af5eae12b33d](https://cgit.freedesktop.org/mesa/mesa/commit/src/egl/drivers/dri2/platform_drm.c?id=bf20076bafcf0809529ae470fb12af5eae12b33d)

```
 .create_pbuffer_surface = dri2_fallback_create_pbuffer_surface,
```

[https://cgit.freedesktop.org/mesa/mesa/tree/src/egl/drivers/dri2/egl_dri2_fallbacks.h?id=bf20076bafcf0809529ae470fb12af5eae12b33d#n39](https://cgit.freedesktop.org/mesa/mesa/tree/src/egl/drivers/dri2/egl_dri2_fallbacks.h?id=bf20076bafcf0809529ae470fb12af5eae12b33d#n39)

```
static inline _EGLSurface *
dri2_fallback_create_pbuffer_surface(_EGLDriver *drv, _EGLDisplay *disp,
                                     _EGLConfig *conf,
                                     const EGLint *attrib_list)
{
   return NULL;
}
```

こうして, `drm`バックエンドでは動作しないことが証明されてしまった…

# X11
次にX11を試した. しかしこれもまたうまく動かない. Serverの`DRI2` extensionが動作していないらしかったが,
これを直すよりもQt Waylandを移植してしまったほうが良いのではと迂闊なことを考える.

# Wayland
しかしこれがあっさり動いた. 今までの苦労は何だったのか. 最初からVC4 DRM Driverと
Waylandを使えばよかったのだ…

# 終わった
解決策を見つけたので, フォーラムやらGitHubやらに投稿した質問を閉じて,
今こうして記事を書いているところである. また, ここまでにいくつかバグを見つけるなどしたので,
パッチを各方面に送らなくてはならない.

思うに, 初代Raspberry Piのような貧弱な端末でLinuxを動かそうなどと考える人はいないのだ.
だから[GCCはぶっ壊れた](https://twitter.com/173210/status/734871378415915011).
悲しいことだ. うちの大学でdevelopmentについて講義している教授がいるが,
曰く社会はものを長持ちさせ, 価格を下げ, 人間の負担を減らす方向ではなく,
無用な性能向上を行い, 古いものは切り捨てる傾向にあるという. 全くもってそのとおりだ.
Raspberry Piの元々の目的であろうところを考えれば, 同じハードウェアを安く売り,
その上でのソフトウェアを発展させたほうが良いに決まっているのに.
性能がほしければスマホでもPCでも買う.

とにかく, 終わったのだ.
