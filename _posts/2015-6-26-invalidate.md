---
layout: post
title: Parent view invalidate
---

如果启动display list，parentview 调用invalidate，子view是不会是去调用onDraw的，因为直接调用displaylist了，如果parentview认为子view应该redraw，调用childview.invalidate。
