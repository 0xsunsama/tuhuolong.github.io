---
layout: post
title: "android点9图architecture"
date: 2016-07-12 19:19:00 
comments: true
categories: []
tags: []
description: "android点9图architecture"
keywords: 
---


 
  
   编译时aapt去掉黑圈, 并将信息写入一个png chunk (npTc chunk + bitmap chunk)
  
  
   
  
  
   加载Bitmap时会取出Chunk
  
  Bitmp {
    private byte[] mBuffer;

    private final long mNativePtr;

    private byte[] mNinePatchChunk;
}
  
   
    
     创建NinePatchDrawable
    
   
  
  public NinePatchDrawable(Resources res, Bitmap bitmap, byte[] chunk,
            Rect padding, Rect opticalInsets, String srcName){
}
  
   
    点9加载库(解析点9Chunk,  可解析原始xxx.9.png图)
    
     
      https://github.com/Anatolii/NinePatchChunk
     
     
      
     
    
   
  
  /**
 * This chunk specifies how to split an image into segments for
 * scaling.
 *
 * There are J horizontal and K vertical segments.  These segments divide
 * the image into J*K regions as follows (where J=4 and K=3):
 *
 *      F0   S0    F1     S1
 *   +-----+----+------+-------+
 * S2|  0  |  1 |  2   |   3   |
 *   +-----+----+------+-------+
 *   |     |    |      |       |
 *   |     |    |      |       |
 * F2|  4  |  5 |  6   |   7   |
 *   |     |    |      |       |
 *   |     |    |      |       |
 *   +-----+----+------+-------+
 * S3|  8  |  9 |  10  |   11  |
 *   +-----+----+------+-------+
 *
 * Each horizontal and vertical segment is considered to by either
 * stretchable (marked by the Sx labels) or fixed (marked by the Fy
 * labels), in the horizontal or vertical axis, respectively. In the
 * above example, the first is horizontal segment (F0) is fixed, the
 * next is stretchable and then they continue to alternate. Note that
 * the segment list for each axis can begin or end with a stretchable
 * or fixed segment.
 *
 * The relative sizes of the stretchy segments indicates the relative
 * amount of stretchiness of the regions bordered by the segments.  For
 * example, regions 3, 7 and 11 above will take up more horizontal space
 * than regions 1, 5 and 9 since the horizontal segment associated with
 * the first set of regions is larger than the other set of regions.  The
 * ratios of the amount of horizontal (or vertical) space taken by any
 * two stretchable slices is exactly the ratio of their corresponding
 * segment lengths.
 *
 * xDivs and yDivs are arrays of horizontal and vertical pixel
 * indices.  The first pair of Divs (in either array) indicate the
 * starting and ending points of the first stretchable segment in that
 * axis. The next pair specifies the next stretchable segment, etc. So
 * in the above example xDiv[0] and xDiv[1] specify the horizontal
 * coordinates for the regions labeled 1, 5 and 9.  xDiv[2] and
 * xDiv[3] specify the coordinates for regions 3, 7 and 11. Note that
 * the leftmost slices always start at x=0 and the rightmost slices
 * always end at the end of the image. So, for example, the regions 0,
 * 4 and 8 (which are fixed along the X axis) start at x value 0 and
 * go to xDiv[0] and slices 2, 6 and 10 start at xDiv[1] and end at
 * xDiv[2].
 *
 * The colors array contains hints for each of the regions. They are
 * ordered according left-to-right and top-to-bottom as indicated above.
 * For each segment that is a solid color the array entry will contain
 * that color value; otherwise it will contain NO_COLOR. Segments that
 * are completely transparent will always have the value TRANSPARENT_COLOR.
 *
 * The PNG chunk type is "npTc".
 */
  
   
    Android 9patch 图片解析堆溢出漏洞分析
   
  
  
   
    Android动态布局入门及NinePatchChunk解密
   
  
  
   
    NinePatchChunk Library
   
  
 
 
  $(function () {
                $('pre.prettyprint code').each(function () {
                    var lines = $(this).text().split('\n').length;
                    var $numbering = $('').addClass('pre-numbering').hide();
                    $(this).addClass('has-numbering').parent().append($numbering);
                    for (i = 1; i ').text(i));
                    };
                    $numbering.fadeIn(1700);
                });
            });
 


