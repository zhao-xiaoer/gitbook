# Path与Paths
* java.nio.file.Path 接口代表一个平台无关的平台路径，描述了目
录结构中文件的位置。
*  Paths 提供的 get() 方法用来获取 Path 对象：
> Path get(String first, String … more) : 用于将多个字符串串连成路径。
*  Path 常用方法：
> boolean endsWith(String path) : 判断是否以 path 路径结束  
> boolean startsWith(String path) : 判断是否以 path 路径开始  
> boolean isAbsolute() : 判断是否是绝对路径  
> Path getFileName() : 返回与调用 Path 对象关联的文件名  
> Path getName(int idx) : 返回的指定索引位置 idx 的路径名称  
> int getNameCount() : 返回Path 根目录后面元素的数量  
> Path getParent() ：返回Path对象包含整个路径，不包含 Path 对象指定的文件路径  
> Path getRoot() ：返回调用 Path 对象的根路径  
> Path resolve(Path p) :将相对路径解析为绝对路径  
> Path toAbsolutePath() : 作为绝对路径返回调用 Path 对象  
> String toString() ： 返回调用 Path 对象的字符串表示形式  

# Files类
java.nio.file.Files 用于操作文件或目录的工具类
* Files常用方法：
> Path copy(Path src, Path dest, CopyOption … how) : 文件的复制  
> Path createDirectory(Path path, FileAttribute<?> … attr) : 创建一个目录  
> Path createFile(Path path, FileAttribute<?> … arr) : 创建一个文件  
> void delete(Path path) : 删除一个文件  
> Path move(Path src, Path dest, CopyOption…how) : 将 src 移动到 dest 位置  
> long size(Path path) : 返回 path 指定文件的大小  
* Files常用方法：用于判断
> boolean exists(Path path, LinkOption … opts) : 判断文件是否存在  
> boolean isDirectory(Path path, LinkOption … opts) : 判断是否是目录  
> boolean isExecutable(Path path) : 判断是否是可执行文件  
> boolean isHidden(Path path) : 判断是否是隐藏文件  
> boolean isReadable(Path path) : 判断文件是否可读  
> boolean isWritable(Path path) : 判断文件是否可写  
> boolean notExists(Path path, LinkOption … opts) : 判断文件是否不存在  
> public static <A extends BasicFileAttributes> A readAttributes(Path path,Class<A> type,LinkOption... options) : 获取与 path 指定的文件相关联的属性  
* Files常用方法：用于操作内容
> SeekableByteChannel newByteChannel(Path path, OpenOption…how) : 获取与指定文件的连接，how 指定打开方式    
> DirectoryStream newDirectoryStream(Path path) : 打开 path 指定的目录  
> InputStream newInputStream(Path path, OpenOption…how):获取 InputStream 对象  
> OutputStream newOutputStream(Path path, OpenOption…how) : 获取 OutputStream 对象  

# 自动资源管理
Java 7 增加了一个新特性，该特性提供了另外
一种管理资源的方式，这种方式能自动关闭文
件。这个特性有时被称为自动资源管理
(Automatic Resource Management, ARM)， 该特
性以 try 语句的扩展版为基础。自动资源管理
主要用于，当不再需要文件（或其他资源）时，
可以防止无意中忘记释放它们  
注意：  
① try 语句中声明的资源被隐式声明为 final ，资源的作用局限于带资源的 try 语句  
② 可以在一条 try 语句中管理多个资源，每个资源以“;” 隔开即可。  
③ 需要关闭的资源，必须实现了 AutoCloseable 接口或其自接口 Closeable  