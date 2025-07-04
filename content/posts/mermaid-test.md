+++
title = 'Mermaid图表测试'
date = '2024-07-04T20:30:00+08:00'
draft = false
tags = ['Mermaid', '图表', '测试']
categories = ['技术']
+++

# Mermaid图表测试

本文展示如何在Hugo博客中使用Mermaid图表。

## 流程图示例

{{< mermaid >}}
flowchart TD
    A[开始] --> B{是否已配置?}
    B -->|是| C[继续执行]
    B -->|否| D[配置环境]
    D --> C
    C --> E[完成]
{{< /mermaid >}}

## 时序图示例

{{< mermaid >}}
sequenceDiagram
    participant 浏览器
    participant 服务器
    浏览器->>服务器: 请求页面
    服务器-->>浏览器: 返回HTML
    浏览器->>服务器: 请求CSS/JS
    服务器-->>浏览器: 返回CSS/JS
    浏览器->>服务器: 请求数据API
    服务器-->>浏览器: 返回JSON数据
{{< /mermaid >}}

## 类图示例

{{< mermaid >}}
classDiagram
    class Animal {
        +String name
        +int age
        +makeSound()
    }
    class Dog {
        +fetch()
    }
    class Cat {
        +climb()
    }
    Animal <|-- Dog
    Animal <|-- Cat
{{< /mermaid >}}

## 甘特图示例

{{< mermaid >}}
gantt
    title 项目开发计划
    dateFormat  YYYY-MM-DD
    section 设计阶段
    需求分析        :done,    des1, 2023-01-01, 2023-01-05
    原型设计        :active,  des2, 2023-01-06, 3d
    section 开发阶段
    编码          :         dev1, after des2, 20d
    测试          :         dev2, after dev1, 10d
    section 发布阶段
    部署上线        :         rel1, after dev2, 2d
{{< /mermaid >}}

## 饼图示例

{{< mermaid >}}
pie title 网站访问来源
    "搜索引擎" : 45.2
    "社交媒体" : 30.5
    "直接访问" : 15.3
    "其他" : 9.0
{{< /mermaid >}}

## 使用说明

要在Hugo博客中使用Mermaid图表，需要：

1. 在`hugo.toml`中启用Mermaid支持
2. 创建自定义的`layouts/partials/extend_footer.html`加载Mermaid脚本
3. 创建`layouts/shortcodes/mermaid.html`短代码
4. 在文章中使用`{{</* mermaid */>}}`短代码

这样就可以轻松地在文章中添加各种图表了！
