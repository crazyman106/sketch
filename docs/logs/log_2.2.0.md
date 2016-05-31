磁盘缓存:
>* 升级LruDiskCache，内部采用DiskLruCache实现
>* DiskCache接口增加了edit(String)方法，去掉了generateCacheFile(String)、
applyForSpace(long)、setCacheDir(File)、setReserveSize(int)、getReserveSize()、
setMaxSize(int)、saveBitmap(Bitmap, String)方法
>* 旧的缓存文件会自动删除
>* `LruDiskCache兼容多进程，多进程下会采用不同的disk缓存目录，防止多进程持有同一目录造成目录被锁不能使用的问题`

下载：
>* 下载进度回调方式改为每秒钟一次（之前是每10%一次）
>* 重构ImageDownloader，新增可设置User-Agent、readTimeout和批量添加header

加载：
>* 默认maxSize由屏幕宽高的1.5倍改为0.75倍，这样可以大幅减少大图的内存占用，但对图片的清晰度影响却较小
>* 默认inSampleSize计算规则优化，targetSize默认放大1.25倍，目的是让原始尺寸跟targetSize较为接近的图片不被缩小直接显示，这样显示效果会比较好
>* 新增ImagePreprocessor可以处理一些特殊的本地文件，然后提取出它们的当中包含的图片，这样Sketch就可以直接显示这些特殊文件中包含的图片了
>* LoadOptions支持设置BitmapConfig，你可以单独定制某张图片的配置
>* LoadOptions支持设置inPreferQualityOverSpeed（也可在Configuration中统一配置），你可以在解码速度和图片质量上自由选择

请求：
>* 支持file:///****.jpg
>* Download也支持设置requestLevel
>* 去掉了DisplayHelper上的listener和progressListener设置，你只能通过SketchImageView来设置listener和progressListener了
>* 重构\***Request的实现，简化并统一逻辑处理
>* \***Helper.options(Enum)改为optionsByName(Enum)
>* 调低分发线程的优先级，这样能减少display在主线程的耗时，提高页面的流畅度
>* 支持在debug模式下输出display在主线程部分的耗时
>* 本地任务支持多线程，加快处理速度
>* `修复在Display的commit阶段显示失败时如果没有配置相应的图片就不设置Drawable而导致页面上显示的还是上一个图片的BUG`

处理：
>* 新增旋转图片处理器RotateImageProcessor
>* RoundedCornerImageProcessor扩展构造函数，支持定义每个角的大小

解码：
>* 支持在debug模式下输出解码耗时
>* LoadOptions支持设置inPreferQualityOverSpeed，你可以在解码速度和图片质量上自由选择
>* 解码支持设置Options.inPreferQualityOverSpeed（通过LoadOptions配置，也可在Configuration中统一配置），你可以在解码速度和图片质量上自由选择

GIF：
>* 由于显示GIF的场景较少，所以默认不再解码GIF图，在需要解码的地方你可以主动调用 ***Options.setDecodeGifImage(true)或
***Helper.decodeGifImage()开启
>* 删除\***Helper.disableDecodeGif()方法替换为\***Helper.decodeGifImage()

SketchImageView：
>* SketchImageView.setDisplayOptions(Enum)改名为setOptionsByName(Enum)
>* `修复使用setImageResource等方法设置图片后在列表中一滑动图片就没了的BUG`

其它：
>* 去掉了一些多余的接口设计，例如HelperFactory、ImageSizeCalculator、RequestFactory、
ResizeCalculator、ImageSize、Request、DisplayHelper、LoadHelper、DownloadHelper
>* 所有failure改名为failed
>* apk文件图标的磁盘缓存KEY加上了文件的最后修改时间，这么做是为了避免包路径一样，但是内容已经发生了变化的时候不能及时刷新缓存图标
>* 原生支持通过包名和版本号显示已安装APP的图标
>* 源码兼容jdk1.6
>* 减少占位图缓存最大容量，调整为最大可用内存的32分之一，但又不能少于2M
>* Sketch.putOptions(RequestOptions)拆分成了Sketch.putDisplayOptions(DisplayOptions)、
Sketch.putLoadOptions(LoadOptions)、Sketch.putDownloadOptions(LoadOptions)

WIKI：
>* 文档和更新日志中说明一下在Application中首次调用Sketch.with()的时候最好过滤一下非主线程，并说一下原因