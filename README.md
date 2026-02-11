## v1.1.0 - 图片翻译、PDF 翻译、OCR 前置

### 新功能

- **图片翻译 API** (`POST /transapi/image/`)：上传图片进行翻译，支持 multipart 和 base64 两种方式
- **PDF 翻译**：上传 PDF 自动拆分为逐页图片，左右对照浏览器支持翻页按需翻译
- **OCR 前置识别**：可配置独立的 OCR 模型（如 glm-ocr）先识别图片文字再翻译，提升复杂排版翻译质量
- **API 文档页面** (`/docs`)：包含所有 API 端点说明、参数、状态码和配置文件说明
- **Ollama API 密钥认证**：支持为 Ollama 服务配置 Bearer Token 认证（`ollama_secret`）
- **OCR 模型独立配置**：OCR 模型可使用不同的 Ollama 实例和密钥
- **OCR 默认勾选配置**：`ocr_model.default_enabled` 控制网页端 OCR 复选框的默认状态
- **OCR 可用性检测**：未配置 OCR 模型时，网页端复选框自动灰色禁用

### Web 页面

- 新增图片翻译选项卡：支持点击上传、拖拽、粘贴图片
- 新增 PDF 翻译选项卡：上传后跳转到翻译浏览器页面
- 图片和 PDF 翻译均支持 OCR 前置开关
- 页面 header 增加 API 文档链接

### 改进

- 翻译模型默认改为 `translategemma:latest`
- 图片翻译提示词简化，提升 4b 模型翻译质量
- PyMuPDF 顶层 import + 启动日志显示 Python 路径和 PDF 支持状态
- EXE 打包使用 Python 辅助脚本精确检测 PyMuPDF 原生二进制

### 依赖

- 新增 Pillow >= 10.0（图片处理）
- 新增 PyMuPDF >= 1.24（PDF 拆分）
