# ZAP动作全扫描

运行OWASP ZAP [Full Scan]（https://www.zaproxy.org/docs/docker/full-scan/）的GitHub操作以执行

ZAP完全扫描操作针对指定的目标（默认情况下没有时间限制）运行ZAP蜘蛛，随后进行可选的Ajax蜘蛛扫描，然后进行完全活动扫描，然后报告结果。警报将作为GitHub问题维护在相应的存储库中。

**警告** 此操作将对目标网站进行攻击。您应仅扫描您有权测试的目标。在执行此操作之前，还应向托管公司和可能受到影响的任何其他服务（例如CDN）进行检查。ZAP还将提交可能导致[大量邮件]的表格（https://www.zaproxy.org/faq/how-can-i-prevent-zap-from-sending-me-1000s-of-emails-via-a -contact-us-form /），例如通过“与我们联系”或“评论”表单。

## 输入

### `target`

**Required** 要扫描的Web应用程序的URL。这可以是公共可用的Web应用程序，也可以是本地可访问的URL。

### `docker_name`

**Optional** 要执行的docker文件的名称。默认情况下，该操作运行ZAP的稳定版本。但是您可以配置参数以使用每周构建。

### `rules_file_name`

**Optional** 您还可以指定规则文件的相对路径，以忽略来自ZAP扫描的任何警报。确保在相关存储库中创建规则文件。下面显示了一个规则文件配置示例，请确保检出存储库 (actions/checkout@v2) 以将ZAP规则提供给扫描操作。

```tsv
10011	IGNORE	(没有安全标志的Cookie)
10015	IGNORE	(不完整或没有缓存控制和Pragma HTTP请求头)
``` 

### `cmd_options`

**Optional** 完整扫描脚本的其他命令行选项

### `issue_title`

**Optional** 要创建的GitHub issue的标题。

### `token`

**Optional** ZAP操作使用GitHub提供的默认操作令牌创建和更新完整扫描的问题，而无需创建专用令牌。确保在运行action（`secrets.GIT_TOKEN`）时使用GitHub的默认action令牌。

### `fail_action`

**Optional** 默认情况下，ZAP Docker容器将失败并显示[退出代码]（https://github.com/zaproxy/zaproxy/blob/efb404d38280dc9ecf8f88c9b0c658385861bdcf/docker/zap-full-scan.py#L31），如果它标识任何警报。如果您希望在ZAP在扫描期间识别任何警报时使GitHub Scan的状态失败，则将此选项设置为true。

## Example usage

** Basic **
```
steps:
  - name: ZAP Scan
    uses: zaproxy/action-full-scan@v0.2.0
    with:
      target: 'https://www.zaproxy.org/'
```

** Advanced **

```
on: [push]

jobs:
  zap_scan:
    runs-on: ubuntu-latest
    name: Scan the webapplication
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          ref: master
      - name: ZAP Scan
        uses: zaproxy/action-full-scan@v0.2.0
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          docker_name: 'owasp/zap2docker-stable'
          target: 'https://www.zaproxy.org/'
          rules_file_name: '.zap/rules.tsv'
          cmd_options: '-a'
```

## 本地化警报详细信息

ZAP是国际化的，警报信息可以多种语言提供

您可以通过`cmd_options`更改语言环境来更改此操作使用的语言，例如: `-z "-config view.locale=fr_FR"`

当前仅适用于“ owasp / zap2docker-weekly”或“ owasp / zap2docker-live” Docker镜像。

参见[https://github.com/zaproxy/zaproxy/tree/develop/zap/src/main/dist/lang](https://github.com/zaproxy/zaproxy/tree/develop/zap/src/main / dist / lang）表示当前支持的所有语言环境。

您可以通过[https://crowdin.com/project/owasp-zap](https://crowdin.com/project/owasp-zap）帮助改进ZAP翻译。
