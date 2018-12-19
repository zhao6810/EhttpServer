# EhttpServer
易语言Http开发框架

易语言MVC开发框架


感谢{
	《卓越网维小邓》提供的HP易语言静态库。
	《kn剑齿虎》SQL生成库。
	《kyozy》提供的HashTable与sqlite3模块。
}


HTTP层基于HPsocket5.3.4实现。

静态编译需要用到VS2010 链接器(20180925) 链接: https://pan.baidu.com/s/1fsxtjFhnAD8GaTHt1ZpXmQ 提取码: aqym

例子简单实现前段与后台。

自动路由规则。

控制器
Route.AddRoute (“/”, ControllerIndex.SumFunAddres (4), 取变量数据地址 (ControllerIndex))

Route.AddRoute (“/index/story”, ControllerIndex.SumFunAddres (5), 取变量数据地址 (ControllerIndex))

Route.AddRoute (“/index/update”, ControllerIndex.SumFunAddres (6), 取变量数据地址 (ControllerIndex))


Route.AddRoute (“/user/index”, ControllerLogin.SumFunAddres (4), 取变量数据地址 (ControllerLogin))

Route.AddRoute (“/user/login”, ControllerLogin.SumFunAddres (5), 取变量数据地址 (ControllerLogin))


Route.AddRoute (“/admin/main”, ControllerUser.SumFunAddres (4), 取变量数据地址 (ControllerUser))

Route.AddRoute (“/admin/index”, ControllerUser.SumFunAddres (5), 取变量数据地址 (ControllerUser))

Route.AddRoute (“/admin/logout”, ControllerUser.SumFunAddres (6), 取变量数据地址 (ControllerUser))


Route.AddRoute (“/admin/news/index”, ControllerNews.SumFunAddres (4), 取变量数据地址 (ControllerNews))

Route.AddRoute (“/admin/news/add”, ControllerNews.SumFunAddres (5), 取变量数据地址 (ControllerNews))

Route.AddRoute (“/admin/news/addnews”, ControllerNews.SumFunAddres (6), 取变量数据地址 (ControllerNews))

Route.AddRoute (“/admin/news/delnews”, ControllerNews.SumFunAddres (7), 取变量数据地址 (ControllerNews))


注册路由，并且计算出对应地址。并且实现自动调用

每个控制器中第一方法必须是SumFunAddres并且必须公开。第二个参数是方法序号，用于计算控制器内存地址。

序号计算方法{
	_初始化:0
	_销毁:1
	SumFunAddres:2
	Prepare：3
}
所以你的控制器从4开始算。


一个简单的登陆例子

Http 为参考类型，是控制器请求与响应的中间件，里边实现一些基础数据传递。


Prepare (Http)

如果 (This.IsPost ())

    User ＝ This.Post (“user”)
    
    Pass ＝ This.Post (“pass”)
    
    User_id ＝ SqlLite3.Wx_User ().Login (User, Pass)
    
    如果 (User_id ≠ 0)
    
        SessionId ＝ Session.SessionCreate (User_id, User, Http.Request.ClientIp)
        
        This.SetCookie (SessionId)
        
        This.Success (“登陆成功”, “/admin/main”)
        
        This.Error (“用户名或者密码错误”, “/user/index”)
        

否则

    This.Error (“用户名或者密码错误”, “/user/index”)
    


以上为基础逻辑控制

Http.Response.Body ＝ 到字节集 (This.GetTemplateContent ()) 比较关键.将模版内容设置到BODY体中。

This.GetHerder (Http.Response.Herder)		设置Herder头

Http.Response.StateCode ＝ 200


以上为响应体与响应头


视图层支持基础的模版功能 变量分配，模版展示。


M层

只实现了一些基础的SQL语句生成。

例如插入

news.Create_time ＝ Times.Time ()

SQLAction.data (“news_title”).val_Str (news.News_title)

SQLAction.data (“news_content”).val_Str (news.News_title)

SQLAction.data (“create_time”).val_Int32 (news.Create_time)

SQL ＝ SQLAction.Insert (“wx_news”)

返回 (SQLlite.执行SQL (SQL))


 
删除
SQLAction.Model (“wx_news”)

SQL ＝ SQLAction.where (“news_id = ” ＋ 到文本 (News_ID)).Delete ()

返回 (SQLlite.执行SQL (SQL))


另外还支持设置静态目录，SESSION管理，MIME类型自识别等等。

以前挺喜欢易语言单文件的原代码格式，现在接触语言多了越发多文件的好处实在太多了，比如易语言的“自定义数据类型”如果定义类型过多话，找起来非常麻烦，非常建议老吴给“自定义数据类型”也加个文件夹管理功能吧，做好类型分类，这样做会好很多的。


有过MVC开发经验的简单看看就能看懂我的结构。

易语言缺少很多现代语言的特性，例如反射、动态类等等，连基本的类都是伪类。为了动态调用类遇到了很多坑。比如调用类的时候要带上本对象，然后还得手动初始化局部变量，如果这两件事没做会遇到各种崩溃，之前参考其他易友写的方法，就是各种的崩溃，直到反编译了易语言调用方法。

以上开发的只是初步实现，离可以上线还缺少很多东西，例如SQL注入，Session安全，等等。 做一些简单小网站暂时够用，只要你觉得不嫌麻烦。



调用类方法的过程。

pushad

mov ecx,dword ptr ss:[ebp+10]

push ecx

mov eax,dword ptr ss:[ebp+8]

push eax

eax,dword ptr ss:[esp]

eax,dword ptr ds:[eax]

eax,dword ptr ds:[eax]

mov ecx,dword ptr ss:[ebp+c]

call ecx

popad


