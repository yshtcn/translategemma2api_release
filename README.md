# TranslateGemma2API

将 [TranslateGemma](https://www.ollama.com/library/translategemma) 大模型封装为翻译 API 服务，兼容 [translate-api](https://github.com/jianchang512/translate-api) 接口格式，附带 Web 翻译页面。

## 下载

| 方式 | 地址 |
|------|------|
| Docker | [`docker pull yshtcn/translategemma2api`](https://hub.docker.com/r/yshtcn/translategemma2api) |
| Windows EXE | [GitHub Releases](https://github.com/yshtcn/translategemma2api_release/releases) |

## 前置条件

需要先安装 [Ollama](https://ollama.com/) 并拉取 TranslateGemma 模型：

```bash
ollama pull translategemma
```

> **提示：** 配置文件中的 `model_name` 必须与 `ollama list` 显示的模型名称**完全一致**（例如 `translategemma:latest` 或 `translategemma:12b`），否则会调用失败。

## 快速开始

### Docker

```bash
docker run -d -p 9911:9911 -v ./data:/data yshtcn/translategemma2api
```

### Windows EXE

下载 `translategemma2api.exe`，双击运行即可。

---

首次启动后程序会自动在 `data/` 目录生成 `config.json` 配置文件。修改配置后**重启服务**即可生效：

```json
{
    "ollama_base_url": "http://localhost:11434",
    "model_name": "translategemma",
    "host": "0.0.0.0",
    "port": 9911,
    "secret": "",
    "request_timeout": 120
}
```

| 字段 | 说明 |
|------|------|
| `ollama_base_url` | Ollama 服务地址。Docker 中默认改为 `http://host.docker.internal:11434` |
| `model_name` | 模型名称，**必须与 `ollama list` 完全一致** |
| `host` / `port` | 监听地址和端口，默认 `0.0.0.0:9911` |
| `secret` | API 密钥，留空表示无需密钥 |
| `request_timeout` | Ollama 请求超时（秒） |

## 使用

- **Web 翻译页面：** `http://localhost:9911/`（设置了密钥时访问 `/?sk=你的密钥`）
- **翻译 API：** `GET /transapi/?text=你好&source_language=zh&target_language=en`
- **语言列表：** `GET /languages`

API 返回格式：

```json
{"code": 0, "msg": "ok", "text": "Hello"}
```


