# 🎨 模板开发指南

emlog可以方便的更换模板，模板文件位于 content\templates 目录下，每个模板都是一个单独的文件夹，文件夹以模板的英文别名命名。通过应用商店以及后台上传安装的模板都保存在这个目录下。

## 模板文件及目录说明

| 文件名            | 说明                                                                                         |
| ----------------- | -------------------------------------------------------------------------------------------- |
| css               | 存放模板所需的所有CSS样式文件                                                                |
| js                | 存放模板所需的所有JS文件                                                                     |
| images            | 存放模板所需 LOGO 等图片资源                                                                 |
| preview.jpg       | 在后台模板选择界面显示的模板预览图，推荐：500x300 jpg格式                                    |
| header.php        | 站点头部信息，一般包含页面head信息和顶部标题、导航栏                                         |
| echo_log.php      | 文章详情页，展示单篇文章内容                                                                 |
| log_list.php      | 首页，展示文章列表                                                                           |
| footer.php        | 站点底部信息，展示版权信息等                                                                 |
| page.php          | 页面，展示自定义的页面                                                                       |
| side.php          | 侧边栏，如制作单栏模板则该文件不是必须的                                                     |
| module.php        | 功能模块：最新文章、评论、分类、标签等                                                       |
| 404.php           | 自定义404页面未找到时的报错页面                                                              |
| pw.php            | 自定义加密文章输入密码页面，没该文件使用系统默认样式 【非必须】                              |
| user.php          | 路由 /user 加载的模板，可用于用户中心等页面【非必须】                                        |
| plugins.php       | 模板的系统调用文件，模板启用后，该文件会被系统自动加载。可用于实现类似插件的功能。【非必须】 |
| options.php       | 模板设置的配置文件，可以构建更丰富的设置项。【非必须】                                       |
| callback.php      | 模板的事件回调函数定义，详见事件回调部分文档【非必须】                                       |
| custom_fields.php | 预设模板自己需要的文章自定义字段【非必须】                                                   |

## 模板引擎

emlog未采用任何其他第三方的模板引擎，直接使用PHP原生的语法标记来内嵌HTML生成动态页面。这样不但降低了开发者的学习负担，也大大提高了页面加载和渲染效率。

```php
// 嵌入变量
<div><?= $value ?></div>

// 循环
<?php foreach ($abc as $v) :?>
    <div><?= $v ?></div>
<?php endforeach;?>

// 判断
<?php if($a == 'abc') :?>
    <div>hello</div>
<?php endif;?>

```

## 公共代码

### 禁止直接访问

下面这行代码存在于模板目录下的每个php文件开头，其作用是防止代码所在的php脚本被直接访问执行，务必保留。

```php 
if(!defined('EMLOG_ROOT')) {exit('error!');}
```

或者

```php 
defined('EMLOG_ROOT') || exit('access denied!');
```

### 引用模板文件

下面这两行代码作用是调用模板文件夹下的 side.php 和 footer.php 的代码到当前文件的当前位置。

```php
require_once View::getView('side'); 
require_once View::getView('footer'); 

// View是模板视图控制器，View::getView('文件名','文件后缀')将返回当前模板安装路径下对应的文件。
// getView 函数的第二个参数为缺省参数，在不传入值的情况下，将默认作为.php文件后缀返回文件路径。
```

## 文件说明

### header.php

站点头部信息，一般包页面head信息和顶部标题、导航栏。

#### 页头信息

开头注释内容是模板的必要信息，该信息会显示在后台模板管理界面，务必完整填写。

```php
/*
Template Name:默认模板
Version:1.0
Template Url:https://www.emlog.net/template/
Description:emlog的默认模板
Author:emlog
Author Url:https://www.emlog.net/author/index/577
*/
```

:::tip
其中 Template URL 和 Author URL 请使用官网 emlog.net 的应用链接和作者页，其他非官网链接不会在后台展示超链接。
:::

#### 加载模板文件

require_once View::getView('module'); // 加载模板通用模块.

#### 变量&常量

