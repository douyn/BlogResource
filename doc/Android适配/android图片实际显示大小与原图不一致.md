## Android图片实际显示大小与原图大小不一致

#### 问题描述
UI原型中图片的尺寸为64x64像素，设备density为300，图片所在的文件夹是xhdpi。本来以为图片显示是正常的，但是通过对比发现图片比原型上要小一点点，然后在代码中打印日志发现图片的实际大小为60像素。

#### 问题分析
##### android系统适配原则
![Android系统适配原则](https://img-blog.csdn.net/20170105105400694?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbXlvdW5nbWVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

###### Android图片的放大和缩小
Android为了适配各种屏幕，会依据当前设备的dpi对图片进行缩放。

通常情况下：
比如当前设备dpi为160，那么它对应的匹配目录为mdpi。如果当前图片是放在mdpi中则匹配正确，如果放在其他目录则匹配错误，匹配错误会放大或者缩小图片。比如放在hdpi会被放大1.5倍，放在ldpi会被缩放0.75倍。

但是对于很多设备,设备density并非刚好等于通用密度对应的dpi，所以一般情况下我们都是根据缩放公式来计算图片的实际显示大小

	scale = 设备dpi / 图片所在目录对应的最大dpi
    
比如一张64x64的图片，放在drawable-dpi中， 当前设备dpi为300.那么这张图片的实际显示大小

	realwidth = 64xscale=64x300/240=80px
    
如果同图片同设备，图片放在drawable-xhdpi中，那么这张图片的实际显示大小

	realwidth = 64xscale=64x300/320=60px
#### 问题解决
所以Android的图片适配机制是导致图片与切图不一致的根本原因，如果要解决这个问题
1. 重新调整切图
2. 调整设备dpi
3. 设置imageview大小为固定

#### 参考 
[Android不同分辨率图片实际显示大小的计算](http://blog.csdn.net/czhpxl007/article/details/48574013)

[玩转Android drawable图片适配](https://blog.csdn.net/myoungmeng/article/details/54090891)