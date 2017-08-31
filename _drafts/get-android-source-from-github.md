# 从Github上下载Android源码

[toc]

## 背景
由于国内没有办法访问Android源码的官方网站及代码库，但是作为一个Android开发人员不能看到Android源码，这是没办法接受的。

经过查找发现Android已经把代码发布在了[Github][github-site]上，那么我们为什么不能从[Github][github-site]上下载repo源码呢？查看了Android在[Github][github-site]上托管的项目展示网站 [Android Github IO][android-io]。上面提到：
> The Android GitHub account provides a read-only mirror of some of the most common repositories from the Android Open Source Project. You can view the source code and review the history of the project, just like any other repository on GitHub.

这就是说[Github][github-site]上的Android代码是一份官网上的**只读镜像**，可以在这里像其他Github上的项目一样查看代码和提交历史等。
> When working with the Android source code, developers use the repo tool to checkout the code from the official git repositories. Because of differences in how repositories are named, repo will not work with the Android mirror on GitHub. Several people have published guides on using repo with GitHub, but you're on your own there.

而这里就明确说道由于项目库命名的差异，repo并不能工作在Android的[Github][github-site]镜像上。但是有很多人发布了使用repo从[Github][github-site]下载Android代码的方法，可以通过google搜索到。






[github-site]: https://github.com "Github官网"
[android-io]: https://android.github.io "Android Github IO"
[google+]: https://plus.google.com/+MarkRenouf/posts/3NngLGAjVgG "How to get android source from GitHub using repo"
[stackoverflow]: https://stackoverflow.com/questions/7761518/checking-out-android-source-from-github "Checking out Android source from github"