| 变量&常量         | 类型 | 说明                                                                                                         |
| ----------------- | ---- | ------------------------------------------------------------------------------------------------------------ |
| $site_title       | 变量 | 站点标题（受后台seo优化设置影响）                                                                            |
| $site_key         | 变量 | 站点关键字                                                                                                   |
| $site_description | 变量 | 输出站点浏览器描述 （受后台seo优化设置影响）                                                                 |
| $blogname         | 变量 | 站点标题                                                                                                     |
| $bloginfo         | 变量 | 站点副标题                                                                                                   |
| BLOG_URL          | 常量 | 站点首页的URL，输出形如https://emlog.net/                                                                    |
| TEMPLATE_URL      | 常量 | 模板文件夹的URL，用于加载模板内的css、js及其他内容，输出形如http://emlog.net/blog/content/templates/default/ |

上面的变量、常量都可以通过下面的方式在模板中输出

```php
<?= $page_url ?>
<?= BLOG_URL ?>
```

### footer.php

站点底部信息，展示版权、备案等信息等。

#### 变量&常量

| 变量、常量            | 类型 | 说明                   |
| --------------------- | ---- | ---------------------- |
| $icp                  | 变量 | 后台设置的ICP备案号    |
| $footer_info          | 变量 | 后台设置的页面底部信息 |
| Option::EMLOG_VERSION | 常量 | 当前emlog版本号        |

### log_list.php

首页模板，展示文章列表。

#### 变量&方法

| 变量、常量、方法                          | 类型 | 说明                                            |
| ----------------------------------------- | ---- | ----------------------------------------------- |
| $value['log_cover']                       | 变量 | 文章封面图URL                                   |
| $value['logid']                           | 变量 | 当前文章的id                                    |
| $value['log_url']                         | 变量 | 文章地址URL                                     |
| $value['log_title']                       | 变量 | 文章标题                                        |
| date('Y-n-j', $value['date'])             | 变量 | 文章发布时间，参数'Y-n-j G:i l'用于定义日期格式 |
| $value['log_description']                 | 变量 | 文章摘要 （没有摘要则输出全文）                 |
| $value['comnum']                          | 变量 | 当前文章的评论数                                |
| $value['views']                           | 变量 | 当前文章的浏览量                                |
| $value['fields']                          | 变量 | 自定义字段，数组类型，读取方式见下方示例        |
| editflg($value['logid'],$value['author']) | 变量 | 当管理员或作者登陆时显示“编辑”链接              |
| topflg($value['top'])                     | 变量 | 显示置顶标记，该函数位于模板module.php内        |
| $page_url                                 | 变量 | 显示当前列表页的翻页功能                        |
| blog_sort($value['logid'])                | 方法 | 展示文章所属的分类                              |
| blog_author($value['author'])             | 方法 | 展示文章的作者                                  |
| blog_tag($value['logid'])                 | 方法 | 展示文章的标签                                  |

上面的变量、方法都可以通过下面的方式在模板中输出

```php
<?= $value['logid'] ?>
<?= blog_author($value['author']) ?>

// 读取自定义字段，其中索引 abcd 即为自定义字段名称
if (isset($value['fields']['abcd'])) {
    echo $value['fields']['abcd']; // 输出自定义字段名为 abcd 的值
}
```

### echo_log.php

文章详情页模板，展示单篇文章内容。

#### 变量&方法

