[cygwin升级到1.7后，git gui和gitk报错](http://zengrong.net/post/1554.htm)

将[cygwin](http://www.cygwin.com)升级到1.7以后，使用git gui或者gitk的时候，会出现下面的错误提示：

>Application initialization failed: no display name and no $DISPLAY environment variable
>Error in startup script: invalid command name "tk_messageBox"
>    while executing
>"tk_messageBox  -icon error  -type ok  -title "git-gui: fatal error"  -message $err"
>    invoked from within
>"if {[catch {package require Tcl 8.4} err]
> || [catch {package require Tk  8.4} err]
>} {
>        catch {wm withdraw .}
>        tk_messageBox \
>                -icon error \
>                -typ..."
>    (file "/usr/lib/git-core/git-gui" line 34)

本来以为是Tk版本太低，可是升级到8.5后问题依旧，无论怎么尝试都找不到原因，使用中文也搜索不到相关资料。或许大家都使用git for windows？

后来还是在cygwin的官方邮件列表中找到了原因：

* [gitk fails to start after updating cygwin](http://cygwin.com/ml/cygwin/2012-02/msg00324.html)
* [git gui and gitk fail to start on cygwin](http://cygwin.com/ml/cygwin/2011-08/msg00478.html)

其实原因很简单：Tk现在需要X支持了。

cygwin装上X环境之后，运行`startx`启动X Server，在X环境下运行`git gui`，熟悉的界面又出来了。（当然，期间错误无数……）

可是在cygwin下运行X……速度那个慢的哦……还是纯命令行吧

记录在此，希望对碰到同样问题又不愿意查英文资料的同学有帮助。
