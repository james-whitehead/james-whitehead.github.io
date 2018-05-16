---
published: true
layout: post
title: Rough.js
excerpt: A graphics library for hand-drawn images
comments: true
---

<canvas id="canvas" width="800" height="600"></canvas>
<script>
    const rc = rough.canvas(document.getElementById('canvas'));
    // heart
    rc.path("M18.028 33.45l-.394-.404c-.821-.843-11.531-11.81-12.972-13.261C1.525 16.627 0 13.439 0 10.041 0 4.504 4.412 0 9.834 0c3.317 0 6.387 1.702 8.199 4.493C19.887 1.676 22.935 0 26.227 0c5.422 0 9.834 4.504 9.834 10.041 0 3.398-1.523 6.586-4.659 9.745-1.167 1.174-8.43 8.607-11.531 11.781l-1.843 1.883zM9.834 1.103", {
        stroke: '#Ef233C',
        fill: '#C0EBF9',
        hachureGap: 3
    });
</script>