| 变量、常量、方法                                                            | 类型 | 说明                                        |
| --------------------------------------------------------------------------- | ---- | ------------------------------------------- |
| $logid                                                                      | 变量 | 当前文章的id                                |
| $log_title                                                                  | 变量 | 文章标题                                    |
| $log_cover                                                                  | 变量 | 封面图URL                                   |
| date('Y-n-j', $date)                                                        | 变量 | 发布时间，参数'Y-n-j G:i l'用于定义日期格式 |
| $log_content                                                                | 变量 | 文章正文                                    |
| $excerpt                                                                    | 变量 | 文章摘要                                    |
| $comnum                                                                     | 变量 | 当前文章的评论数                            |
| $views                                                                      | 变量 | 当前文章的浏览量                            |
| $fields                                                                     | 变量 | 自定义字段，数组类型，读取方式见下方示例    |
| $page_url                                                                   | 变量 | 显示当前列表页的翻页功能                    |
| topflg($top)                                                                | 方法 | 显示置顶标记                                |
| blog_tag($logid)                                                            | 方法 | 标签                                        |
| blog_author($author)                                                        | 方法 | 作者                                        |
| blog_sort($logid)                                                           | 方法 | 所属的分类                                  |
| editflg($logid,$author)                                                     | 方法 | 当管理员或作者登录时显示“编辑”链接。        |
| neighbor_log($neighborLog)                                                  | 方法 | 邻近文章，就是上一篇、下一篇。              |
| blog_comments($comments)                                                    | 方法 | 评论列表                                    |
| blog_comments_post($logid,$ckname,$ckmail,$ckurl,$verifyCode,$allow_remark) | 方法 | 发表评论框                                  |

```php
// 在模板中输出标题
<?= $log_title ?>

// 读取自定义字段，其中索引 abcd 即为自定义字段名称
if (isset($fields['abcd'])) {
    echo $fields['abcd']; // 输出自定义字段名为 abcd 的值
}
```

### page.php

页面，展示自定义的页面。变量定义等和echo_log.php相同。

### side.php

侧边栏，主要负责根据后台widgets设置信息输出侧边栏内容。建议该文件内代码保持不变。

### module.php

模板公共代码，包含侧边widgets、评论、引用、编辑等。

该文件由若干函数组成，被模板文件调用，可在内自定义函数实现更多功能。

如在自定义函数内调用emlog缓存时，假设读取user缓存信息，则形如： global $CACHE; $user_cache = $CACHE→readCache('user');

如需要操作数据库，则形如： $DB = MySql::getInstance(); $res = $DB→query($sql);

### 404.php

用于自定义404页面的模板。

### pw.php

用于自定义加密文章输入密码页面，如果没有该模板文件，将使用系统默认样式，不影响输入密码功能正常使用，模板内容请参考默认模板。

### user.php

用户中心模板，可用于主题构建自己的用户中心。用户中心URL路由规则如下，其中 xxx 为自定义的路由名称，比如：/user/profile 或 /?uc=profile 等，如果该模板文件不存在，访问会提示404

- 路由规则1（需要开启伪静态）: /user/xxx
- 路由规则2: /?uc=xxx

示例如下：

```php
<?php
/**
 * 前台用户中心
 */
defined('EMLOG_ROOT') || exit('access denied!');


// 判断是否登录
if (ISLOGIN) {
    //do something
}

// 获取当前登录用户信息
$avatar = $userData['photo']
$name = $userData['nickname']
$bio = $userData['description']
$email = $userData['email']

// 获取当前路由路径
// 变量 $routerPath 存储了当前请求的路由路径，如：/user/profile，$routerPath 值为 profile
// 引入头部模板文件（可根据路由判断是否引入）
if (in_array($routerPath, ['', 'order', 'account', 'profile', 'weiyu'])) {
    include View::getView('header');
}

// 实现路由对应功能
if ($routerPath === 'profile') {
    include View::getView('user_xxxx') //可加载其他模板文件
} elseif ($routerPath === 'weiyu') {
    // 展示微语页
} elseif ($routerPath === 'order_calback') {
    // 处理支付回调逻辑
} else {
    show_404_page();
}

// 引入底部模板文件
include View::getView('footer')

```

### custom_fields.php

预设模板自己需要的文章自定义字段，在后台文章编辑页面添加自定义字段支持下拉选择这些预设字段。配置内容参考如下：

```php
<?php
/**
 * 模板预设的文章自定义字段
 */

defined('EMLOG_ROOT') || exit('access denied!');

$custom_fields = [
    'price' => [
        'type'        => 'text',
        'name'        => '价格',
        'description' => '如：9.99',
        'default'     => ''
    ],
    'need_vip' => [
        'type'        => 'radio',
        'name'        => '是否需要会员',
        'values'      => [
            '0' => '否',
            '1' => '是'
        ],
        'description' => '1是，0否',
        'default'     => '0'
    ],
];
```

