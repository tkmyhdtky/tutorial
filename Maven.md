# Maven

## 基础概念

### 仓库

- 仓库：存储资源，包含各种jar包
  - 本地仓库：自己电脑上存储资源都仓库，连接远程仓库获取资源
  - 远程仓库：费本机电脑上的仓库，为本地仓库提供资源
    - 中央仓库：Maven团队维护，存储所有资源都仓库
    - 私服：部门/公司范围内存储资源都仓库，从中央仓库获取资源
- 私服的作用：
  - 保存具有版权的资源，包含自主研发或购买的jar包
    - 中央仓库中的jar都是开源的，不能存储具有版权的资源
  - 一定范围内共享资源，仅对内部开发，不对外共享

### 坐标

maven中用于描述仓库中资源的位置

#### Maven坐标的组成

- groupid:当前Maven项目隶属组织名称(域名反写)
- artifactld:当前Maven项目名称
- version：当前项目版本号

#### Maven坐标的作用

使用唯一坐标，唯一性定位资源位置，通过该标识可以讲资源都识别与下载工作交由机器完成

### 远程仓库配置

## 依赖管理

### 依赖配置

指当前项目运行所需要的jar包，一个项目可以设置多个依赖

### 依赖传递

依赖具有传递性

- 直接依赖：在当前项目中通过依赖配置简历的依赖关系
- 间接依赖：被资源都资源如果依赖其他资源，当前项目间接依赖 其他资源

#### 传递冲突问题

- 路径优先：当依赖中出现相同的资源时，层级越深，优先级越低，层级越浅，优先级越高
- 声明优先：当资源在相同层级被依赖时，配置顺序靠前的覆盖配置顺序靠后的
- 特殊优先：当同级配置了相同资源都不同版本，后配置的覆盖先配置的

### 可选依赖

可选依赖对外隐藏当前所依赖的资源——不透明

### 排除依赖

主动断开依赖的资源，被排除的资源无需指定版本

### 依赖范围

- 依赖的jar默认情况可以在任何地方使用，可以通过scope标签设定器设定其作用范围
- 作用范围
  - 主程序范围有效(main文件夹范围内)
  - 测试程序范围有效(test文件夹范围内)
  - 是否参与打包(package文件夹范围内)

|     scope     | 主代码 | 测试代码 | 打包  |    范例     |
| :-----------: | :----: | :------: | :---: | :---------: |
| compile(默认) |   Y    |    Y     |   Y   |    log4j    |
|     test      |        |    Y     |       |    junit    |
|   provided    |   Y    |    Y     |       | servlet-api |
|    runtime    |        |          |   Y   |    jdbc     |

## 生命周期与插件

### 构建生命周期

- clean:清理工作
  - pre-clean:执行一些clean之前完成的工作
  - clean：移除上一次构建生成的文件
  - post-clean:执行一些需要在clean之后完成的工作
- default：核心工作
- site：产生报告，发布站点等
  - pre-site:执行一些需要在生成站点文档之前完成的工作
  - site:生成项目的站点文档
  - post-site：执行一些需要在生成站点文档之后完成的工作，并未部署做准备
  - 将生成的站点文档部署到特定的服务器上

### 插件

- 插件与生命周期内的阶段绑定，在执行到对应生命周期时执行对应的插件功能
- 默认Maven在各个生命周期上绑定有预设的功能
- 通过插件可以自定义其他功能

## 分模块开发与设计

- 模块中仅包含当前模块对应的功能类与配置文件
- Spring核心配置根据模块功能不同进行独立制作
- 当前模块所依赖的模块通过导入坐标的形式加入当前模块后才可以使用
- web.xml需要加载所有的Spring核心配置文件

## 聚合

聚合用于快速构建Maven工程，一次性构建多个项目/模块

- 制作方法
  - 创建一个空模块，打包类型定义类pom
  - 兴义当前模块进行构建操作时关联的其他模块名称
- 注意事项：参与聚合操作的模块最终执行顺序与模块间的依赖有关，与配置顺序无关

## 继承

通过继承可以实现在子工程中沿用父工程的配置

- 制作方式
  - 在子工程中声明其父工程坐标与对应位置

``` xml
  <parent>
    <groupId></groupId>
    <artifactId></artifactId>
    <version></version>
    <!--填写父工程文件-->
    <relativePath></relativePath>
  </parent>
```

### 继承和聚合的区别

- 作用
  - 聚合用于快速构建项目
  - 继承用于快速配置
- 相同
  - 继承和聚合的pom.xml文件打包方式均为pom，可以讲两种关系制作到同一个pom文件中
  - 聚合和继承均属于设计型模块，并无实际的模块内容
- 不同
  - 聚合是在当前模块中配置关系，可以感知到参与聚合的模块有哪些
  - 继承是在子模块中配置关系，父模块无法感知那些子模块继承自己

## 属性

### 自定义属性

- 作用：等同于变量，方便统一维护
- 定义格式

``` xml
  <properties>
    <spring.version>5.1.9.RELEASE</spring.version>
    <junit.version>5.1.9.RELEASE</junit.version>
  </properties>
```

- 调用格式

``` xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>org.springframework</artifactId>
  <version>org.springframework</version>
</dependency>
```

### 内置属性

- 作用：使用Maven内置属性，快速配置
- 调用格式

```js
  ${basedir}
  ${version}
```

### Setting 属性

- 作用：使用Maven配置文件setting.xml中的标签属性，用于动态配置
- 调用格式：

```${setting.localRepository}```

### Java系统属性和环境变量属性

- 作用：读取JAVA系统属性
- 调用格式

```js
${系统属性值} //JAVA系统属性
${env.环境变量值} //环境变量属性
```

- 系统属性查询方式

```maven
mvn help:system
```

## 资源配置

- 作用：在任意配置文件中加载pom文件中定义的属性
- 调用格式

```js
${属性名}
```

- 开启配置文件加载pom属性

```js
<!--配置资源文件对应信息-->
<resources>
  <resource>
  <!--设定配置文件对应的位置目录，支持使用属性动态设定路径-->
  <directory>${project.basedir}/对应目录</directory>
  <!--开启对配置文件的资源加载过滤-->
  <filtering>true</filtering>
  </resource>
</resources>
```

## 多环境开发配置

### 多环境配置

```js
<!-- 创建多环境 -->
<profiles>
  <!-- 定义具体环境：生产环境 -->
  <profile>
    <!-- 定义环境对应的唯一名称-->
    <id>pro_env</id>
    <!-- 定义环境中专用的属性值
    <properties>
      <jdbc.url></jdbc.url>
    </properties>
    <!-- 设置默认启动-->
    <activation>true</activation>
  </profile>
  <!-- 定义具体环境：开发环境-->
  <profile>
    <id>dev_env</id>
  </profile>
</profiles>
```

### 加载指定环境

- 作用：加载指定环境配置
- 调用格式

```mvn 指令 -P 环境定义id```

- 范例

```mvn install -P pro_env```

## 私服
