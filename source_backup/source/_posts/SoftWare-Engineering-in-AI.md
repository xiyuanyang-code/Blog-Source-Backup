---
title: SoftWare Engineering in AI
date: 2025-11-08 14:48:34
excerpt: Lecture note for CS3604, new era of AI-Human Development for software-engineering. Handmade coding is about to become the spinning jenny of the new era.
index_img: /img/cover/handmade-coding.jpg
categories:
  - Artificial Intelligence
tags:
  - tutorial
  - software development
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# SoftWare Engineering in AI

{% note primary %}

**Serves as the lecture notes for SJTU-CS3604**.

{% endnote %}

## Introductions

软件工程是在现实生活中各类软件的构筑基石，包含上千个 feature，上百万行代码的大型软件的规划设计、开发、测试、部署、运维等需要成百上千人共同维护努力，是一项相当复杂的系统性工程。

随着大语言模型的迅速发展，基座模型的编码能力持续增长，在单个简单任务的场景下，AI 编码的速度、准确率、代码整洁度等方面显著超越人类水平，但是仍然在面对庞大 codebase、复杂需求，需要长程记忆和任务理解的复杂软件工程项目上无法胜任。

{% note primary %}

因此，在未来，程序员的使用不再是**手动编写代码**，而是**拆解需求和技术框架，明确到每一个原子需求，并且保持 Human in the loop**，保持对代码的理解和 Debug 能力。

It is the era of AI-driven programming.

{% endnote %}

在 AI 时代，软件工程会出现下面的变化趋势：

- 从编码为主导的开发导向到以需求为导向的开发导向
- 需求工程将会从**人类可读**的文档形态转向以**上下文工程**为主的形态。

## 软件工程的基本阶段

![软件工程的基本阶段](/images/software-base.png)

### 需求工程

产品经理：将从用户为导向的需求（我需要实现 XXX 功能）转向为技术人员为导向的需求。

具体而言，这个过程可以被分解为：

- 需求的获取 & 整理
- 需求的建模 & 转换
- 需求的撰写 & 验证 & 确认
- 需求的管理 & 变更

同时，需求本身存在层级性，可以生成一颗非常庞大的需求树。

- 需求的顶层主要是面向用户的接口
- 需求的底层是面向 AI 编程的接口设计问题
- 中间的需求设计**需求的拆解和合并**


### 软件设计

软件设计将需求转化为系统实现方案，决定了软件模块、接口、数据及其相互关系。

We need to design an **interface**:

- What I should provide
- How you will call the interface
- What the result you will ensure

For the intermediate steps, the code is programmed by AI!

### 软件测试

- 黑盒测试：只验证输入输出是否达到了预期
- 白盒测试：审查程序的具体接口和逻辑检查


{% note primary %}

从需求到测试：

- 业务需求（面向客户）
- 系统需求（用户端需求）
- 高层设计需求（比如不同模块的接口的设计）
- 底层的接口内部函数设计和编码

这四个不同层级的设计也有对应的测试实现！

- 底层设计：单元测试
- 高层设计：模块测试
- 系统需求：集成测试
- 业务需求：验收测试

{% endnote %}

#### Test-Driven Development

根据需求和设计出的接口实现测试脚本（黑盒测试）

### 软件维护

- 版本管理系统
  - 去中心化，本地化，面向多人协作
- 问题追踪系统
  - Issue Tracking Systems 问题追踪系统
  - One single issue focus on one atomic tasks!
    - 保证需求文档和软件代码能够实时同步，并且方便回滚回退，方便多人协作
- 持续集成系统（CICD）
  - 和问题追踪系统高度耦合
  - 降低风险，保证主干可以实时发布

以上三个自动化系统可以保证人类程序员在面对超长的 code-base 和 memory 的情况下可以提升效率，对 AI 来说，也同样如此。

## 软件工程的版本管理

I am quite familiar with Git and version control, thus we just skip that part!

## 需求测试和编码

### 测试驱动的开发和实践 Test Driven Development

在大语言模型，testcase 的设计天生为语言模型定义了良好的奖励机制，使语言模型的能力迅速上升。

在面向测试的 TDD 开发流程中，**根据需求和场景确定设计接口和生成鲁棒准确全面的测试样例**是开发的核心流程。（尤其是在 AI 时代）

### 软件需求测试

- 功能性需求：实现什么功能，验证可实现性
  - 方便验证，集成
- 非功能性需求：例如兼容性需求，速度需求，性能需求，成本需求等
  - 更复杂，更具体

#### 需求场景

$$S = <\textbf{Context}, \textbf{Action}, \textbf{ExcpectedOutcome}>$$

- Context 代表系统的初始状态和上下文
- Action 代表用户或系统执行的触发动作
- Then 代表系统应该产生的行为和输出

##### 需求场景描述语言