### plugins.php

模板的系统调用文件，模板启用后，该文件会被系统自动加载。可用于实现类似插件的功能。

有时候模板也会希望在系统中自定义一些功能，包括在插件的挂载点挂载函数进行功能扩展，该文件就赋予模板这个能力，这样模板就不需要安装额外的配套插件，就可以实现更丰富的功能。

```php
<?php
/**
 * 模板的系统调用文件
 * 模板启用后，该文件会被系统自动加载。可用于实现类似插件的功能。
 */

if (!defined('EMLOG_ROOT')) {
    exit('error!');
}

/* eg:

function sameFunc() {
    echo "xxxx";
}

addAction('adm_head', 'sameFunc');

*/
```

### options.php

模板设置的配置文件，见下文 开启模板设置 部分。

## 自定义模板

文章、分类、页面：支持自定义模板，用户在创建分类、页面以及文章的时候可以选择模板主题支持的自定义模板，从而实现更丰富的页面展示。

### 自定义模板文件的命名规则

* 分类：文件名以 log_list_ 开头，如：log_list_abc.php
* 文章：文件名以 echo_log_ 开头，如：echo_log_abc.php
* 页面：文件名以 page_ 开头，如：page_abc.php

### 自定义模板文件的注释规则

```php
<?php
/*@name 自定义模板的名称*/

```

该注释内容会作为自定义模板名称出现在下拉选择框中 ，符合上述规范的自定义模板会以下拉选择框的方式出现在添加分类和创建页面场景中，方便用户选择。

## 模板挂载点

如下代码格式是模板里的插件挂载点，参考默认模板，注意保留，不要删除

```php
<?php doAction('xxx') ?>
```

:::danger
注意：务必在开发模板时参考默认模板保留下面这些挂载点，方便兼容其他插件。
:::


| 挂载点                            | 所属文件     | 改在点描述                                       | 实例 |
| --------------------------------- | ------------ | ------------------------------------------------ | ---- |
| doAction('index_head')            | header.php   | 站点head标签内挂载点，用于挂载js或css样式文件    |
| doAction('index_footer')          | footer.php   | 页脚底部挂载点，用于挂载底部信息展示类插件       |
| doAction('index_loglist_top')     | log_list.php | 首页文章列表顶部挂载点, 建议添加在首页导航栏下方 |
| doAction('log_related', $logData) | echo_log.php | 相关文章挂载点, 添加在文章内容和评论之间的位置   |

## 事件回调

在emlog后台的外观-模板管理页面，用户可以开启、删除、更新模板。这些操作会触发对应的回调函数。
开发者可以给模板添加文件： callback.php 并定义特定事件的回调函数，来实现模板初始化、数据清理、数据结构更新等操作。

| 事件     | 触发函数        |
| -------- | --------------- |
| 开启模板 | callback_init() |
| 删除模板 | callback_rm()   |
| 更新模板 | callback_up()   |

示例:（请参考默认主题目录下的 callback.php）

## 模板设置

模板支持自定义设置功能，为模板提供更丰富的设置功能。

在模板目录里放入 `options.php` 文件，内容格式如下即可，可以任意增加设置项，开头注释不可以删除或更改，参考如下：

