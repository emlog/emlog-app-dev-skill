# 🧩 插件开发指南

emlog支持插件机制，这样使得开发者可以方便地向系统中添加自己需要的功能。

## 实现原理

在emlog整个运行过程中我们设定了一些动作事件，遇到这些事件时emlog会自动的调用插件绑定到该事件的上的所有插件函数，从而实现插件的功能。

## 挂载点函数：doAction

doAction 函数内置于emlog核心代码中，就是所谓的插件挂载点。

```php
//这是首页head头的挂载点，首页加载的时候会执行该挂载点上挂载的插件函数。
doAction('index_head') 
```

## 插件挂载: addAction

addAction 用于插件向挂载点挂载自身函数，写在插件文件中。 有两个参数：挂载点名称 和 插件自身函数名称。

```php
// 插件的 add_some_style 函数挂载到系统的 index_head 挂载点上，只要系统执行到 index_head 挂载点时,就会调用 add_some_style 函数.

addAction('index_head','add_some_style');

function add_some_style() {
   // 添加一些样式等操作
}
```

## 开发规范

### 文件结构

- 插件目录：/content/plugins，插件目录下每一个文件夹即为一个插件
- 插件英文别名：如系统自带的小贴士插件英文别名为： tips，仅识别 插件英文别名/插件英文别名.php” 目录结构的插件，如： tips/tips.php

| 文件             | 说明                                             |
| ---------------- | ------------------------------------------------ |
| xxx.php          | 插件主文件                                       |
| xxx_callback.php | 事件回调相关函数文件                             |
| xxx_setting.php  | 插件后台设置页面（仅管理员可见）                 |
| xxx_user.php     | 插件后台设置页面（所有人可见）                   |
| xxx_show.php     | 插件前台页面                                     |
| preview.jpg      | 插件图标，用于后台插件列表展示，尺寸：75x75 像素 |

上面表格中的 xxx 为插件英文别名，下面有插件文件的详细介绍。

### 插件主文件

插件文件夹下 插件名.php 的文件即为插件主要文件，例如：默认的tips插件,其文件夹名称为 tips, 插件主文件名称为 tips.php

tips.php 文件开头注释内容是插件的必要信息，该信息会显示在后台插件管理界面，务必完整填写。参考如下：

```php
<?php
/*
Plugin Name: 小贴士
Version: 3.0
Plugin URL:https://www.emlog.net/plugin/detail/xxx
Description: 在后台首页展示一句使用小提示，也可作为插件开发的demo。
Author: emlog
Author URL: https://www.emlog.net
*/
```

:::tip
其中 Plugin URL 和 Author URL 请使用官网 emlog.net 的应用链接和作者页，其他非官网链接不会在后台插件列表展示超链接。
:::

### 事件回调

在emlog后台的插件管理页面，用户可以开启插件、关闭插件、删除插件，还可以更新插件。这些操作有的会触发对应的回调函数。
开发者可以给插件添加文件： pluginname_callback.php 来定义特定事件的回调函数，来实现插件初始化、插件数据清理、数据结构更新等操作。

| 事件     | 触发函数        |
| -------- | --------------- |
| 开启插件 | callback_init() |
| 删除插件 | callback_rm()   |
| 更新插件 | callback_up()   |

示例：

tips_callback.php

```php
<?php
!defined('EMLOG_ROOT') && exit('access denied!');

// 插件开启时调用，可用于初始化配置
function callback_init() {
	$plugin_storage = Storage::getInstance('plugin_name');
	$r = $plugin_storage->getValue('key');
	if (empty($r)) {
		$default_data = [
			'ip'      => [],
			'time'    => [],
			'attempt' => [],
		];
		$plugin_storage->setValue('temp', json_encode($default_data), 'string');
	}
}

// 插件删除时调用，可用于数据清理
function callback_rm() {
	$plugin_storage = Storage::getInstance('plugin_name'); //使用插件的英文名称初始化一个存储实例
	$ak = $plugin_storage->deleteAllName('YES'); //删除此插件创建的所有数据, 请传入大写的"YES"来确认删除。
}

// 插件更新时调用，可用于数据库变更等
function callback_up() {
    ...
}
```

