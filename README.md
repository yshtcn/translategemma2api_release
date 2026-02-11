<img width="1217" height="606" alt="image" src="https://github.com/user-attachments/assets/97d77326-c581-43af-9114-3f34bd7f8853" />

# TranslateGemma2API

将 [TranslateGemma](https://www.ollama.com/library/translategemma) 大模型封装为翻译 API 服务，兼容 [translate-api](https://github.com/jianchang512/translate-api) 接口格式，附带 Web 翻译页面。

支持**文本翻译**、**图片翻译**和 **PDF 翻译**，内置**自动语言检测**。

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
    "ollama_secret": "",
    "model_name": "translategemma:latest",
    "host": "0.0.0.0",
    "port": 9911,
    "secret": "",
    "request_timeout": 120,
    "ocr_model": {
        "ollama_base_url": "",
        "ollama_secret": "",
        "model_name": "glm-ocr:latest",
        "model_prompt": "Text Recognition",
        "request_timeout": 120,
        "default_enabled": true
    },
    "language_detect_prompt": "You are an expert language detector.\n...\n{TEXT}",
    "image_language_detect_prompt": "Detect the primary language of text in this image.\n..."
}
```

| 字段 | 说明 |
|------|------|
| `ollama_base_url` | Ollama 服务地址。Docker 中默认改为 `http://host.docker.internal:11434` |
| `ollama_secret` | Ollama API 密钥（Bearer Token），留空表示无需认证 |
| `model_name` | 翻译模型名称，**必须与 `ollama list` 完全一致** |
| `host` / `port` | 监听地址和端口，默认 `0.0.0.0:9911` |
| `secret` | 翻译服务的访问密钥，留空表示无需密钥 |
| `request_timeout` | Ollama 请求超时（秒） |
| `ocr_model` | OCR 前置模型配置（用于图片/PDF 翻译） |
| `ocr_model.ollama_base_url` | OCR 模型的 Ollama 地址，留空则使用主地址 |
| `ocr_model.ollama_secret` | OCR 模型的 Ollama 密钥，留空则使用主密钥 |
| `ocr_model.model_name` | OCR 模型名称，默认 `glm-ocr:latest` |
| `ocr_model.model_prompt` | OCR 识别提示词，默认 `Text Recognition` |
| `ocr_model.default_enabled` | 网页端 OCR 前置是否默认勾选（`true`/`false`） |
| `language_detect_prompt` | 文本语言检测提示词，`{TEXT}` 为占位符 |
| `image_language_detect_prompt` | 图片语言检测提示词 |

## 使用

- **Web 翻译页面：** `http://localhost:9911/`（设置了密钥时访问 `/?sk=你的密钥`）
- **API 文档：** `http://localhost:9911/docs`

### 文本翻译 API

```
GET /transapi/?text=你好&source_language=zh&target_language=en
```

也支持 POST 请求（JSON 或 Form 表单）。

### 图片翻译 API

```
POST /transapi/image/
Content-Type: multipart/form-data

参数：image（图片文件）, source_language, target_language, secret（可选）, use_ocr（可选，true/false）
```

也支持通过 JSON 提交 `image_base64` 字段。

### PDF 翻译

通过 Web 页面上传 PDF 文件，系统会自动拆分为逐页图片，提供左右对照的翻译浏览器，支持翻页按需翻译。

### 语言检测 API

```
POST /api/detect-language/
Content-Type: application/json

{"text": "Hello world", "secret": ""}
```

返回 `{"code": 0, "detected_code": "en", "detected_name": "English", "detected_tg_code": "en"}`

图片语言检测：

```
POST /api/detect-language/image/
Content-Type: multipart/form-data

参数：image（图片文件）, secret（可选）
```

### OCR 独立接口

```
POST /api/ocr/image/
Content-Type: multipart/form-data

参数：image（图片文件）, secret（可选）
```

返回 `{"code": 0, "text": "OCR 识别的文字"}`

### 语言列表

```
GET /languages
```

### 返回格式

```json
{"code": 0, "msg": "ok", "text": "Hello"}
```

| code | 含义 |
|------|------|
| 0 | 成功 |
| 1 | 无权访问（密钥错误） |
| 2 | 参数缺失 |
| 3 | 不支持的语言 |
| 4 | 翻译结果为空 |
| 5 | 服务错误 |
