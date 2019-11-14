# Intent解析的匹配规则

1. 动作Action检测

    - 一个**Intent** **Filter**元素**至少应该包含一个Action**，否则任何一个Intent请求都不能与之匹配成功；

    - 如果**一个Intent Filter包含多个Action**，只要其中一个Action匹配Intent请求中的Action成功，则该Intent Filter匹配成功；

    例如：

    ```java
    <intent-filter>
    
      <action android:name="com.example.project.SHOW_CURRENT" />
    
      <action android:name="com.example.project.SHOW_RECENT" />
    
      <action android:name="com.example.project.SHOW_PENDING" />
    
      . . .
    
    </intent-filter>
    ```
    只要Intent请求中有上述三条中的一条即算Action匹配成功。

    -  如果**Intent请求中没有Action项**，则对于任何Intent Filter，只要其中含有Action项，都能匹配成功。

2. 种类Category检测

	- **对Action的附加描述**—必须在设置Action的情况下才能使用;

	- 只有当**Intent请求中的所有Category属性在一个Intent Filter中都能找到相应的Category匹配项时，才算匹配成功**， Intent Filter 中多余的Category并不会导致匹配失败；

	- **一个不包含Category项的IntentFilter只会匹配同样不包含Category的Intent请求**；

	- 原则上如果一个**Intent请求中没有Category项**，应该**总是通过种类检测（即匹配成功）**，而不管Intent Filter中有什么Category； 但是，有个例外，Android对待所有传递给Context.*startActivity*()的隐式intent，它们内部**至少包含**“*android.intent.category.DEFAULT*” 。因此，**Activity想要接收隐式intent必须要在intent过滤器的种类部分中包含android.intent.category.DEFAULT

3. 数据Data检测--1

	- 每个<data>元素指定一个URI和数据类型（MIME类型）。它有四个属性scheme、host、port、path对应于URI的每个部分： 
		 	`scheme://host:port/path`
		- 例如：content://com.example.project:200/folder/subfolder/etc
			 	scheme是content，host是"com.example.project"，port是200，path是“/folder/subfolder/etc"。
	- host和port一起构成URI的凭据（authority），如果host没有指定，port也被忽略。 这四个属性都是可选的，但**它们之间并不都是完全独立的**。要让authority有意义，scheme必须也要指定。要让path有意义，scheme和authority也都必须要指定(**匹配时在Intent filter中如果前面的属性不指定,则不检测后面的属性值总是通过检测**)。

3. 数据Data检测—2

   - **MIME**类型**就是设定某种扩展名的文件用一种应用程序来打开的**方式类型**，当该扩展名文件被访问的时候，浏览器会自动使用指定应用程序来打开。多用于指定一些客户端自定义的文件名，以及一些媒体文件**打开方式**。

   - 服务器会将它们发送的多媒体数据的类型告诉浏览器，而通知手段就是说明该多媒体数据的MIME类型，从而让浏览器知道接收到的信息哪些是MP3文件，哪些是BMP文件等等。服务器将MIME标志符放入传送的数据中来告诉浏览器使用哪种插件读取相关文件。

   - **MIME类型其实就是一个字符串**，中间用一个 “/” 来隔开，“/”前面的部分是系统识别的部分，就相当于我们定义一个变量时的变量数据类型，据此系统能够知道我们所要表示的是个什么东西。至于 “/” 后面的部分就是我们自已来随便定义的“变量名”（类型名称）了。

   - *<data>元素的type属性指定数据的MIME类型，Intent请求和Intent Filter都可以用*“\*”通配符匹配子类型*字段*

     ​	*例如“text/*”，“audio/*”表示任何子类型。

5. 数据Data检测—3

   - **数据检测既要检测URI**，也要检测数据类型**。规则如下(**一一对应匹配)：

   - 一个Intent请求既不包含URI，也不包含数据类型(只有Action的情况) ：仅当Intent Filter也不指定任何URIs和数据类型时，才能通过检测，其余情况均不通过。 

   - 一个Intent请求包含URI，但不包含数据类型：仅当Intent Filter也不指定数据类型，同时它们的URI匹配，才能匹配成功。

   - 一个Intent请求包含数据类型，但不包含URI：仅当Intent Filter也只包含数据类型且与Intent请求相同，才匹配成功。 

   - 一个Intent请求既包含URI，也包含数据类型，只有二者与Intent Filter全部匹配才算通过；

6. 数据Data检测—4

   - Intent filter示例（Data部分检测）：

     - <data android:mimeType="image/*" />，这个组件能够从内容提供者获取image数据并显示它。
       - 因为大部分可用数据由内容提供者（content provider）分发，**过滤器指定一个数据类型但没有指定URI或许更通用**。

     - <data android:scheme="http" android:mimeType="video/*" />，这个组件能够从网络获取视频数据并显示它。

当用户点击一个web页面上的link，浏览器应用程序首先会试图去显示数据，如果它不能显示数据，它将把一个隐式Intent加上scheme和数据类型，去启动一个能够做此工作的Activity。