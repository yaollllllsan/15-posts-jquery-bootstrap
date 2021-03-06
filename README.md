# Posts (jQuery + ajax)

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [Posts (jQuery + ajax)](#posts-jquery--ajax)
    - [首先，对页面进行设计](#首先对页面进行设计)
    - [其次，使用 bootstrap 完成首页布局](#其次使用-bootstrap-完成首页布局)
    - [然后，使用 jQuery 添加首页功能](#然后使用-jquery-添加首页功能)
    - [之后，详情页面是同样的逻辑](#之后详情页面是同样的逻辑)
    - [接下来，封装一个 ResultVO 用来简化、统一数据的返回](#接下来封装一个-resultvo-用来简化统一数据的返回)
    - [最后，进行其他优化](#最后进行其他优化)
    - [接下来的任务](#接下来的任务)
        - [增加分页等其他功能](#增加分页等其他功能)
        - [MVVM: Vue.js/ReactJS.js/AngularJS](#mvvm-vuejsreactjsjsangularjs)

<!-- markdown-toc end -->

## 首先，对页面进行设计

<img src="img/ux.png">

## 其次，使用 bootstrap 完成首页布局

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>我的博客</title>
    <!-- 提供基本的样式、布局、组件 -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css">

    <!-- 提供额外的，跟交互有关的组件功能，弹出层、tab 页切换 -->
    <script src="https://cdn.jsdelivr.net/npm/jquery@1.12.4/dist/jquery.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/js/bootstrap.min.js"></script>
    <style>
        .wrapper {
            margin-top: 80px;
        }
        .hot-tags, .hot-posts {
            border: 1px solid grey;
            height: 100px;
            padding: 1em;
            margin-top: 1em;
            margin-bottom: 2em;
        }
        .others {
            margin-top: 1em;
            padding-top: 2em;
            min-height: 80px;
            background: #eeeeee;
        }
        .posts {
            padding-right: 2em;
        }
        .post-header {
            display: flex;
            align-items: center;
        }
        .post-header img {
            width: 100%;
        }
        .post {
            margin-bottom: 2em;
            padding-bottom: 2em;
            border-bottom: 1px solid #eee;
        }
        .post-footer {
            margin-top: 1em;
        }
        #addPostModal img {
            width: 100%;
        }
    </style>
</head>
<body>

<section>
    <nav class="navbar navbar-default navbar-fixed-top" role="navigation">
        <div class="navbar-header">
            <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-ex1-collapse">
                <span class="sr-only">Toggle navigation</span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
            </button>
            <a class="navbar-brand" href="#">My Private Blog</a>
        </div>

        <div class="collapse navbar-collapse navbar-ex1-collapse">
            <ul class="nav navbar-nav">
                <li class="dropdown">
                    <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">分类 <span class="caret"></span></a>
                    <ul class="dropdown-menu">
                        <li><a href="http://baidu.com">学习</a></li>
                        <li><a href="#">娱乐</a></li>
                        <li role="separator" class="divider"></li>
                        <li><a href="#">未知</a></li>
                        <li><a href="#">琐事</a></li>
                    </ul>
                </li>
                <li><a href="#">标签</a></li>
                <li><a href="#">关于我们</a></li>
            </ul>
            <form class="navbar-form navbar-left" role="search">
                <div class="form-group">
                    <input type="text" class="form-control" placeholder="Search">
                </div>
                <button type="submit" class="btn btn-default">搜索</button>
            </form>
            <ul class="nav navbar-nav navbar-right" style="padding-right: 1em">
                <li>
                    <a href="#">用户管理</a>
                </li>
            </ul>
            <ul class="nav navbar-nav navbar-right">
                <li>
                    <a href="#" data-target="#addPostModal" data-toggle="modal">添加博客</a>
                </li>
            </ul>
        </div>
    </nav>
</section>
<section class="container wrapper">
    <div class="row">
        <div class="col-md-9 main">
            <div class="posts">
                <div class="post">
                    <div class="post-header row">
                        <div class="col-md-8">
                            <h3>特朗普说，他一定能当选</h3>
                            <p>
                                对特朗普来说，就没有不坏的规矩，所有正常的规则、规矩，他只要看着不太顺眼，立刻就像个破坏王一样砸烂。 刚刚，特朗普在联合国大会上又坏了一个规矩，他竟然为了一己私利，利用联合国的讲台，罔顾事实地编造谎言抹黑指责中国，这种行为可以说low到爆了！ 据媒体报道，在第75届联大一般性辩论会上，特朗普当着全世界多国领导人和国际组织的面，罔顾常识和事实，竟然直接称新冠病毒为“中国病毒”，甚至在讲话中用了很大篇幅在多个层面攻击中国，譬如抗疫、网络安全、生态、乃至海洋捕捞等等，时间长达7分钟。
                            </p>
                        </div>
                        <div class="col-md-4">
                            <img src="../img/mm2.jpg" class="img-thumbnail">
                        </div>
                    </div>
                    <div class="post-footer row">
                        <div class="col-md-1 post-author">张三</div>
                        <div class="col-md-3 post-time">发布于 <i>2001-10-23</i></div>
                        <div class="col-md-4"></div>
                        <div class="col-md-4">
                            <span class="post-like">点赞 (<i>10</i>) </span>
                            <span class="post-comment">评论 (<i>17</i>) </span>
                            <span class="post-share">分享 (<i>20</i>) </span>
                        </div>
                    </div>
                </div>
                <div class="post">
                    <div class="post-header row">
                        <div class="col-md-8">
                            <h3>特朗普说，他一定能当选</h3>
                            <p>
                                对特朗普来说，就没有不坏的规矩，所有正常的规则、规矩，他只要看着不太顺眼，立刻就像个破坏王一样砸烂。 刚刚，特朗普在联合国大会上又坏了一个规矩，他竟然为了一己私利，利用联合国的讲台，罔顾事实地编造谎言抹黑指责中国，这种行为可以说low到爆了！ 据媒体报道，在第75届联大一般性辩论会上，特朗普当着全世界多国领导人和国际组织的面，罔顾常识和事实，竟然直接称新冠病毒为“中国病毒”，甚至在讲话中用了很大篇幅在多个层面攻击中国，譬如抗疫、网络安全、生态、乃至海洋捕捞等等，时间长达7分钟。
                            </p>
                        </div>
                        <div class="col-md-4">
                            <img src="../img/mm2.jpg">
                        </div>
                    </div>
                    <div class="post-footer row">
                        <div class="col-md-1 post-author">张三</div>
                        <div class="col-md-3 post-time">发布于 <i>2001-10-23</i></div>
                        <div class="col-md-4"></div>
                        <div class="col-md-4">
                            <span class="post-like">点赞 (<i>10</i>) </span>
                            <span class="post-comment">评论 (<i>17</i>) </span>
                            <span class="post-share">分享 (<i>20</i>) </span>
                        </div>
                    </div>
                </div>
                <div class="post">
                    <div class="post-header row">
                        <div class="col-md-8">
                            <h3>特朗普说，他一定能当选</h3>
                            <p>
                                对特朗普来说，就没有不坏的规矩，所有正常的规则、规矩，他只要看着不太顺眼，立刻就像个破坏王一样砸烂。 刚刚，特朗普在联合国大会上又坏了一个规矩，他竟然为了一己私利，利用联合国的讲台，罔顾事实地编造谎言抹黑指责中国，这种行为可以说low到爆了！ 据媒体报道，在第75届联大一般性辩论会上，特朗普当着全世界多国领导人和国际组织的面，罔顾常识和事实，竟然直接称新冠病毒为“中国病毒”，甚至在讲话中用了很大篇幅在多个层面攻击中国，譬如抗疫、网络安全、生态、乃至海洋捕捞等等，时间长达7分钟。
                            </p>
                        </div>
                        <div class="col-md-4">
                            <img src="../img/mm2.jpg">
                        </div>
                    </div>
                    <div class="post-footer row">
                        <div class="col-md-1 post-author">张三</div>
                        <div class="col-md-3 post-time">发布于 <i>2001-10-23</i></div>
                        <div class="col-md-4"></div>
                        <div class="col-md-4">
                            <span class="post-like">点赞 (<i>10</i>) </span>
                            <span class="post-comment">评论 (<i>17</i>) </span>
                            <span class="post-share">分享 (<i>20</i>) </span>
                        </div>
                    </div>
                </div>
                <div class="post">
                    <div class="post-header row">
                        <div class="col-md-8">
                            <h3>特朗普说，他一定能当选</h3>
                            <p>
                                对特朗普来说，就没有不坏的规矩，所有正常的规则、规矩，他只要看着不太顺眼，立刻就像个破坏王一样砸烂。 刚刚，特朗普在联合国大会上又坏了一个规矩，他竟然为了一己私利，利用联合国的讲台，罔顾事实地编造谎言抹黑指责中国，这种行为可以说low到爆了！ 据媒体报道，在第75届联大一般性辩论会上，特朗普当着全世界多国领导人和国际组织的面，罔顾常识和事实，竟然直接称新冠病毒为“中国病毒”，甚至在讲话中用了很大篇幅在多个层面攻击中国，譬如抗疫、网络安全、生态、乃至海洋捕捞等等，时间长达7分钟。
                            </p>
                        </div>
                        <div class="col-md-4">
                            <img src="../img/mm2.jpg">
                        </div>
                    </div>
                    <div class="post-footer row">
                        <div class="col-md-1 post-author">张三</div>
                        <div class="col-md-3 post-time">发布于 <i>2001-10-23</i></div>
                        <div class="col-md-4"></div>
                        <div class="col-md-4">
                            <span class="post-like">点赞 (<i>10</i>) </span>
                            <span class="post-comment">评论 (<i>17</i>) </span>
                            <span class="post-share">分享 (<i>20</i>) </span>
                        </div>
                    </div>
                </div>
                <div class="post">
                    <div class="post-header row">
                        <div class="col-md-8">
                            <h3>特朗普说，他一定能当选</h3>
                            <p>
                                对特朗普来说，就没有不坏的规矩，所有正常的规则、规矩，他只要看着不太顺眼，立刻就像个破坏王一样砸烂。 刚刚，特朗普在联合国大会上又坏了一个规矩，他竟然为了一己私利，利用联合国的讲台，罔顾事实地编造谎言抹黑指责中国，这种行为可以说low到爆了！ 据媒体报道，在第75届联大一般性辩论会上，特朗普当着全世界多国领导人和国际组织的面，罔顾常识和事实，竟然直接称新冠病毒为“中国病毒”，甚至在讲话中用了很大篇幅在多个层面攻击中国，譬如抗疫、网络安全、生态、乃至海洋捕捞等等，时间长达7分钟。
                            </p>
                        </div>
                        <div class="col-md-4">
                            <img src="../img/mm2.jpg">
                        </div>
                    </div>
                    <div class="post-footer row">
                        <div class="col-md-1 post-author">张三</div>
                        <div class="col-md-3 post-time">发布于 <i>2001-10-23</i></div>
                        <div class="col-md-4"></div>
                        <div class="col-md-4">
                            <span class="post-like">点赞 (<i>10</i>) </span>
                            <span class="post-comment">评论 (<i>17</i>) </span>
                            <span class="post-share">分享 (<i>20</i>) </span>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="col-md-3 aside">
            <div class="hot-tags">
                热门标签
            </div>
            <div class="hot-posts">
                点击排行
            </div>
        </div>
    </div>
</section>
<section class="others">
    <p class="text-center">
        版权所有: 南方 IT 学院 163 班
    </p>
</section>

<div class="modal fade" id="addPostModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
                <h4 class="modal-title" id="myModalLabel">添加博客</h4>
            </div>
            <div class="modal-body">
                <div class="row">
                    <div class="col-md-8">
                        <form>
                            <div class="form-group">
                                <label for="post-title">标题</label>
                                <input type="text" class="form-control" id="post-title" placeholder="请输入标题">
                            </div>
                            <div class="form-group">
                                <label for="post-content">内容</label>
                                <textarea class="form-control" id="post-content" placeholder="请输入内容"></textarea>
                            </div>
                            <div class="form-group" style="display: none">
                                <input type="file" id="post-cover">
                            </div>
                        </form>
                    </div>
                    <div class="col-md-4">
                        <img src="../img/mm.jpg">
                        <p>
                            请点击选择图片
                        </p>
                    </div>
                </div>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-default" data-dismiss="modal">关闭</button>
                <button type="button" class="btn btn-primary">保存</button>
            </div>
        </div>
    </div>
</div>

</body>
</html>
```



## 然后，使用 jQuery 添加首页功能

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>我的博客</title>
    <!-- 提供基本的样式、布局、组件 -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css">

    <!-- 提供额外的，跟交互有关的组件功能，弹出层、tab 页切换 -->
    <script src="https://cdn.jsdelivr.net/npm/jquery@1.12.4/dist/jquery.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/js/bootstrap.min.js"></script>
    <style>
        .wrapper {
            margin-top: 80px;
        }
        .hot-tags, .hot-posts {
            border: 1px solid grey;
            height: 100px;
            padding: 1em;
            margin-top: 1em;
            margin-bottom: 2em;
        }
        .others {
            margin-top: 1em;
            padding-top: 2em;
            min-height: 80px;
            background: #eeeeee;
        }
        .posts {
            padding-right: 2em;
        }
        .post-header {
            display: flex;
            align-items: center;
        }
        .post-header img {
            width: 100%;
        }
        .post {
            margin-bottom: 2em;
            padding-bottom: 2em;
            border-bottom: 1px solid #eee;
        }
        .post-footer {
            margin-top: 1em;
        }
        #addPostModal img {
            width: 100%;
        }
    </style>
</head>
<body>

<section>
    <nav class="navbar navbar-default navbar-fixed-top" role="navigation">
        <div class="navbar-header">
            <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-ex1-collapse">
                <span class="sr-only">Toggle navigation</span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
            </button>
            <a class="navbar-brand" href="#">My Private Blog</a>
        </div>

        <div class="collapse navbar-collapse navbar-ex1-collapse">
            <ul class="nav navbar-nav">
                <li class="dropdown">
                    <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">分类 <span class="caret"></span></a>
                    <ul class="dropdown-menu">
                        <li><a href="http://baidu.com">学习</a></li>
                        <li><a href="#">娱乐</a></li>
                        <li role="separator" class="divider"></li>
                        <li><a href="#">未知</a></li>
                        <li><a href="#">琐事</a></li>
                    </ul>
                </li>
                <li><a href="#">标签</a></li>
                <li><a href="#">关于我们</a></li>
            </ul>
            <form class="navbar-form navbar-left" role="search">
                <div class="form-group">
                    <input type="text" class="form-control" placeholder="Search">
                </div>
                <button type="submit" class="btn btn-default">搜索</button>
            </form>
            <ul class="nav navbar-nav navbar-right" style="padding-right: 1em">
                <li>
                    <a href="#">用户管理</a>
                </li>
            </ul>
            <ul class="nav navbar-nav navbar-right">
                <li>
                    <a href="#" data-target="#addPostModal" data-toggle="modal">添加博客</a>
                </li>
            </ul>
        </div>
    </nav>
</section>
<section class="container wrapper">
    <div class="alert alert-success alert-dismissible" style="display: none;" id="msg-tip" role="alert">
        <button type="button" class="close" data-dismiss="alert" aria-label="Close"><span aria-hidden="true">&times;</span></button>
        <span></span>
    </div>
    <div class="row">
        <div class="col-md-9 main">
            <div class="posts">
            </div>
        </div>
        <div class="col-md-3 aside">
            <div class="hot-tags">
                热门标签
            </div>
            <div class="hot-posts">
                点击排行
            </div>
        </div>
    </div>
</section>
<section class="others">
    <p class="text-center">
        版权所有: 南方 IT 学院 163 班
    </p>
</section>
<div class="modal fade" id="addPostModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
                <h4 class="modal-title" id="myModalLabel">添加博客</h4>
            </div>
            <div class="modal-body">
                <div class="row">
                    <div class="col-md-8">
                        <form>
                            <div class="form-group">
                                <label for="post-title">标题</label>
                                <input type="text" class="form-control" id="post-title" name="title" placeholder="请输入标题">
                            </div>
                            <div class="form-group">
                                <label for="post-content">内容</label>
                                <textarea class="form-control" id="post-content" name="content" placeholder="请输入内容"></textarea>
                            </div>
                            <div class="form-group" style="display: none">
                                <input class="file-input" type="file" name="cover" id="post-cover">
                                <input type="hidden" name="author" value="张三">
                            </div>
                        </form>
                    </div>
                    <div class="col-md-4">
                        <img class="preview-img" src="../img/mm.jpg">
                        <p>
                            请点击选择图片
                        </p>
                    </div>
                </div>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-default" data-dismiss="modal">关闭</button>
                <button type="button" class="btn btn-primary post-save-btn">保存</button>
            </div>
        </div>
    </div>
</div>
<div class="modal fade" id="myalert" tabindex="-1" role="dialog">
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
                <h4 class="modal-title">提示信息</h4>
            </div>
            <div class="modal-body">
                <p></p>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-default" data-dismiss="modal">关闭</button>
            </div>
        </div><!-- /.modal-content -->
    </div><!-- /.modal-dialog -->
</div><!-- /.modal -->

<script>
    function inits() {
        loadPosts();
        $(".preview-img").click(() => $(".file-input").click());
        $(".file-input").change(() => $(".preview-img").prop("src", URL.createObjectURL($(this).get(0).files[0])));
        $(".post-save-btn").click(savePost);
        $(".posts").on("click", ".post-like", likePost);
    }
    function loadPosts() {
        // 1. 发送 ajax
        // 2. 数据，组装成 post 节点
        // 3. 放到 DOM 相应位置
        $.ajax({
            method: "GET",
            url: "/posts",
            dataType: "json"
        }).done(function (data) {
            $.each(data, function (i, e) {
                createPostNode(e).appendTo(".posts");
            })
        });
    }
    function savePost() {
        var form = $("#addPostModal form").get(0);
        var formData = new FormData(form);
        $.ajax({
            method: "post",
            url: "/post/add",
            data: formData,
            processData: false,
            contentType: false,
            dataType: "json"
        }).done(function (post) {
            createPostNode(post).prependTo(".posts");
            $("#addPostModal").modal('hide');
            showMessage("文件添加成功！");
        });
    }
    function createPostNode(post) {
        var tpl = `
         <div class="post" data-id="${post.id}">
            <div class="post-header row">
                <div class="col-md-8">
                    <h3>${post.title}</h3>
                    <p>
                        ${post.content}
                    </p>
                </div>
                <div class="col-md-4">
                    <img src="${post.cover}" class="img-thumbnail">
                </div>
            </div>
            <div class="post-footer row">
                <div class="col-md-2 post-author">${post.author}</div>
                <div class="col-md-4 post-time">发布于 <i>${post.created}</i></div>
                <div class="col-md-2"></div>
                <div class="col-md-4">
                    <span class="post-like">点赞 (<i>${post.likes}</i>) </span>
                    <span class="post-comment">评论 (<i>17</i>) </span>
                    <span class="post-share">分享 (<i>20</i>) </span>
                </div>
            </div>
        </div>`;
        return $(tpl);
    }
    function likePost(event) {
        var $this = $(this);
        var id = $this.closest(".post").data("id");
        $.get("/post/like?id=" + id, function (data) {
            $this.find("i").text(data);
        });
    }
    function showMessage(msg) {
        // $("#msg-tip > span").text(msg).parent().show();
        // alert(msg);
        $("#myalert .modal-body p").text(msg).closest("#myalert").modal('show');
    }

    // 初始化
    inits();
</script>

</body>
</html>
```

## 之后，详情页面是同样的逻辑

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>博客</title>
    <!-- 提供基本的样式、布局、组件 -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css">

    <!-- 提供额外的，跟交互有关的组件功能，弹出层、tab 页切换 -->
    <script src="https://cdn.jsdelivr.net/npm/jquery@1.12.4/dist/jquery.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/js/bootstrap.min.js"></script>
    <style>
        body {
            font-family: -apple-system,SF UI Text,Arial,PingFang SC,Hiragino Sans GB,Microsoft YaHei,WenQuanYi Micro Hei,sans-serif;
        }
        .main {
            min-height: 400px;
        }
        .hot-tags, .hot-posts {
            border: 1px solid grey;
            height: 100px;
            padding: 1em;
            margin-top: 1em;
            margin-bottom: 2em;
        }
        .others {
            margin-top: 1em;
            padding-top: 2em;
            min-height: 80px;
            background: #eeeeee;
        }
        .post {
            margin: 1em 0 3em 0;
        }
        .post p {
            font-size: 16px;
            line-height: 24px;
        }
        .comment {
            margin: 15px 0;
            padding: 15px 8px;
            border-bottom: 1px solid #eee;
        }
        .comment p {
            padding: 1em 0;
        }
        .comment-submit {
            margin-top: 1em;
        }
    </style>
</head>
<body>

<section>
    <nav class="navbar navbar-default" role="navigation">
        <div class="navbar-header">
            <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-ex1-collapse">
                <span class="sr-only">Toggle navigation</span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
            </button>
            <a class="navbar-brand" href="#">My Private Blog</a>
        </div>

        <div class="collapse navbar-collapse navbar-ex1-collapse">
            <ul class="nav navbar-nav">
                <li class="dropdown">
                    <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true"
                       aria-expanded="false">分类 <span class="caret"></span></a>
                    <ul class="dropdown-menu">
                        <li><a href="http://baidu.com">学习</a></li>
                        <li><a href="#">娱乐</a></li>
                        <li role="separator" class="divider"></li>
                        <li><a href="#">未知</a></li>
                        <li><a href="#">琐事</a></li>
                    </ul>
                </li>
                <li><a href="#">标签</a></li>
                <li><a href="#">关于我们</a></li>
            </ul>
            <form class="navbar-form navbar-left" role="search">
                <div class="form-group">
                    <input type="text" class="form-control" placeholder="Search">
                </div>
                <button type="submit" class="btn btn-default">搜索</button>
            </form>
            <ul class="nav navbar-nav navbar-right" style="padding-right: 1em">
                <li>
                    <a href="#">用户管理</a>
                </li>
            </ul>
            <ul class="nav navbar-nav navbar-right">
                <li>
                    <a href="#" data-target="#addPostModal" data-toggle="modal">添加博客</a>
                </li>
            </ul>
        </div>
    </nav>
</section>
<section class="container wrapper">
    <div class="row">
        <div class="col-md-9 main">
            <div class="post">
                <img src="../img/mm.jpg" width="100%" height="200px">
                <h3 class="post-title"></h3>
                <p class="post-content"></p>
                <div class="post-footer row">
                    <div class="col-md-2 post-author"></div>
                    <div class="col-md-4 post-time">发布于 <i></i></div>
                    <div class="col-md-2"></div>
                    <div class="col-md-4">
                        <span class="post-like">点赞 (<i></i>) </span>
                        <span class="post-comment">评论 (<i>17</i>) </span>
                        <span class="post-share">分享 (<i>20</i>) </span>
                    </div>
                </div>
            </div>
            <div class="comment-form">
                <textarea class="form-control" name="content" rows="3"></textarea>
                <button class="btn comment-submit">发表评论</button>
            </div>
            <h4 class="comments-title">评论列表</h4>
            <div class="comments">
            </div>
        </div>
        <div class="col-md-3 aside">
            <div class="hot-tags">
                相关推荐
            </div>
            <div class="hot-posts">
                猜你喜欢
            </div>
        </div>
    </div>
</section>
<section class="others">
    <p class="text-center">
        版权所有: 南方 IT 学院 163 班
    </p>
</section>


<script>
    var postid = getQueryString("id");

    $(function () {
        loadPost();
        loadComments();

        $(".comment-submit").click(saveComment);
    });

    function loadPost() {
        $.ajax({
            method: "get",
            url: "/post?id=" + postid,
            dataType: "json",
            async: false
        }).done(function (post) {
            var $post = $(".post");
            $post.find("img").attr("src", post.cover);
            $post.find(".post-title").text(post.title);
            $post.find(".post-content").html(post.content);
            $post.find(".post-author").html(post.author);
            $post.find(".post-time").html(post.created);
            $post.find(".post-like i").html(post.likes);
        });
    }
    function loadComments() {
        $.ajax({
            method: "GET",
            url: "/comments?postid=" + postid,
            dataType: "json"
        }).done(function (posts) {
            $.each(posts, function (i, e) {
                createCommentNode(e).prependTo(".comments");
            });
        });
    }
    function saveComment() {
        $.ajax({
            method: "post",
            url: "/comment/add",
            data: {
                postid: postid,
                author: "李四",
                content: $(".comment-form textarea").val()
            },
            dataType: "json"
        }).done(function (data) {
            $(".comment-form textarea").val("");
            createCommentNode(data).prependTo(".comments");
        });
    }
    function createCommentNode(comment) {
        var tpl = `
        <div class="comment">
            <header class="row">
                <div class="col-md-1">
                    <img class="img-rounded" width="100%" height="100%" src="../img/mm.jpg">
                </div>
                <div class="col-md-11">
                    <div>${comment.author}</div>
                    <div>${comment.created}</div>
                </div>
            </header>
            <p>
                ${comment.content}
            </p>
            <footer>
                回复 点赞 删除
            </footer>
        </div>`;
        return $(tpl);
    }
    function getQueryString(name) {
        let reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i");
        let r = window.location.search.substr(1).match(reg);
        if (r != null) {
            return decodeURIComponent(r[2]);
        }
        return null;
    }

</script>


</body>
</html>
```

## 接下来，封装一个 ResultVO 用来简化、统一数据的返回

ResultVO 示例
```java
public class ResultVO {
    private int code = 1;
    private String message;
    private Object data;

    private ResultVO() {}

    public String toJSON () {
        Gson gson = new Gson();
        return gson.toJson(this);
    }

    public static ResultVO ok(Object data) {
        ResultVO vo = new ResultVO();
        vo.setData(data);
        return vo;
    }

    public static ResultVO err(int code, String message) {
        ResultVO vo = new ResultVO();
        vo.setCode(code);
        vo.setMessage(message);
        return vo;
    }

    public int getCode() {
        return code;
    }

    public void setCode(int code) {
        this.code = code;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public Object getData() {
        return data;
    }

    public void setData(Object data) {
        this.data = data;
    }

}
```

使用方式:
```java
try {
    String data = ResultVO.ok(posts).toJSON();
    resp.getWriter().print(data);
} catch (Exception e) {
    e.printStackTrace();
    String error = ResultVO.err(114, e.getLocalizedMessage()).toJSON();
    resp.getWriter().print(error);
}
```

页面端:
```js
 $.ajax({
    method: "GET",
    url: "/posts",
    dataType: "json"
}).done(function (data) {
    if (data.code === 1) {
        $.each(data.data, function (i, e) {
            createPostNode(e).appendTo(".posts");
        })
    } else {
        alert(data.message);
    }
});
```

## 最后，进行其他优化

- 缺失功能的添加
- 分页功能
- 页面的细节优化

## 接下来的任务
### 增加分页等其他功能
### MVVM: Vue.js/ReactJS.js/AngularJS