#### ☘️ 绿色插件

使用事件回调机制打造绿色插件，所谓绿色插件要做到：

1. 插件启动和使用中不修改、添加、删除核心数据表字段
2. 安装插件不需要额外添加插件自定义的挂载点，均采用官方预留的挂载点（个别主题缺少挂载点，可以引导用户添加官方挂载点）
3. 插件删除时清理掉所有该插件的数据，包括自建的数据库表以及配置信息

### 插件后台设置页面（仅管理员可见）

如果你想让插件在后台有一个设置页面，可以:

1. 在插件中添加文件： pluginname_setting.php
2. 该文件内要包含名为 plugin_setting_view 的函数，其中可以输出设置内容
   此时插件的后台配置地址为：https://yourdomain/admin/plugin.php?plugin=pluginname
3. 插件设置界面可以直接基于 [Bootstrap4](https://v4.bootcss.com/docs/components/forms/) 构建，请参考默认的小贴上插件

### 插件后台功能页面（所有用户均可见）

如果你想让插件在后台有一个功能页面，可以:

1. 在插件中添加文件： pluginname_user.php
2. 该文件内要包含名为 plugin_user_view 的函数，其中可以输出功能内容
   此时插件的后台功能地址为：https://yourdomain/admin/plugin_user.php?plugin=pluginname
3. 插件设置界面可以直接基于 [Bootstrap4](https://v4.bootcss.com/docs/components/forms/) 构建，请参考默认的小贴上插件

该页面可以用来构建一些给普通注册用户使用的后台功能，比如文章收藏插件就使用了该特性。

### 插件前台页面

如果想让插件在前台输出一个页面，可以在插件中添加文件： pluginname_show.php
此时插件的前台显示地址为：https://yourdomain/?plugin=pluginname 或者 https://yourdomain/plugin/pluginname （需要开启伪静态规则）
这样就可以在 pluginname_show.php 文件中构建插件的前台展示页面了。

### 命名规则

#### 插件英文别名

请以小写的英文字母、数字、下划线(_)、横杠(-) 组合而成，且只能以字母作为开头

如: tips、 em_ai

#### 插件内自定义函数命名

函数采用 "插件英文别名_" 作为前缀来命名，如：tips_init，其中 tips 为插件英文别名。

```php
function tips_init() {
    global $array_tips;
    $i = mt_rand(0, count($array_tips) - 1);
    $tip = $array_tips[$i];
    echo "<div id=\"tip\"> $tip</div>";
}
```

采用这样的命名方式可以避免与其他插件的函数出现冲突.

### 插件文件名称

- 插件文件命名推荐使用自定义前缀，避免和其他插件冲突，如： myprefix_tips ，其中 myprefix_ 为自定义前缀。
- 插件主文件名称必须与插件所在文件夹名称相同，如:

```
myprefix_tips/
      myprefix_tips.php
      myprefix_tips_setting.php
      myprefix_tips_callback.php
```

### 安全性

在插件文件开头增加限制语句 插件函数文件需要增加:

```php
!defined('EMLOG_ROOT') && exit('access denied!');
```

如果不增加该语句,那么直接访问插件的程序文件php会爆出博客的物理路径,对博客的安全造成威胁。

如果你的插件需要接收一些参数,请务必严格过滤每一个变量的数据. 例如：获取外部获取一个int型的参数，$id = $_GET['id'];
这样写是不安全的，要改为：$id = intval($_GET['id']);

如果是一个字符型的参数，$action = $_GET['action']; 这样写也是不安全的， 要改为：$action = addslashes($_GET['action']);

## 插件数据存储（1）：Storage

插件如果需要保存设置等信息，可以使用系统提供的Storage类来完成数据的存储读取，数据会被存储在MySQL数据库的storage表里。
该存储方式适合存储 key-value 类型的键值对数据，如插件的设置项等。

### 写入数据

```php
	$plugin_storage = Storage::getInstance('plugin_name');//使用插件的英文名称初始化一个存储实例
	$plugin_storage->setValue('key', 'xxx'); // 设置key的值为 xxx，最大可以存储长度为65,535个字符的数据。
```

设置写入数据类型：数据存储还支持第三个参数指定存储数据的类型，读取时会返回相应的数据类型，目前支持4种类型，默认是string类型。

- string //读取时返回string
- number // 读取时返回float类型
- boolean // 读取时返回布尔类型
- array // 返回数组

如：

```php
	$plugin_storage = Storage::getInstance('plugin_name');
	$data = ['name' => 'tom', 'age' => 19];
	$plugin_storage->setValue('key', $data, 'array'); //存储为数组类型，这样数组会被序列化后存入数据库，读取的时候会被自动反序列化。
```

### 读取数据

```php
    $plugin_storage = Storage::getInstance('plugin_name'); //使用插件的英文名称初始化一个存储实例
	$ak = $plugin_storage->getValue('key'); // 读取key值
	
	// 如果读取的是一个数组，请先判断读取到的值是否为空，避免出现 warning 报错
	$config = $plugin_storage->getValue('config');
    $test_key = !empty($config) ? $config['test_key'] : '';
```

### 清理删除数据

```php
    $plugin_storage = Storage::getInstance('plugin_name'); //使用插件的英文名称初始化一个存储实例
	$ak = $plugin_storage->deleteName('key') // 删除此插件创建的一行名为key的数据
	$ak = $plugin_storage->deleteAllName('YES'); //删除此插件创建的所有数据, 请传入大写的"YES"来确认删除 ，一般用于插件删除回调函数。
```

## 插件数据存储（2）：自建数据表

如果上面的 Storage 数据存储方式无法满足更复杂的数据结构存储要求，插件可以自建数据库表存储数据。

### 创建插件数据表

利用上面提到的【事件回调】机制在自定义的 callback 函数中实现创建插件自己的表，下面给出一个简单的示例。

```php
<?php
!defined('EMLOG_ROOT') && exit('access denied!');

//  初始化插件数据表
function callback_init() {
    $db = MySql::getInstance();
    $charset = 'utf8mb4';
    $type = 'InnoDB';
    $table = DB_PREFIX . 'stats';
    $add = "ENGINE=$type DEFAULT CHARSET=$charset;";
    $sql = "
	CREATE TABLE IF NOT EXISTS `$table` (
		`id` int(11) unsigned NOT NULL AUTO_INCREMENT,
		`gid` int(11) unsigned NOT NULL,
		`title` varchar(255) NOT NULL default '',
		`views` bigint(11) unsigned NOT NULL default 0,
		`comments` bigint(11) unsigned NOT NULL default 0,
		`date` date NOT NULL,
		PRIMARY KEY (`id`),
		UNIQUE KEY `date_gid` (`date`,`gid`)
	)" . $add;
    $db->query($sql);
}

// 插件删除时删除插件数据表
function callback_rm() {
	$sql = "DROP TABLE IF EXISTS `" . DB_PREFIX . "stats`";
    $db = MySql::getInstance();
    $db->query($sql);
}

```

### 自建数据表完整示例

下面PHP代码是一个完整的维护插件自建数据库表的 callback 示例，可以直接用于自己插件 xxxx_callback.php ，修改对应建表语句即可。

```php

<?php
/**
 * 插件回调
 */
!defined('EMLOG_ROOT') && exit('error!');

/**
 * 插件激活回调
 */
function callback_init(){
    Init_Database_Callback::instance()->pluginInit();
}

/**
 * 插件更新回调
 */
function callback_up(){
    Init_Database_Callback::instance()->pluginUp();
}

/**
 * 插件删除回调
 */
function callback_rm(){
    Init_Database_Callback::instance()->pluginRm();
}

/**
 * 数据表操作类
 */
class Init_Database_Callback {
    //实例
    private static $instance;
    //数据库实例
    private $db;
    //数据表配置
    private $option = [
        //数据表名称
        "tableName"             => DB_PREFIX."toEverColor_list",
        //卸载插件是否删除数据表 - true/false 对应 删除/不删除 默认为false（不删除）
        "checkDeleteTable"      => false,
        //数据表字段信息，字段=>sql语句，请勿写错，程序根据这个来创建和检测字段
        "fieldData"             => [
            "id"            => "`id` int(50) NOT NULL AUTO_INCREMENT",
            "gid"           => "`gid` int(50) NOT NULL COMMENT '文章ID'",
            "color"         => "`color` varchar(200) DEFAULT NULL COMMENT '颜色'",
            "weight"        => "`weight` enum('n','y') DEFAULT 'n' COMMENT '是否加粗（默认不加粗）'",
            "font_size"     => "`font_size` int(50) DEFAULT NULL COMMENT '字号'",
            "line_through"  => "`line_through` enum('n','y') DEFAULT 'n' COMMENT '删除线'",
        ]
    ];

    /**
     * 私有构造函数，保证单例
     */
    private function __construct(){
        //数据库实例赋值
        $this->db = Database::getInstance();
    }

    /**
     * 单例入口
     */
    public static function instance(){
        if (self::$instance === null) {
            self::$instance = new self();
        }
        return self::$instance;
    }

    /**
     * 检测数据表是否存在
     */
    public function checkDataTable() {
        if (isset($this->option['tableName'])) {
            $query = $this->db->query("SHOW TABLES LIKE '{$this->option['tableName']}'");
            if ($this->db->num_rows($query) > 0) {
                return true;
            }
            return false;
        }
        return false;
    }

    /**
     * 检测数据表中字段是否存在 - 指定字段名
     */
    public function checkDataField($fieldName = '') {
        if (!empty($fieldName) && $this->checkDataTable()) {
            $query = $this->db->query("SHOW COLUMNS FROM {$this->option['tableName']} LIKE '{$fieldName}'");
            if ($this->db->num_rows($query) > 0) {
                return true;
            }
            return false;
        }
        return false;
    }

    /**
     * 数据表创建函数
     */
    private function addDataTable() {
        if (!empty($this->option) && is_array($this->option) && isset($this->option['fieldData']) && is_array($this->option['fieldData'])) {
            $sql = "CREATE TABLE IF NOT EXISTS {$this->option['tableName']} (";
            foreach ($this->option['fieldData'] as $field => $fieldSql) {
                $sql .= $fieldSql . ',';
            }
            $sql .= " PRIMARY KEY (`id`)) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='标题改色表';";
            $this->db->query($sql);
        }
    }

    /**
     * 检测数据表字段是否存在，不存在则创建字段
     */
    private function addDataTableField() {
        if (!empty($this->option) && is_array($this->option) && isset($this->option['fieldData']) && is_array($this->option['fieldData'])) {
            $preForeachData = '';
            foreach ($this->option['fieldData'] as $field => $fieldSql) {
                if (!$this->checkDataField($field)) {
                    $after = !empty($preForeachData) ? " AFTER {$preForeachData}" : '';
                    $this->db->query("ALTER TABLE {$this->option['tableName']} ADD COLUMN {$fieldSql}{$after}");
                }
                $preForeachData = $field;
            }
        }
    }

    /**
     * 插件启用执行函数
     */
    public function pluginInit() {
        if ($this->checkDataTable()) {
            $this->addDataTableField();
        } else {
            $this->addDataTable();
        }
    }

    /**
     * 插件更新执行函数
     */
    public function pluginUp() {
        $this->addDataTableField();
    }

    /**
     * 插件卸载执行函数
     */
    public function pluginRm() {
        if (isset($this->option['checkDeleteTable']) && $this->option['checkDeleteTable'] === true) {
            $this->db->query("DROP TABLE {$this->tableName}");
        }
    }
}

```

### 读取插件数据表

```php
<?php
   // 读取插件数据
   function getDetail($id) {
   $db = MySql::getInstance();
   $row = $db->once_fetch_array("SELECT * FROM " . DB_PREFIX . "stats WHERE id = " . $id);

    $row['xxxx']
    ……
}

```

## 插件数据存储（3）：扩展核心表字段

尚未支持，目前可以使用上面两种方式替代，后续会支持。

#### 🔴 重要提示

:::danger
插件不得修改emlog核心数据库表及字段，包括向核心表增加字段。特别是增加没有默认值的字段。
:::

## 挂载点类型

### 1、插入式挂载

* 执行原理：顺序执行挂在钩子上的函数,支持多参数
* 适用场景：在挂载点位置插入指定内容，或者执行某些动作。

```php
// 挂载点名称：adm_main_top
doAction('adm_main_top');
```

```php
// 插件开发例子：在如上挂载点 "adm_main_top"，挂载tips函数，实现管理后台插入一句话。
addAction('adm_main_top', 'tips');
function tips() {
	echo "<div>世界你好</div>";
}
```

带有参数的挂载点，参数会按照顺序传递给挂载在上面的函数。如下面的例子

```php
// 挂载点名称：save_log，保存文章的挂载点，带有多个参数，包括文章ID 等
doAction('save_log', $blogid, $pubPost, $logData)
```

```php
// 插件开发例子：将函数test_foo挂载到如上 save_log 挂载点，并接收传递的参数 $blogid, $pubPost, $logData
addAction('save_log', 'test_foo');
function test_foo($blogid, $pubPost, $logData) {
   var_dump($blogid, $pubPost, $logData); // 可以尝试打印参数，来确定是否正确接收到值
}
```

#### 挂载点列表（插入式挂载）

##### 后台相关挂载点

| 挂载点                                             | 所在文件                      | 描述                                                                                                                                                                                             |
| -------------------------------------------------- | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| doAction('adm_main_top')                           | admin/views/header.php        | 后台首页顶部区域扩展，官方小贴士插件就使用了该挂载点                                                                                                                                             |
| doAction('adm_head')                               | admin/views/header.php        | 后台头部扩展：可以用于增加后台css样式、加载js等                                                                                                                                                  |
| doAction('adm_menu')                               | admin/views/header.php        | 后台侧边栏一级菜单扩展，仅管理员可见。                                                                                                                                                           |
| doAction('login_head')                             | admin/views/user_head.php     | 登录、注册页面头部扩展，可用于添加登录样式css等。                                                                                                                                                |
| doAction('user_menu')                              | admin/views/uc_header.php     | 个人中心顶部菜单扩展，仅注册用户可见。                                                                                                                                                           |
| doAction('adm_footer')                             | admin/views/footer.php        | 后台底部扩展：可以用于增加后台js等                                                                                                                                                               |
| doAction('adm_main_content')                       | admin/views/index.php         | 管理员后台首页信息模块扩展                                                                                                                                                                       |
| doAction('user_main_content')                      | admin/views/uc_index.php      | 注册用户后台首页信息模块扩展                                                                                                                                                                     |
| doAction('login_ext')                              | admin/views/signin.php        | 用户登录页扩展：可以用于增加QQ登录等第三方登录按钮                                                                                                                                               |
| doAction('signup_ext')                             | admin/views/signup.php        | 用户注册页扩展：可以用于增加QQ登录等第三方登录、注册按钮                                                                                                                                         |
| doAction('login_succeed', $uid);                   | admin/account.php             | 登录成功，可用于自定义登录成功跳转地址等                                                                                                                                                         |
| doAction('login_fail');                            | admin/account.php             | 登录失败                                                                                                                                                                                         |
| doAction('register_succeed');                      | admin/account.php             | 注册成功                                                                                                                                                                                         |
| doAction('delete_user');                           | admin/user.php                | 删除用户                                                                                                                                                                                         |
| doAction('adm_comment_display')                    | admin/views/comment.php       | 后台评论显示扩展，可以用于查询评论人ip所在地域                                                                                                                                                   |
| doAction('blogger_ext')                            | admin/views/blogger.php       | 后台个人信息编辑页面扩展点                                                                                                                                                                       |
| doAction('adm_sort_add')                           | admin/views/sort.php          | 后台添加、编辑分类扩展点                                                                                                                                                                         |
| doAction('adm_writelog_bar')                       | admin/views/article_write.php | 写文章页：标题下方区域，开发规范：1、仅支持文字链接，且不超过8个汉字，2、链接样式和【上传插入图片】链接保持一致（包括颜色、字体大小、 icon 大小），3、交互采用点击展开收起，或者点击弹出模态窗口 |
| doAction('adm_writelog_head')                      | admin/views/article_write.php | 写文章页：摘要下方区域                                                                                                                                                                           |
| doAction('adm_writelog_side')                      | admin/views/article_write.php | 写文章页：右侧边栏下方区域                                                                                                                                                                       |
| doAction('save_log', $blogid, $pubPost, $logData); | admin/article_save.php        | 发布文章、修改文章扩展点，传递文章ID,是否直接发布、文章完整数据参数                                                                                                                              |
| doAction('adm_write_page_side')                    | admin/views/page_crate.php    | 创建页面：右侧边栏下方区域                                                                                                                                                                       |
| doAction('save_page', $pageId, $logData);          | admin/page.php                | 新建和修改页面扩展点，传递页面ID、页面数据参数                                                                                                                                                   |
| doAction('del_log', $key)                          | admin/article.php             | 删除文章操作扩展点                                                                                                                                                                               |
| doAction('approved_log', $gid);                    | admin/article.php             | 文章通过审核扩展点                                                                                                                                                                               |
| doAction('rejected_log', $gid, $feedback);         | admin/article.php             | 驳回文章扩展点                                                                                                                                                                                   |
| doAction('comment_reply', $commentId, $reply)      | admin/comment.php             | 回复评论扩展点                                                                                                                                                                                   |
| doAction('post_note')                              | admin/twitter.php             | 微语笔记发布扩展点                                                                                                                                                                               |
| doAction('del_media', $filepath);                  | admin/media.php               | 删除资源文件扩展点，可用于云存储插件删除远程文件                                                                                                                                                 |
| doAction('save_sort', $sid, $sort_data);           | admin/sort.php                | 创建、保存分类扩展点                                                                                                                                                                             |
| doAction('attach_upload')                          | include/lib/common.php        | 扩展附件上传，如增加图片水印效果等                                                                                                                                                               |

##### 前台相关挂载点

| 挂载点                             | 所在文件                                   | 描述                                                                   |
| ---------------------------------- | ------------------------------------------ | ---------------------------------------------------------------------- |
| doAction('comment_post')           | include/controller/comment_controller.php  | 发表评论扩展点（写入评论前）。可用于垃圾评论防范                       |
| doAction('comment_saved')          | include/model/comment_model.php            | 发表评论扩展点（写入评论后）。用于发布评论成功的后续操作，如发通知邮件 |
| doAction('log_related',$logData)   | content/templates/default/echo_log.php     | 前台模板：文章详情页面扩展点、用于增加文章相关内容                     |
| doAction('index_head')             | Content/templates/default/header.php       | 前台模板：头部扩展：可以用于增加前台css样式、加载js等                  |
| doAction('index_footer')           | content/templates/default/footer.php       | 前台模板：底部扩展点                                                   |
| doAction('index_loglist_top')      | content/templates/default/log_list.php     | 前台模板：文章列表顶部扩展点，如显示公告等                             |
| doAction('rss_display')            | rss.php                                    | Rss输出扩展                                                            |
| doAction('page_not_found')         | include/lib/common.php                     | 文章、页面不存在的扩展点，方便开发404重定向到首页、自定义路由等功能    |
| doAction('log_direct_link', $link) | include/controller/log_controller.php      | 文章直接跳转链接扩展点，方便开发跳转中间页面等功能                     |
| doAction('download_resource', $r); | include/controller/download_controller.php | 资源下载扩展点，方便开发下载扣减积分等下载权限验证的功能               |

示例：

```php
function tips_css() {
    echo "<style>
    #tip{
        background:url(../content/plugins/tips/icon_tips.gif) no-repeat left 3px;
        padding:3px 18px;
        margin:5px 0px;
        font-size:12px;
        color:#999999;
    }
    </style>\n";
}
// 在管理后台 head 头部加入 css 样式
addAction('adm_head', 'tips_css');
```

### 2、单次接管式挂载

* 执行原理：执行挂在钩子上的第一个函数，仅执行一次，接收输入input，且会修改传入的变量$ret）
* 适用场景：替换核心的函数，如接管核心的文件上传函数，将上传本地改为上传云端

```php
// 挂载点名称：upload_media，上传文件挂载点，带有参数$attach，$ret
doOnceAction('upload_media', $attach, $ret);
```

```php
// 插件开发例子：将函数upload2qiniu 挂载到upload_media挂载点
addAction('upload_media', 'upload2qiniu');

function upload2qiniu($attach, &$result) {

}
```

#### 挂载点列表（单次接管式挂载）

| 挂载点                                               | 所在文件               | 描述                                         |
| ---------------------------------------------------- | ---------------------- | -------------------------------------------- |
| doOnceAction('upload_media', $attach, $ret);         | admin/media.php        | 资源文件上传挂载点，可以用于云存储插件开发   |
| doOnceAction('get_Gravatar', $email, $gravatar_url); | include/lib/common.php | 评论人头像挂载点，可以用于改变头像的生成方式 |

### 3、轮流接管式挂载

* 执行原理：执行挂在钩子上的所有函数，上一个执行结果作为下一个的输入，且会修改传入的第二个变量值。
* 适用场景：对指定内容进行修改，eg：不同插件对文章内容进行不同的修改替换。

```php
// 挂载点名称：article_content_echo，文章内容展示挂载点，带有参数$log_content, $log_content
// 第一个参数 $logData：输入原始的文章数据，数组结构包括标题、内容、文章id等信息
// 第二个参数 $logData：被插件修改后的文章数据，完成内容变量的覆盖替换。
doMultiAction('article_content_echo', $logData, $logData);
```

#### 挂载点列表（轮流接管式挂载）

| 挂载点                                                             | 所在文件                              | 描述                                                    |
| ------------------------------------------------------------------ | ------------------------------------- | ------------------------------------------------------- |
| doMultiAction('article_content_echo', $log_content, $log_content); | include/controller/log_controller.php | 文章内容输出挂载点，可用于文章内容替换                  |
| doMultiAction('pre_save_log', $logData, $logData);                 | include/admin/article_save.php        | 文章数据预处理挂载点，可用于文章内容替换，xss过滤等操作 |

示例：

```php
// 将文章内容中的 aaaa 替换为 bbbb，并将替换后的文章内容存入文章数组变量 $result
function content_replace($logData, &$result) {
    $result['log_content'] = str_replace('aaaa', 'bbbb', $logData['log_content']);
}
addAction('article_content_echo', 'content_replace');
```

## 常用方法&函数

可以直接使用的方法和函数：[通用方法和函数](/dev/develop_func.md)

## 参考demo

emlog系统自带的tips插件，也是官方提供的插件演示demo，可以基于该插件进行修改来开发自己的插件。
tips插件所在目录: content/plugins/tips

## 开源发布

如果你开发的插件在 Github 开源，那请为仓库添加topic： [emlog-plugin](https://github.com/topics/emlog-plugin)

## 发布到应用商店

制作好的插件，经过测试后就可以打包发布到官网的应用商店了。

1. 打包：来到content\plugins 目录下，找到该插件的文件夹，直接使用zip压缩工具打包这个文件夹（注意不要进到文件夹内打包），并将打包后的压缩包命名为
   ：插件英文别名.zip，如：tips.zip
2. 发布：登录官网emlog.net - 我的 - 应用开发 - 发布插件，按照提示填写必要的信息即可发布，等待审核。