```php
<?php
/*@support tpl_options*/

!defined('EMLOG_ROOT') && exit('access denied!');

$options = [
    /** 此项必需存在 */
    'TplOptionsNavi'   => [
        'type'         => 'radio',
        'name'         => '定义设置项标签页名称',
        'values'       => [
            'tpl-head' => '头部设置',
        ],
        'icons' => array(
            'tpl-head' => 'ri-home-line',
        ),
        'description'  => '<p>模板：晨 <br>欢迎使用这款简约的模板，目前仅支持设置头部logo</p>'
    ],
    'sale_qq'          => [
        'labels'       => 'tpl-head',
        'type'         => 'text',
        'name'         => 'QQ咨询',
      	'multi'        => 'true',
        'values'       => ['12345678'],
    ],
    'logotype'         => [
        'labels'       => 'tpl-head',
        'type'         => 'radio',
        'name'         => 'LOGO显示模式',
        'values'       => [
            '1' => '文字',
            '0' => '图片',
        ],
        'default'      => '1',
    ],
    'appearance-color'   => [
        'labels'      => 'tpl-appearance',
        'type'        => 'color',
        'name'        => '网站主色调',
        'description' => '',
        'values'      => array('#006fff'),
    ],
    'logoimg'          => [
        'labels'       => 'tpl-head',
        'type'         => 'image',
        'name'         => 'LOGO上传',
        'values'       => [
            TEMPLATE_URL . 'images/logo.png',
        ],
        'description'  => '上传LOGO图片。'
    ],
    'index_sort_list' => [
        'labels'       => 'modules',
        'type'         => 'sort',
        'name'         => '分类多选',
        'multi'        => 'true',
        'description'  => ''
    ],
    'index_page_list' => [
        'labels'       => 'modules',
        'type'         => 'page',
        'name'         => '页面多选',
        'multi'        => 'true',
        'description'  => ''
    ],
     'styles-lazyopts' => [
        'labels'       => 'styles',
        'type'         => 'checkbox',
        'name'         => '图像异步懒加载',
        'values'       => [
            'view'   => '浏览量',
            'comnum' => '评论数量',
            'agree'  => '点赞数量',
        ],
        'default' => array('view', 'comnum'),
        'description' => '',
    ],
    'index_tag'        => [
        'labels'       => 'tpl-head',
        'type'         => 'checkon',
        'name'         => '首页是否显示标签列表',
        'values'       => ['1' => '开启'],
        'default'      => '1',
        'description'  => ''
    ],
    'index_post_list'  => [
        'labels'       => 'tpl-head',
        'type'         => 'select',
        'name'         => '搜索文章',
        'new'          => 'NEW',
        'pattern'      => 'post',
        'description'  => ''
    ],
    'index_cate_list'  => [
        'labels'       => 'tpl-head',
        'type'         => 'select',
        'name'         => '搜索分类',
        'new'          => 'NEW',
        'pattern'      => 'cate',
        'description'  => ''
    ],
    'index_page_list'  => [
        'labels'       => 'tpl-head',
        'type'         => 'select',
        'name'         => '搜索页面',
        'new'          => 'NEW',
        'pattern'      => 'page',
        'description'  => ''
    ],
    'index_block_list' => [
        'labels'       => 'tpl-head',
        'type'         => 'block',
        'name'         => '拖动多内容块',
        'new'          =>  'NEW',
        'description'  => ''
    ],
    'index-image_list' => [
        'labels'       => 'tpl-head',
        'type'         => 'block',
        'name'         => '拖动多图片内容块',
        'new'          => 'NEW',
        'pattern'      => 'image',
        'description'  => ''
    ],
    'index-num_text'   => [
        'labels'       => 'tpl-head',
        'type'         => 'text',
        'name'         => '数字文本框',
        'new'          =>  'NEW',
        'pattern'      => 'num',
        'unit'         => '秒',
        'max'          => '10',
        'min'          => '1',
        'description'  => ''
    ],
    'index-date_text'   => [
        'labels'       => 'tpl-head',
        'type'         => 'text',
        'name'         => '日期文本框',
        'new'          =>  'NEW',
        'date'         => 'true',
        'description'  => ''
    ],
];
```

### options.php里，每个元素都该写什么？

如上所示，*$options* 数组里，key为设置项的id，而value是一个数组，数组里包含若干个元素。其中type属性和name属性必选，name是设置项名字，而type用来指定设置项的类型。

### 设置项支持的类型：

- radio: 单选按钮
- checkbox: 复选按钮
- checkon: 开关
- text: 文本
- image: 图片
- page: 页面
- sort: 分类
- tag: 标签
- select: 搜索选择
- block: 多内容块
- color: 颜色选择

