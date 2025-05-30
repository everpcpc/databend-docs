---
title: Grafana
---

[Grafana](https://grafana.com/) 是一个监控仪表盘系统，是由 Grafana Labs 开发的开源监控工具。它允许我们提供要监控的数据，并生成各种可视化效果，从而大大简化了监控的复杂性。此外，它还具有警报功能，可以在系统出现问题时发送通知。Databend 和 Databend Cloud 可以通过 [Grafana Databend Data Source Plugin](https://github.com/databendlabs/grafana-databend-datasource) 与 Grafana 集成。

## 教程：与 Grafana 集成

本教程将指导你使用 Grafana Databend Data Source Plugin 将 Databend / Databend Cloud 与 Grafana 集成的过程。

### 步骤 1. 设置环境

在开始之前，请参考官方安装指南安装 Grafana：[https://grafana.com/docs/grafana/latest/setup-grafana/installation](https://grafana.com/docs/grafana/latest/setup-grafana/installation)。

在本教程中，你可以选择与 Databend 或 Databend Cloud 集成：

- 如果你选择与本地 Databend 实例集成，请按照 [部署指南](/guides/deploy) 部署它（如果还没有）。
- 如果你更喜欢与 Databend Cloud 集成，请确保你可以登录你的帐户并获取计算集群的连接信息。有关更多详细信息，请参阅 [连接到计算集群](/guides/cloud/using-databend-cloud/warehouses#connecting)。

### 步骤 2. 修改 Grafana 配置

将以下行添加到你的 `grafana.ini` 文件中：

```ini
[plugins]
allow_loading_unsigned_plugins = databend-datasource
```

### 步骤 3. 安装 Grafana Databend Data Source Plugin

1. 在 [GitHub Release](https://github.com/databendlabs/grafana-databend-datasource/releases) 上找到最新版本。

2. 获取插件 zip 包的下载 URL，例如 `https://github.com/databendlabs/grafana-databend-datasource/releases/download/v1.0.2/databend-datasource-1.0.2.zip`。

3. 获取 Grafana 插件文件夹并将下载的 zip 包解压缩到其中。

```shell
curl -fLo /tmp/grafana-databend-datasource.zip https://github.com/databendlabs/grafana-databend-datasource/releases/download/v1.0.2/databend-datasource-1.0.2.zip
unzip /tmp/grafana-databend-datasource.zip -d /var/lib/grafana/plugins
rm /tmp/grafana-databend-datasource.zip
```

4. 重新启动 Grafana 以加载插件。

5. 导航到 Grafana UI 中的 **Plugins** 页面，例如 `http://localhost:3000/plugins`，并确保已安装该插件。

![Plugins](/img/integration/grafana-plugins.png)
![Plugin detail](/img/integration/grafana-plugin-detail.png)

### 步骤 3. 创建数据源

1. 转到 `Add new connection` 页面，例如 `http://localhost:3000/connections/add-new-connection?search=databend`，搜索 `databend`，然后选择它。

2. 单击页面右上角的 **Add new data source**。

3. 输入你的 Databend 实例的 `DSN` 字段。例如，`databend://root:@localhost:8000?sslmode=disable`，或 `databend://cloudapp:******@tnxxxxxxx.gw.aws-us-east-2.default.databend.com:443/default?warehouse=xsmall-fsta`。

4. 或者，输入 `SQL User Password` 字段以覆盖 `DSN` 字段中的默认密码。

5. 单击 **Save & test**。如果页面显示“Data source is working”，则表示已成功创建数据源。