Gherkin 是一种结构化的自然语言（Structured Natural Language），用于行为驱动开发（BDD）中描述软件系统的行为。它让需求文档既能被人阅读，又能被测试工具 [Behave](https://behave.readthedocs.io/en/latest/tutorial/) 执行。

```Gherkin
Feature: showing off behave

  Scenario: run a simple test
      Given we have behave installed
      When we implement a test
      Then behave will test it for us!
```

变体需求场景（Variant Scenario）是指：在一个功能需求下，围绕同一主
目标（Main Scenario），针对不同输入条件、异常情况或交互路径而产生的
分支场景（Alternative Scenarios）或异常场景（Exception Scenarios）。

{% note primary %}

**变体需求场景**有点类似于 Rust 中的 Happy Path

{% endnote %}

### 软件接口

接口的精髓在于**形成通信或者数据交换的契约**，即**尽可能精炼的暴露有用的信息，而不暴露一些内部的细节信息**，这样保证不同模块之间的高度复用和低耦合，提升开发效率。

- User Interface
- Application Programming Interface

#### UI 接口

主要提供 GUI 操作的相关测试。

- 设计目标
- 约束属性

完全从用户的角度出发，不暴露技术细节！

#### API 接口

RESTAPI 调用：一套标准化的 API 接口实现

- URL
- 请求参数（一个字典）
- 返回 JSON 格式
- 得到响应示例（一个 JSON 格式）
- 存在调用约束

对于一个需求场景，可以把它转换为一个接口！

- When 的触发条件即提供了 API 接口的输入和调用条件
- Then 代表 API 接口的执行结果
- Given 的验证被封装在 API 接口的内部验证

### Example

```text
1. 用户登录
   1.1 登录界面
       1.1.1 首页通过点击“亲，请登录”按钮进入登录界面。
       1.1.2 界面包含手机号输入框、验证码输入框、获取验证码按钮、以及“登录”按钮。

   1.2 用户发起获取验证码请求
       1.2.1 校验用户输入的手机号码格式是否正确
           Scenario: 请求验证码时手机号格式无效
               Given 用户在**登录页面**
               When 用户输入一个无效的手机号（如 "123"）并点击“获取验证码”
               Then 系统不发送验证码 并且 页面提示“请输入正确的手机号码”

       1.2.2 生成验证码
           Scenario: 成功获取验证码
               Given 用户在**登录页面**
               When 用户输入一个格式正确的手机号并点击“获取验证码”
               Then 系统为该手机号生成一个6位验证码并打印在控制台
               And “获取验证码”按钮进入60秒倒计时且不可点击
               And 数据库记录手机号和验证码，有效期为60秒

   1.3 用户提交登录信息
       1.3.1 校验手机号是否已注册
           Scenario: 使用未注册的手机号登录
               Given 一个手机号未被注册
               And 用户在**登录页面**
               When 用户使用该未注册的手机号和正确的验证码点击“**登录**”
               Then 系统不让用户登录 并且 页面提示“该手机号未注册，请先完成注册”

       1.3.2 校验验证码是否正确
           Scenario: 使用错误的验证码登录
               Given 一个手机号已被注册
               And 用户在**登录页面**
               When 用户使用该手机号和错误的验证码点击“**登录**”
               Then 系统不让用户登录 并且 页面提示“验证码错误”
       
       1.3.3 系统完成登录流程
           Scenario: 成功登录
               Given 一个手机号已被注册
               And 用户在**登录页面**
               When 用户使用该手机号和正确的验证码点击“**登录**”
               Then 系统验证成功
               And 页面提示“登录成功”
               And 页面自动跳转到首页

2. 用户注册
   2.1 注册界面
       2.1.1 **首页通过点击“免费注册”按钮进入注册界面。**
       2.1.2 **界面包含手机号输入框、验证码输入框、获取验证码按钮、《淘贝用户协议》同意勾选框、以及“注册”按钮。**

   2.2 用户发起获取验证码请求
       2.2.1 校验用户输入的手机号码格式是否正确
           Scenario: 请求验证码时手机号格式无效
               Given 用户在**注册页面**
               When 用户输入一个无效的手机号（如 "123"）并点击“获取验证码”
               Then 系统不发送验证码 并且 页面提示“请输入正确的手机号码”

       2.2.2 生成验证码
           Scenario: 成功获取验证码
               Given 用户在**注册页面**
               When 用户输入一个格式正确的手机号并点击“获取验证码”
               Then 系统为该手机号生成一个6位验证码并打印在控制台
               And “获取验证码”按钮进入60秒倒计时且不可点击
               And 数据库记录手机号和验证码，有效期为60秒

   2.3 用户提交注册信息
       2.3.1 校验手机号是否已被注册
           Scenario: 使用已注册的手机号进行注册
               Given 一个手机号已被注册
               And 用户在**注册页面**
               When 用户使用该手机号和正确的验证码点击“**注册**”
               Then 系统不创建新用户 并且 页面提示“该手机号已注册，将直接为您登录”
               And 用户成功登录并跳转到首页

       2.3.2 校验用户是否同意用户协议
           Scenario: 未同意用户协议时注册按钮的状态
               Given 用户在**注册页面**输入了手机号和验证码
               When 用户未勾选“同意《淘贝用户协议》”复选框
               Then “**注册**”按钮为不可点击状态
               When 用户勾选“同意《淘贝用户协议》”复选框
               Then “**注册**”按钮变为可点击状态
       
       2.3.3 系统完成注册流程
           Scenario: 成功注册
               Given 用户在**注册页面**
               When 用户输入未注册的手机号和正确的验证码，勾选协议并点击“**注册**”
               Then 系统在数据库中创建新用户
               And 页面提示“注册成功”
               And 用户成功登录并自动跳转到首页
```

The source code for this example above has been open sourced into [This Repo](https://github.com/xiyuanyang-code/My-Typst-Note/tree/main/LectureNote/SoftwareDevelopment/src/behave_test/features).