### 设置项说明

- 对于所有类型，default属性用于指定默认值，当没有指定default时，使用values里第一个值，若都没有指定，则会使用奇怪的默认值。
- 对于radio和chexkbox类型，values属性用来设置各个按钮的值和显示名称。
- 除sort外，均可以指定depend为sort，表示该选项可以根据不同的分类设置不同的值，当指定depend为sort时，可选unsorted属性，为true时，表示包括未分类，为false不包括，默认为true。
- 除tag外，均可以指定depend为tag，表示该选项可以根据不同的标签设置不同的值，例如给标签添加图标
- sort和page可设置multi属性为true，表示多选。
- description属性用于描述该选项 (可选) 
- 若type为text，可设置multi属性为true，表示多行文本，
- 可选属性rich用以支持富文本，若设置该值，将加载编辑器（已废弃，pro系列版本不支持）。
- 如果要使用数字文本框，type仍为text，可设置pattern属性为num。可指定max、min、unit，即限制最大值、限制最小值和数量单位。可单独设置最小值或最大值。例如仅设置最小值，最大值不会限制输入。计量单位会显示在文本框最右侧。
- 如果使用日期文本框，type仍为text，可设置date属性为true即可。
- 若type为sort、page或者tag，且设置了多选，默认值将为空，否则将为第一个该类型的值。
- 对于类型**select**，pattern属性是**必填项**，可以填入：(1). post  (2).cate  (3).page。分别依次对应文章、分类、页面。此功能模块在数据非常庞大时可能查询缓慢。使用内置函数获取的数组内容为设置类型的ID，例如获取到一组文章gid。
- (可选) 上述**所有类型**均支持 *new* 属性，即会在设置项名称后显示提醒徽标，效果可见默认模板。该属性值随意填写，如：NEW、新等。若为空或不填写将不显示。
- 对于类型**block**，可选设置pattern属性，若不设置pattern属性默认内容为文本，可设置multi属性为true，表示多行文本。pattern属性设置为image可以使用多图片内容块。

### 为设置菜单增加图标

