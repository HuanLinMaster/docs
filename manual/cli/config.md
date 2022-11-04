# 配置文件

每个 Koishi 应用都有一个配置文件，它管理了应用及其插件的全部配置。默认情况下配置文件的格式为 [YAML](https://en.wikipedia.org/wiki/YAML)，它是一种易于阅读和编辑的文本格式，你可以用任何文本编辑器打开。

::: tip
如果你不了解 YAML 的语法，请不要随意修改配置文件，否则将可能导致 Koishi 应用无法运行。你可以在[这篇教程](https://www.runoob.com/w3cnote/yaml-intro.html)中学习 YAML 的语法。
:::

## 根目录位置

配置文件所在的目录叫**根目录**。根据你的安装方式，根目录的位置可能不同：

- 模板项目：你创建的项目目录
- zip：解压目录下 `data/instances/default`
- msi：`C:/Users/你的用户名/AppData/Roaming/Il Harper/Koishi/data/instances/default`
- pkg：`~/Library/Application Support/Il Harper/Koishi/data/instances/default`

配置文件是根目录下名为 `koishi.yml` 的文件。当你遇到问题时，开发者可能会要求你提供配置文件的内容。此时去上面的地方找就好了。

## 理解配置文件

尝试打开配置文件，你会发现它的内容大致如下：

```yaml
# 全局设置
host: localhost
port: 5140

# 插件列表
plugins:
  # group 表示这是一个插件组
  group:console:
    # 波浪线前缀表示一个不启用的插件
    ~auth:
    console:
    logger:
    insight:
    market:
      # 以缩进的方式显示插件的配置项
      registry:
        endpoint: https://registry.npmmirror.com

  # 这里是一些零散的插件
  github:
  dialogue:
```

如果你使用过控制台，你就会发现，配置文件的结构与「插件配置」页面基本是一致的。具体而言，配置文件中包含三种内容：

### 全局设置

全局设置对应于配置文件中 `plugins:` 一行以上的部分。这里会包含一些最基础的配置项，例如网络设置、指令前缀、默认权限等。修改这里的配置项，会影响整个 Koishi 应用的行为而非某个插件。你可以在 [这个页面](../../api/core/app.md) 了解全部的全局设置。

### 插件配置

`plugins` 是一个 YAML 对象，它的每一个键对应于插件的名称，而值则对应于插件的配置。当没有进行配置时，值可以省略 (或者写成 `{}`)。当存在配置时，值需要在插件的基础上缩进并写在接下来的几行中。例如：

```yaml koishi.yml
plugins:
  dialogue:
    # 这里是 koishi-plugin-dialogue 的配置
    context:
      enable: true
```

### 插件名称

插件名称通常对应于插件发布时的包名。例如：

- `market` 对应于官方插件 `@koishijs/plugin-market`
- `dialogue` 对应于社区插件 `koishi-plugin-dialogue`

除了插件的包名外，插件名称还可以拥有一个可选的前缀 (`~`) 和后缀 (`:xxx`)。插件名称前的波浪线 (`~`) 表示该插件不会被启用。插件名称后的冒号后是插件的别名，当某个插件需要存在多组配置时这会非常有用。

### 插件组

你可以将插件组理解为一个名为 `group` 的特殊插件。它的语法与 `plugins` 一致，都是一个包含了插件名称和插件配置的 YAML 对象。使用插件组不仅能更好地帮助你整理插件，还能批量控制其中插件的行为。插件组也支持嵌套，例如：

```yaml koishi.yml
plugins:
  group:official:
    group:console:
      # 两层嵌套插件组下的 market 插件
      market:
```

## 编辑配置文件

当你启动 Koishi 应用时，Koishi 会读取上述配置文件并加载所需的插件；而当你在「插件配置」页面中修改了某些配置，Koishi 也会自动将这些改动写入配置文件。

绝大多数的功能都可以通过「插件配置」页面来完成，但目前尚有一些功能没有做好相应的交互界面，这时你就需要手动修改配置文件了。你需要做的有以下几步：

1. 关闭当前 Koishi 应用
2. 打开配置文件进行编辑
3. 保存配置文件后再次启动 Koishi 应用

## 使用更多格式

::: warning
我们并不推荐使用高级语言来编写配置文件，因为动态的配置无法支持环境变量、配置热重载和插件市场等特性。
:::

Koishi 默认支持的配置文件后缀名包括 `.yml`、`.yaml`、`.json`、`.js`、`.ts`。这意味着你也可以使用其他类型的配置文件。

除此以外，结合命令行工具的 `-r` 参数，你还可以扩展配置文件的格式。例如，如果你喜欢 CoffeeScript，你可以这样修改你的启动脚本：

```json title=package.json
{
  "scripts": {
    "start": "koishi start -r coffeescript/register -r yml-register"
  }
}
```

这样你就可以将配置写在 `koishi.coffee` 中啦~

## 环境变量插值

你可以通过插值语法在配置文件中使用环境变量。例如：

```yaml title=koishi.yml
plugins:
  adapter-discord:
    bots:
      - token: ${{ env.DISCORD_TOKEN }}
```

当项目启动时，会将环境变量中的值替换进去。

除了系统提供的环境变量外，Koishi 还原生支持 [dotenv](https://github.com/motdotla/dotenv)。你可以在当前目录创建一个 `.env` 文件，并在里面填写你的环境变量。这个文件已经被包含在 `.gitignore` 中，你可以在其中填写隐私信息 (例如账号密码) 而不用担心被上传到远端。例如在上面的例子中你就可以这样写：

```sh .env
DISCORD_TOKEN = xxx
```