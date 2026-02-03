# configx

configx 是一个功能强大且灵活的 Go 语言配置管理库，支持多种配置读取方式，包括本地文件、环境变量、结构体标签等。

## 功能特性

- **多源配置读取**：支持从本地文件、环境变量、结构体标签等多种来源读取配置
- **灵活的配置器模式**：通过实现 Configurator 接口自定义配置加载逻辑
- **多种文件格式支持**：支持 JSON、YAML、TOML 等常见配置文件格式
- **链式读取机制**：按优先级顺序尝试不同的配置读取器
- **易于扩展**：可轻松添加新的配置读取器

## 安装

```bash
go get github.com/go-xuan/configx
```

## 快速开始

### 基本用法

```go
package main

import (
	"fmt"

	"github.com/go-xuan/configx"
)

func main() {
	if err := configx.LoadConfigurator(&Config{}); err != nil {
		panic(err)
	}
}

// 定义配置结构体
type Config struct {
	Port     int    `yaml:"port" default:"8080"`
	Database string `yaml:"database" default:"localhost:5432"`
	Debug    bool   `yaml:"debug" default:"false"`
}

func (c *Config) Readers() []configx.Reader {
	return []configx.Reader{
		configx.NewFileReader("config.yaml"), // 从文件读取
		configx.NewEnvReader(),               // 从环境变量读取
		configx.NewTagReader(),               // 从结构体标签读取默认值
	}
}

func (c *Config) Valid() bool {
	return c.Port > 0
}

func (c *Config) Execute() error {
	fmt.Printf("Server will start on port %d\n", c.Port)
	return nil
}


```

### 支持的配置读取器

#### 文件读取器 (FileReader)
从本地文件读取配置，支持 JSON、YAML、TOML 等格式。

```go
reader := configx.NewFileReader("config.yaml")
```

#### 环境变量读取器 (EnvReader)
从环境变量读取配置值。

```go
reader := configx.NewEnvReader()
```

#### 标签读取器 (TagReader)
从结构体字段标签读取默认值。

```go
reader := configx.NewTagReader()
```

## 高级用法

### 自定义文件读取路径

```go
fileReader := configx.NewFileReader("config.json")
fileReader.Anchor("./configs") // 设置配置文件目录
```

### 配置验证

实现 `Valid()` 方法对配置进行验证:

```go
func (c *Config) Valid() bool {
	return c.Port > 0 && c.Port < 65536
}
```

## API 文档

### 主要接口

#### Configurator 接口
- `Readers()` - 返回配置读取器列表
- `Valid()` - 验证配置是否有效
- `Execute()` - 执行配置相关逻辑

#### Reader 接口
- `Read(v any)` - 从数据源读取配置
- `Anchor(anchor string)` - 设置配置文件锚点
- `Location()` - 返回配置数据源位置

### 主要函数

- `LoadConfigurator(configurator Configurator) error` - 加载并执行配置器
- `LoadConfiguratorPanic(configurators ...Configurator)` - 加载配置器，失败时 panic
- `ReadConfigurator(configurator Configurator) (string, error)` - 仅读取配置

## 使用场景

- 微服务配置管理
- 多环境部署 (开发、测试、生产)
- 配置热更新
- 不同配置源优先级管理

## 贡献

欢迎提交 Issue 和 Pull Request 来改进此项目。

## 许可证

MIT License