TplOptionsNavi项内可加入图标icons数组，为你的主题设置侧边栏菜单父设置名称前增加图标。icons数组的键名和TplOptionsNavi项values数组一致。使用的是[Remixicon](https://remixicon.com/)
，去图标站点找到合适的图标，复制其class内的属性值即可，例如class="ri-home-line"
，只需复制ri-home-line即可。另外需在模板plugins.php内加入以下代码用于引入图标CSS。

```php
function optionIconFont() {
    echo sprintf('<link rel="stylesheet" href="%s">', 'https://cdn.bootcdn.net/ajax/libs/remixicon/3.5.0/remixicon.min.css?ver=' . Option::EMLOG_VERSION_TIMESTAMP);
}
addAction('adm_head', 'optionIconFont');
```

### 模板里如何调用设置项

插件提供简单方法  _g($key) 或 _em($key)，来获取设置，以_g($key)为例如：

- 使用_g('sidebar')来获取侧边栏的设置，取到的值将为0或者1，
- 使用_g('sortIcon')来获取分类icon的全部设置，以分类id为key的数组，
- 使用_g('sortIcon.1')来获取分类id为1（如果存在）的sortIcon。需要注意的是，对于类型为page的，将取到页面id，类型为sort的，将取到分类id，类型为tag的，将取到标签名。

若不传递参数，即使用 _g() 方法将获取到所有设置项，对于老的模板迁移来的，可以用extract( _g() );来代替原来的加载option文件。

示例如下: 来自默认主题设置站点 logo 的配置

```php

<?php if (_g('logotype') == 1): ?>
   <a class="blog-header-title" href="<?= BLOG_URL ?>"><?= $blogname ?></a>
   <div class="blog-header-subtitle subtitle-overflow" title="<?= $bloginfo ?>"><?= $bloginfo ?></div>
<?php else: ?>
   <a href="<?php echo BLOG_URL; ?>" title="<?php echo $bloginfo; ?>"><img src="<?php echo _g('logoimg'); ?>" alt="<?php echo $blogname; ?>"/></a>
<?php endif; ?>
```

如需获取多内容块的数据，提供_getBlock($key, $type)方法获取：

- $key同_g()方法提供的参数
- $type是多内容块的数据类型，分为title和content，此参数可缺省，默认会获取content。title是多内容块填入的标题。content即内容，使用内置该函数可获取多内容块文本类型的文本或多内容块图片类型设置的图片URL
- 返回值类型为array

使用案例：

```php
_getBlock('image-block', 'content')
```

## 可以直接使用的常量

| 变量、常量            | 类型 | 说明                                                                 |
| --------------------- | ---- | -------------------------------------------------------------------- |
| Option::EMLOG_VERSION | 常量 | 获得当前emlog版本号                                                  |
| ROLE                  | 常量 | 当前用户角色（用户组）：admin 管理员、writer 注册用户 、visitor 访客 |
| ROLE_ADMIN            | 常量 | admin 管理员                                                         |
| ROLE_WRITER           | 常量 | writer 注册用户                                                      |
| ROLE_VISITOR          | 常量 | visitor 访客                                                         |
| UID                   | 常量 | 当前用户UID                                                          |
| BLOG_URL              | 常量 | 站点URL                                                              |
| ISLOGIN               | 常量 | 是否登录状态，true登录 false未登录                                   |
| TEMPLATE_URL          | 常量 | 当前模板的URL eg: http://localhost:8080/content/templates/default/   |
| TEMPLATE_PATH         | 常量 | 当前模板的绝对路径 eg:/www/emlog/content/templates/default/          |

上面的常量可以通过下面的方式在模板中使用

```php
// 输出当前站点网址
<?= BLOG_URL ?>

```

## 常用配置信息获取

| 配置项                    | 获取示例                        |
| ------------------------- | ------------------------------- |
| 站点标题                  | Option::get('blogname')         |
| 站点副标题                | Option::get('bloginfo')         |
| 站点meta信息：title       | Option::get('site_title')       |
| 站点meta信息：description | Option::get('site_description') |
| 站点meta信息：keyword     | Option::get('site_key')         |
| 站点URL                   | Option::get('blogurl')          |

使用示例：

```php
// 输出当前站点名称
<?= Option::get('blogname')?>

```

## 常用方法&函数

可以直接使用的方法和函数：[通用方法和函数](/dev/develop_func.md)

## 异步请求

pro版本支持ajax异步请求并返回json格式信息，方便用户构建评论、弹窗登录、注册、找回密码等更友好的前台交互体验。

### 发布评论

- [详见API文档：发布评论](/api/api.md)

### 用户登录

- [详见API文档：用户登录](/api/api.md)

### 用户注册

- [详见API文档：用户注册](/api/api.md)

### 找回密码

- [详见API文档：找回密码](/api/api.md)

### 文章搜索

搜索路由：https://yoursite_domain/?keyword=emlog&sid=1

- keyword：搜索关键词
- sid：分类id，可选，填写后只搜索该分类下的文章，主题可以自行实现按分类搜索。


## 参考demo

emlog系统自带的默认主题就是最好的演示demo，可以基于该主题进行修改来开发自己的主题，或者参考该主题的部分元素和文件。
默认模板所在目录: content/templates/default

## 开源发布

如果你开发的模板在 Github 开源，那请为仓库添加topic： [emlog-template](https://github.com/topics/emlog-template)

## 发布到应用商店

制作好的主题，经过测试后就可以打包发布到官网的应用商店了。

1. 打包：来到content\templates 目录下，找到模板的文件夹，直接使用zip压缩工具打包这个文件夹（注意不要进到文件夹内打包），并将打包后的压缩包命名为
   ：模板英文别名.zip，如 default.zip
2. 发布：登录官网emlog.net - 我的 - 应用开发 - 发布模板，按照提示填写必要的信息即可发布，等待审核。
